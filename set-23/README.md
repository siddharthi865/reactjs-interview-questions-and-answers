# Set 23

| #   | Question                                                                                                                                                                |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement a dynamic table with sorting and filtering?](#question-1-how-do-you-implement-a-dynamic-table-with-sorting-and-filtering)                         |
| 2   | [How do you implement a search input with debounce in a functional component?](#question-2-how-do-you-implement-a-search-input-with-debounce-in-a-functional-component) |
| 3   | [How do you implement a reusable modal component with animations?](#question-3-how-do-you-implement-a-reusable-modal-component-with-animations)                         |
| 4   | [How do you implement a multi-step wizard form with progress tracking?](#question-4-how-do-you-implement-a-multi-step-wizard-form-with-progress-tracking)               |
| 5   | [How do you implement a global loader for API requests?](#question-5-how-do-you-implement-a-global-loader-for-api-requests)                                             |
| 6   | [How do you implement optimistic UI updates when deleting a list item?](#question-6-how-do-you-implement-optimistic-ui-updates-when-deleting-a-list-item)               |
| 7   | [How do you implement a “copy to clipboard” button in React?](#question-7-how-do-you-implement-a-copy-to-clipboard-button-in-react)                                     |
| 8   | [How do you handle uncontrolled components in a reusable form component?](#question-8-how-do-you-handle-uncontrolled-components-in-a-reusable-form-component)           |
| 9   | [How do you implement theme switching using context and hooks?](#question-9-how-do-you-implement-theme-switching-using-context-and-hooks)                               |
| 10  | [How do you implement a responsive image gallery with modals?](#question-10-how-do-you-implement-a-responsive-image-gallery-with-modals)                                |
| 11  | [How do you implement a real-time search filter?](#question-11-how-do-you-implement-a-real-time-search-filter)                                                          |
| 12  | [How do you implement sticky headers in tables or sections?](#question-12-how-do-you-implement-sticky-headers-in-tables-or-sections)                                    |
| 13  | [How do you implement client-side caching for API responses?](#question-13-how-do-you-implement-client-side-caching-for-api-responses)                                  |
| 14  | [How do you implement a rating system with half-stars?](#question-14-how-do-you-implement-a-rating-system-with-half-stars)                                              |
| 15  | [How do you implement a file upload component with preview?](#question-15-how-do-you-implement-a-file-upload-component-with-preview)                                    |
| 16  | [How do you implement dynamic tabs with add/remove functionality?](#question-16-how-do-you-implement-dynamic-tabs-with-addremove-functionality)                         |
| 17  | [How do you implement keyboard navigation for interactive components?](#question-17-how-do-you-implement-keyboard-navigation-for-interactive-components)                |
| 18  | [How do you implement virtualized lists for thousands of items?](#question-18-how-do-you-implement-virtualized-lists-for-thousands-of-items)                            |
| 19  | [How do you implement reusable form input components with validation?](#question-19-how-do-you-implement-reusable-form-input-components-with-validation)                |
| 20  | [How do you implement a slide-in sidebar menu?](#question-20-how-do-you-implement-a-slide-in-sidebar-menu)                                                              |

## Question 1. How do you implement a dynamic table with sorting and filtering?

# Dynamic Table with Sorting and Filtering in React

## Short answer

A dynamic table with sorting and filtering is typically implemented by:

- Storing the original dataset in state.
- Keeping **sorting** and **filtering** state separately.
- Using `useMemo` to derive the visible rows efficiently.
- Rendering rows based on the computed dataset.
- For large datasets, combine this with **virtualization**, **server-side pagination**, or **server-side sorting/filtering**.

---

# Explanation

A production-ready React table should separate concerns:

- **Original Data** → Immutable source.
- **Filter State** → Search text, dropdown filters, date filters, etc.
- **Sort State** → Column + direction.
- **Derived Data** → Computed with `useMemo`.
- **UI** → Pure rendering component.

```
Original Data
      │
      ▼
 Filtering
      │
      ▼
 Sorting
      │
      ▼
 Paginate (optional)
      │
      ▼
 Render Table
```

This architecture avoids mutating the original data and makes the table predictable.

### Sorting

Maintain:

```ts
const [sortConfig, setSortConfig] = useState({
  key: "name",
  direction: "asc",
});
```

When a column header is clicked:

- same column → toggle direction
- different column → ascending

Example comparator:

```ts
a[key] > b[key] ? 1 : -1;
```

For strings:

```ts
localeCompare();
```

For numbers:

```ts
a.age - b.age;
```

---

### Filtering

Maintain:

```ts
const [search, setSearch] = useState("");
```

Then:

```ts
rows.filter(...)
```

Example:

```ts
row.name.toLowerCase().includes(search.toLowerCase());
```

Multiple filters are simply chained:

```ts
rows
.filter(...)
.filter(...)
.filter(...)
```

---

### Derived State with `useMemo`

Avoid recalculating every render.

```tsx
const filteredAndSorted = useMemo(() => {
    let result = [...users];

    if (search) {
        result = result.filter(user =>
            user.name.toLowerCase().includes(search.toLowerCase())
        );
    }

    result.sort(...);

    return result;
}, [users, search, sortConfig]);
```

This recalculates only when dependencies change.

---

### React 18 Considerations

React 18 introduced:

- Automatic batching
- Concurrent rendering
- `useTransition`
- `useDeferredValue`

For expensive filtering:

```tsx
const deferredSearch = useDeferredValue(search);
```

or

```tsx
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setSearch(value);
});
```

This keeps typing responsive while filtering thousands of rows.

---

### Client-side vs Server-side Tables

| Client-side       | Server-side       |
| ----------------- | ----------------- |
| Small datasets    | Huge datasets     |
| Fast              | Scalable          |
| Sort in browser   | Sort in API       |
| Filter in browser | Filter in backend |
| No network calls  | API per action    |

Interview tip:

> For datasets beyond a few thousand rows, prefer **server-side sorting/filtering/pagination**.

---

## Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-table -- --template react-ts
cd my-table
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { useMemo, useState } from "react";

type User = {
  id: number;
  name: string;
  age: number;
};

const users: User[] = [
  { id: 1, name: "Alice", age: 28 },
  { id: 2, name: "Bob", age: 34 },
  { id: 3, name: "Charlie", age: 22 },
];

export default function App() {
  const [search, setSearch] = useState("");
  const [sortAsc, setSortAsc] = useState(true);

  const visibleUsers = useMemo(() => {
    return [...users]
      .filter((u) => u.name.toLowerCase().includes(search.toLowerCase()))
      .sort((a, b) =>
        sortAsc ? a.name.localeCompare(b.name) : b.name.localeCompare(a.name),
      );
  }, [search, sortAsc]);

  return (
    <div style={{ padding: 20 }}>
      <input
        placeholder="Search..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
      />

      <table border={1} cellPadding={8}>
        <thead>
          <tr>
            <th
              style={{ cursor: "pointer" }}
              onClick={() => setSortAsc((s) => !s)}
            >
              Name {sortAsc ? "▲" : "▼"}
            </th>
            <th>Age</th>
          </tr>
        </thead>

        <tbody>
          {visibleUsers.map((user) => (
            <tr key={user.id}>
              <td>{user.name}</td>
              <td>{user.age}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```

This example demonstrates:

- Type-safe data model.
- Client-side filtering.
- Click-to-toggle sorting.
- `useMemo` for derived state.
- Immutable updates.

---

# Tooling & Setup

- **Avoid Create React App (CRA)**; it is deprecated.
- Prefer **Vite** for fast startup, native ESM support, and efficient hot module replacement during development.
- For applications requiring SSR, SEO, or React Server Components, consider **Next.js App Router**. **Remix** is another solid option for data-driven routing.
- **ESM vs CommonJS:** Modern React projects should use **ES Modules (ESM)**. Vite is ESM-first, enabling faster dependency handling and tree-shaking.
- Bundlers such as **Vite (Rollup for production)** and **Turbopack** (in newer Next.js workflows) provide faster builds and optimized bundles.

---

# Performance

For production tables:

- Memoize filtered/sorted data using `useMemo`.
- Memoize row components with `React.memo` if rows are expensive to render.
- Memoize event handlers with `useCallback` when passing callbacks deeply.
- Use **windowing/virtualization** (e.g., `react-window` or `@tanstack/react-virtual`) for thousands of rows.
- Prefer **server-side pagination, sorting, and filtering** for very large datasets.
- Profile render performance using **React DevTools Profiler** to identify unnecessary re-renders.
- Code-split large table features with `React.lazy` and `Suspense` if they are not needed on initial load.
- Cache server responses using libraries such as **TanStack Query** or **SWR** to reduce redundant network requests.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example assertions:

- Typing in the search box filters rows correctly.
- Clicking a sortable header toggles ascending/descending order.
- Empty filters display the expected fallback message.
- Accessibility checks ensure headers and controls are keyboard accessible.

For end-to-end testing, use **Playwright** to verify sorting and filtering flows in a real browser.

---

# Ops & Deployment

- Add **Error Boundaries** around complex table components to isolate rendering failures.
- Use structured logging for failed data fetches and slow API responses.
- For SSR, pre-render initial table data where SEO or faster first paint is important; otherwise CSR is often sufficient for internal dashboards.
- Keep bundles small by lazy-loading advanced table functionality (exporting, column configuration, etc.).
- Deploy optimized static assets to a CDN and enable HTTP caching. For API-backed tables, configure appropriate cache headers or leverage client-side caching libraries to reduce latency.

---

# Pitfalls

- **Don't mutate** the original array—always copy (`[...data]`) before sorting.
- **Avoid expensive computations** directly in the render body; derive filtered/sorted data with `useMemo`.
- **Use stable keys** (such as unique IDs) instead of array indices to prevent incorrect row reconciliation during sorting.

## Question 2. How do you implement a search input with debounce in a functional component?

## Question 3. How do you implement a reusable modal component with animations?

## Question 4. How do you implement a multi-step wizard form with progress tracking?

## Question 5. How do you implement a global loader for API requests?

## Question 6. How do you implement optimistic UI updates when deleting a list item?

## Question 7. How do you implement a “copy to clipboard” button in React?

## Question 8. How do you handle uncontrolled components in a reusable form component?

## Question 9. How do you implement theme switching using context and hooks?

## Question 10. How do you implement a responsive image gallery with modals?

## Question 11. How do you implement a real-time search filter?

## Question 12. How do you implement sticky headers in tables or sections?

## Question 13. How do you implement client-side caching for API responses?

## Question 14. How do you implement a rating system with half-stars?

## Question 15. How do you implement a file upload component with preview?

## Question 16. How do you implement dynamic tabs with add/remove functionality?

## Question 17. How do you implement keyboard navigation for interactive components?

## Question 18. How do you implement virtualized lists for thousands of items?

## Question 19. How do you implement reusable form input components with validation?

## Question 20. How do you implement a slide-in sidebar menu?
