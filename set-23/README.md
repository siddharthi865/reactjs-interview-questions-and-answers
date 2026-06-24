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

# How do you implement a search input with debounce in a functional component?

## Short answer

A debounced search delays executing the search logic until the user stops typing for a specified time (e.g., 300–500ms). In React functional components, the common approach is to:

- Store the immediate input value in state.
- Use `useEffect` with `setTimeout` and `clearTimeout` to update a debounced value.
- Trigger API calls or expensive filtering when the **debounced value** changes instead of on every keystroke.

This reduces unnecessary renders, API requests, and improves user experience.

---

# Explanation

Without debouncing:

```text
User types: React

R → API Call
Re → API Call
Rea → API Call
Reac → API Call
React → API Call
```

With a **300ms debounce**:

```text
User types: React

R
Re
Rea
Reac
React
      ↓ 300ms pause
   Single API Call
```

### How it works

There are two pieces of state:

```tsx
const [search, setSearch] = useState("");
const [debouncedSearch, setDebouncedSearch] = useState("");
```

Flow:

```text
Input Change
      │
      ▼
setSearch()
      │
      ▼
Start Timer (300ms)
      │
      ▼
User types again?
      │
 Yes ─────────► Cancel previous timer
      │
 No
      ▼
setDebouncedSearch()
      │
      ▼
API Call / Filtering
```

The cleanup function inside `useEffect` clears the previous timeout, ensuring only the latest input triggers the update.

### Why use debounce?

- Prevents excessive API requests.
- Reduces expensive filtering or computations.
- Improves responsiveness on slower networks.
- Provides a smoother typing experience.

### React 18 considerations

React 18 introduced features that complement (but do not replace) debouncing:

- **Automatic batching** reduces unnecessary re-renders when multiple state updates occur together.
- **`useDeferredValue`** keeps the UI responsive while rendering expensive filtered results, but it **does not prevent network requests**.
- **`useTransition`** marks expensive UI updates as non-urgent.

For API searches, **debounce is still the preferred solution**. You can combine it with `useTransition` or `useDeferredValue` for even smoother rendering.

---

# Example

### Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest debounce-search -- --template react-ts
cd debounce-search
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useEffect, useState } from "react";

export default function App() {
  const [search, setSearch] = useState("");
  const [debouncedSearch, setDebouncedSearch] = useState("");

  // Debounce input
  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedSearch(search);
    }, 500);

    return () => clearTimeout(timer);
  }, [search]);

  // Simulate API call
  useEffect(() => {
    if (!debouncedSearch) return;

    console.log("Searching:", debouncedSearch);

    // Example:
    // fetch(`/api/search?q=${encodeURIComponent(debouncedSearch)}`)
    //   .then(res => res.json())
    //   .then(data => console.log(data));
  }, [debouncedSearch]);

  return (
    <div style={{ padding: 20 }}>
      <input
        type="text"
        placeholder="Search..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
      />

      <p>Typing: {search}</p>
      <p>Debounced: {debouncedSearch}</p>
    </div>
  );
}
```

This example demonstrates:

- Functional component with Hooks.
- Debouncing using `useEffect`.
- Proper timeout cleanup.
- Triggering side effects only after the debounce interval.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** since it is deprecated.
- Prefer **Vite** for React applications because of its fast startup, HMR, and ESM-first architecture.
- Use **Next.js App Router** when building SSR/SEO-focused applications or when leveraging React Server Components.
- **ESM vs CommonJS:** Modern React tooling uses **ES Modules (ESM)** for efficient tree-shaking and faster builds. Vite natively supports ESM.
- For larger applications, organize debounce logic into a reusable custom hook such as `useDebounce`.

---

# Performance

- Debounce API calls (typically 300–500ms).
- Use `useMemo` for expensive client-side filtering.
- Memoize callbacks with `useCallback` if passing them to memoized children.
- Wrap expensive result lists with `React.memo` to minimize unnecessary re-renders.
- Use **`useDeferredValue`** to keep rendering responsive while filtering large datasets.
- For long result lists, combine debounce with virtualization (`react-window` or `@tanstack/react-virtual`).
- Use the **React DevTools Profiler** to identify render bottlenecks.
- Cache search results using **TanStack Query** or **SWR** to avoid repeated requests for the same query.

---

# Testing

Use **Vitest** and **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test ideas:

- Verify no search executes immediately while typing.
- Advance fake timers and confirm the search runs once after the debounce interval.
- Ensure rapid typing cancels previous timers.
- Mock API calls and verify only the final query is requested.

For end-to-end testing, use **Playwright** to validate real user typing behavior and network interactions.

---

# Ops & Deployment

- Cancel in-flight requests with `AbortController` when a newer search starts to avoid race conditions.
- Log slow search responses and API failures for observability.
- Use an **Error Boundary** for rendering search results, while handling fetch errors locally in the component or data layer.
- If using SSR, debounce only on the client; server-render the initial page if needed for SEO.
- Serve static assets via a CDN and cache API responses where appropriate.

---

# Pitfalls

- **Forgetting to clear the timeout**, causing multiple delayed executions.
- **Debouncing the input state itself** instead of debouncing the side effect, making the input feel laggy.
- **Ignoring stale responses** from older API requests; cancel them or ignore outdated results.

## Question 3. How do you implement a reusable modal component with animations?

# How do you implement a reusable modal component with animations?

## Short answer

A reusable animated modal should:

- Be **controlled** via `isOpen` and `onClose` props.
- Render through a **React Portal** so it sits outside the normal DOM hierarchy.
- Animate entry/exit using CSS transitions or an animation library (e.g., Framer Motion).
- Support **Escape key**, **backdrop click**, **focus management**, and **accessibility** (`role="dialog"`, `aria-modal="true"`).

---

# Explanation

A production-ready modal separates **state**, **presentation**, and **behavior**.

```text
Parent Component
       │
       ▼
isOpen + onClose
       │
       ▼
Reusable Modal
 ├── Portal
 ├── Backdrop
 ├── Animation
 ├── Keyboard Events
 ├── Focus Trap
 └── Content
```

### Component API

```tsx
<Modal isOpen={isOpen} onClose={() => setIsOpen(false)} title="Delete Item">
  <p>Are you sure?</p>
</Modal>
```

This makes the component reusable for confirmations, forms, image previews, etc.

### Why use React Portal?

Without a portal, the modal may be clipped by parent containers with `overflow: hidden` or affected by stacking contexts.

Using `createPortal()` renders the modal into a dedicated DOM node (e.g., `#modal-root` or `document.body`), ensuring proper layering.

### Animation strategies

Common approaches:

- **CSS transitions** (simple, lightweight).
- **Framer Motion** (rich animations, gestures).
- **React Transition Group** (transition lifecycle management).

Typical animation:

- Backdrop fades in/out.
- Modal scales from 95% → 100%.
- Opacity transitions from 0 → 1.

### React 18 considerations

- Automatic batching minimizes unnecessary renders when opening/closing.
- Concurrent rendering keeps UI responsive during state updates.
- Animation state should remain local to the modal; avoid unnecessary global state.

---

# Example

### Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest animated-modal -- --template react-ts
cd animated-modal
npm install
npm install framer-motion
npm run dev
```

### `Modal.tsx`

```tsx
import { ReactNode, useEffect } from "react";
import { createPortal } from "react-dom";
import { AnimatePresence, motion } from "framer-motion";

type ModalProps = {
  isOpen: boolean;
  title?: string;
  children: ReactNode;
  onClose: () => void;
};

export function Modal({ isOpen, title, children, onClose }: ModalProps) {
  useEffect(() => {
    const handler = (e: KeyboardEvent) => {
      if (e.key === "Escape") onClose();
    };

    window.addEventListener("keydown", handler);
    return () => window.removeEventListener("keydown", handler);
  }, [onClose]);

  return createPortal(
    <AnimatePresence>
      {isOpen && (
        <motion.div
          className="backdrop"
          initial={{ opacity: 0 }}
          animate={{ opacity: 1 }}
          exit={{ opacity: 0 }}
          onClick={onClose}
        >
          <motion.div
            className="modal"
            initial={{ scale: 0.95, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            exit={{ scale: 0.95, opacity: 0 }}
            transition={{ duration: 0.2 }}
            onClick={(e) => e.stopPropagation()}
            role="dialog"
            aria-modal="true"
            aria-labelledby={title ? "modal-title" : undefined}
          >
            {title && <h2 id="modal-title">{title}</h2>}
            {children}
          </motion.div>
        </motion.div>
      )}
    </AnimatePresence>,
    document.body,
  );
}
```

### Usage

```tsx
import { useState } from "react";
import { Modal } from "./Modal";

export default function App() {
  const [open, setOpen] = useState(false);

  return (
    <>
      <button onClick={() => setOpen(true)}>Open Modal</button>

      <Modal isOpen={open} title="Confirmation" onClose={() => setOpen(false)}>
        <p>Delete this item?</p>
        <button onClick={() => setOpen(false)}>Close</button>
      </Modal>
    </>
  );
}
```

This example demonstrates:

- Controlled component pattern.
- React Portal.
- Entry/exit animations.
- Escape key handling.
- Backdrop click to close.
- Accessible dialog semantics.

---

# Tooling & Setup

- **Avoid Create React App (CRA)**; it is deprecated.
- Prefer **Vite** for client-side React applications due to fast HMR and ESM support.
- Use **Next.js App Router** if the modal is part of an SSR application. Mark interactive modal components with `"use client"` because they rely on browser APIs and event handlers.
- **ESM vs CommonJS:** Use ESM imports/exports. Vite and modern bundlers optimize ESM for tree-shaking and faster builds.
- For advanced animations, **Framer Motion** provides declarative APIs and integrates well with React.

---

# Performance

- Render the modal only when `isOpen` is `true`.
- Memoize heavy child components with `React.memo` if their props are stable.
- Memoize callbacks passed to the modal using `useCallback`.
- Lazy-load infrequently used modal content with `React.lazy` and `Suspense`.
- Use the **React DevTools Profiler** to verify opening/closing doesn't trigger unnecessary parent renders.
- For data shown inside the modal, use caching libraries such as **TanStack Query** or **SWR** to avoid redundant fetches.

---

# Testing

Use **Vitest** with **React Testing Library**.

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Test cases:

- Modal renders only when `isOpen` is `true`.
- Clicking the backdrop calls `onClose`.
- Pressing **Escape** closes the modal.
- Clicking inside the modal does not close it.
- Verify `role="dialog"` and `aria-modal="true"` for accessibility.

Use **Playwright** for end-to-end tests to validate focus behavior, keyboard navigation, and animation timing.

---

# Ops & Deployment

- Implement a proper **focus trap** (e.g., `focus-trap-react`) and restore focus to the triggering element when the modal closes.
- Lock background scrolling (`document.body.style.overflow = "hidden"`) while the modal is open.
- Wrap modal content with an **Error Boundary** if it contains complex UI.
- Monitor runtime errors and interaction metrics through your logging platform.
- Code-split large modal content to reduce the initial bundle size, and serve static assets through a CDN.

---

# Pitfalls

- **Not using a Portal**, causing clipping or z-index issues.
- **Ignoring accessibility**, such as missing focus management, `role="dialog"`, or keyboard support.
- **Unmounting immediately**, which prevents exit animations; use an animation library or transition lifecycle to animate before removal.

## Question 4. How do you implement a multi-step wizard form with progress tracking?

# How do you implement a multi-step wizard form with progress tracking?

## Short answer

A multi-step wizard form is implemented by:

- Keeping the **current step** in state.
- Storing **all form data** in a single shared state (or reducer).
- Rendering one step at a time.
- Tracking progress using the current step index.
- Validating each step before allowing navigation.
- Preserving state when moving between steps.

For large applications, use **React Hook Form** with **Zod/Yup** for validation and keep each step as an independent, reusable component.

---

# Explanation

A production-ready wizard separates navigation, state, validation, and presentation.

```text
               Wizard
                  │
        ┌─────────┴─────────┐
        │                   │
   Current Step        Form Data
        │                   │
        ▼                   ▼
  Step Components      Shared State
        │
        ▼
 Validation
        │
        ▼
 Next / Previous
```

### Component architecture

```
src/
 ├── components/
 │    ├── Wizard.tsx
 │    ├── ProgressBar.tsx
 │    ├── Step1.tsx
 │    ├── Step2.tsx
 │    └── Step3.tsx
 ├── hooks/
 │    └── useWizard.ts
 └── App.tsx
```

Each step should:

- Receive only the data it needs.
- Update shared state through callbacks.
- Contain step-specific validation.
- Be reusable independently.

---

### Managing state

Instead of storing state inside every step:

```tsx
const [formData, setFormData] = useState({
  name: "",
  email: "",
  address: "",
});
```

or for larger forms:

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

This prevents losing entered values when users move between steps.

---

### Navigation

```tsx
Next
Previous
Go To Step
Submit
```

Example:

```tsx
setCurrentStep((step) => step + 1);
```

Disable Next until validation succeeds.

---

### Progress Tracking

Progress can be calculated as:

```ts
progress = (currentStep / totalSteps) * 100;
```

Example:

```
Step 1 / 4
██████░░░░░░ 25%

Step 2 / 4
██████████░░ 50%

Step 3 / 4
██████████████ 75%
```

A progress bar improves user experience by showing completion status.

---

### Validation

Validate only the current step.

Example:

Step 1

- Name required
- Email required

Step 2

- Address required

Step 3

- Review

Avoid validating the whole form until submission.

Libraries:

- React Hook Form
- Zod
- Yup

---

### React 18 considerations

React 18 provides:

- Automatic batching
- Concurrent rendering
- `useTransition`

For expensive validation:

```tsx
startTransition(() => {
  setCurrentStep(next);
});
```

This keeps navigation responsive while large components render.

---

# Example

### Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest wizard-form -- --template react-ts
cd wizard-form
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

type FormData = {
  name: string;
  email: string;
};

const totalSteps = 2;

export default function App() {
  const [step, setStep] = useState(1);

  const [form, setForm] = useState<FormData>({
    name: "",
    email: "",
  });

  const progress = (step / totalSteps) * 100;

  return (
    <div style={{ padding: 20, maxWidth: 400 }}>
      <h2>Multi-Step Wizard</h2>

      <progress value={progress} max={100} />
      <p>{Math.round(progress)}%</p>

      {step === 1 && (
        <>
          <label>Name</label>
          <input
            value={form.name}
            onChange={(e) => setForm({ ...form, name: e.target.value })}
          />
        </>
      )}

      {step === 2 && (
        <>
          <label>Email</label>
          <input
            value={form.email}
            onChange={(e) => setForm({ ...form, email: e.target.value })}
          />
        </>
      )}

      <div style={{ marginTop: 20 }}>
        <button disabled={step === 1} onClick={() => setStep((s) => s - 1)}>
          Previous
        </button>

        {step < totalSteps ? (
          <button onClick={() => setStep((s) => s + 1)}>Next</button>
        ) : (
          <button onClick={() => alert(JSON.stringify(form))}>Submit</button>
        )}
      </div>
    </div>
  );
}
```

This example demonstrates:

- Shared form state.
- Step navigation.
- Progress tracking.
- State persistence across steps.
- Conditional rendering of step components.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** since it is deprecated.
- Prefer **Vite** for fast development, native ESM support, and excellent TypeScript integration.
- For production applications, combine **React Hook Form** with **Zod** or **Yup** for schema-based validation and minimal re-renders.
- If the wizard is part of an SSR application, **Next.js App Router** is a strong choice. Interactive wizard components should be Client Components (`"use client"`).
- **ESM vs CommonJS:** Modern React projects should use **ES Modules (ESM)** for better tree-shaking and faster builds.

---

# Performance

- Store all form data in a single source of truth (`useState` or `useReducer`) to simplify updates.
- Use `React.memo` for step components that don't depend on frequently changing props.
- Memoize navigation handlers with `useCallback` when passing them to memoized children.
- Use `useMemo` for expensive derived values (e.g., dynamic summaries or computed validation state).
- Lazy-load rarely visited or heavy steps with `React.lazy` and `Suspense`.
- Profile the wizard using the **React DevTools Profiler** to ensure navigating between steps doesn't trigger unnecessary re-renders.
- Cache server-side validation or lookup data (e.g., address autocomplete) with **TanStack Query** or **SWR**.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Test scenarios:

- Initial step renders correctly.
- "Next" advances only after valid input.
- "Previous" retains entered values.
- Progress indicator updates correctly.
- Final submission contains data from all steps.

For end-to-end testing, use **Playwright** to verify the complete user flow, including validation, navigation, and submission.

---

# Ops & Deployment

- Persist unfinished wizard data in `localStorage` or IndexedDB for long forms, and synchronize with the server if needed.
- Wrap complex steps with **Error Boundaries** to isolate rendering failures.
- Log validation failures and submission errors to monitor user friction.
- Consider SSR for the initial page when SEO matters; most authenticated or dashboard wizards are well-suited to CSR.
- Optimize bundle size by code-splitting optional steps and loading them on demand. Deploy static assets through a CDN for faster delivery.

---

# Pitfalls

- **Keeping state inside each step**, causing data loss when components unmount.
- **Validating the entire form on every step**, leading to poor UX; validate only the active step.
- **Using deeply nested state updates** instead of a reducer or form library for large, complex wizards.

## Question 5. How do you implement a global loader for API requests?

# How do you implement a global loader for API requests?

## Short answer

A global loader is typically implemented by:

- Managing a **global loading state** using **React Context**, Redux, or a data-fetching library.
- Incrementing a counter when an API request starts and decrementing it when the request finishes.
- Displaying a full-screen loader whenever the number of active requests is greater than zero.
- Using **Axios interceptors** or centralized `fetch` wrappers so individual components don't manually manage loading state.

This approach works correctly even when **multiple API requests run concurrently**.

---

# Explanation

A production-ready solution should **not** rely on a single boolean:

❌ Bad

```tsx
const [loading, setLoading] = useState(false);
```

If two requests start simultaneously:

```text
Request A starts → loading = true
Request B starts → loading = true

Request A finishes → loading = false ❌
Request B still running
```

The loader disappears too early.

Instead, maintain an **active request counter**.

```text
API Request Starts
        │
        ▼
 activeRequests++
        │
        ▼
loading = activeRequests > 0
        │
        ▼
API finishes
        │
        ▼
activeRequests--
```

---

### Architecture

```text
               App
                │
      LoadingProvider
                │
        Loading Context
                │
      ┌─────────┴─────────┐
      │                   │
 Component A        Component B
      │                   │
      └──── API Requests ─┘
                │
         Global Loader
```

The provider exposes:

- `showLoader()`
- `hideLoader()`
- `loading`

or simply manages the request counter internally.

---

### Axios interceptor approach

```text
Request
   │
   ▼
Increment Counter
   │
   ▼
Axios Request
   │
   ▼
Response/Error
   │
   ▼
Decrement Counter
```

Every request automatically updates the loader without component-specific logic.

---

### React 18 considerations

React 18 improves responsiveness with:

- Automatic batching
- Concurrent rendering
- `useTransition`

However, **API loading state still requires explicit management**. React doesn't automatically track network requests.

For applications using **TanStack Query** or **SWR**, leverage their built-in global fetching indicators instead of reinventing the solution.

---

# Example

### Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest global-loader -- --template react-ts
cd global-loader
npm install
npm install axios
npm run dev
```

### `LoadingContext.tsx`

```tsx
import { createContext, useContext, useMemo, useState, ReactNode } from "react";

type LoadingContextType = {
  loading: boolean;
  start: () => void;
  stop: () => void;
};

const LoadingContext = createContext<LoadingContextType | null>(null);

export function LoadingProvider({ children }: { children: ReactNode }) {
  const [count, setCount] = useState(0);

  const value = useMemo(
    () => ({
      loading: count > 0,
      start: () => setCount((c) => c + 1),
      stop: () => setCount((c) => Math.max(0, c - 1)),
    }),
    [count],
  );

  return (
    <LoadingContext.Provider value={value}>
      {children}
      {count > 0 && <div className="loader">Loading...</div>}
    </LoadingContext.Provider>
  );
}

export function useLoading() {
  const context = useContext(LoadingContext);
  if (!context) {
    throw new Error("useLoading must be used inside LoadingProvider");
  }
  return context;
}
```

### Axios interceptor

```tsx
import axios from "axios";

export const api = axios.create({
  baseURL: "/api",
});

export function registerInterceptors(start: () => void, stop: () => void) {
  api.interceptors.request.use((config) => {
    start();
    return config;
  });

  api.interceptors.response.use(
    (response) => {
      stop();
      return response;
    },
    (error) => {
      stop();
      return Promise.reject(error);
    },
  );
}
```

This example demonstrates:

- Global loading state.
- Concurrent request handling.
- Automatic loader updates through interceptors.
- No loading logic inside individual components.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Prefer **Vite** for fast development, HMR, and native ESM support.
- Use **Next.js App Router** when SSR or React Server Components are required. Client-side loading overlays belong in Client Components (`"use client"`).
- **ESM vs CommonJS:** Use ES Modules (ESM) for modern React projects. Vite and current bundlers optimize ESM for tree-shaking and faster builds.
- For server state, prefer **TanStack Query** or **SWR**, which expose global fetching status (`useIsFetching` in TanStack Query) and simplify cache management.

---

# Performance

- Track the **number of active requests** instead of a boolean to handle concurrent API calls correctly.
- Memoize the context value with `useMemo` to reduce unnecessary consumer re-renders.
- Memoize loader components with `React.memo` if they render complex UI.
- Use `useCallback` for context actions if they're passed independently.
- Avoid showing a loader for very short requests (e.g., under 150–200 ms) to reduce UI flicker.
- Use the **React DevTools Profiler** to verify that loader state changes don't cause excessive re-renders.
- Cache API responses using **TanStack Query** or **SWR** to minimize repeated loading states.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Test scenarios:

- Loader appears when a request starts.
- Loader disappears only after **all** concurrent requests finish.
- Loader hides after failed requests as well as successful ones.
- Interceptors correctly increment and decrement the active request counter.

For end-to-end testing, use **Playwright** to verify the loading overlay during real network activity.

---

# Ops & Deployment

- Always decrement the request counter in both **success** and **error** paths to avoid a stuck loader.
- Handle request cancellation (`AbortController` or Axios cancellation) and ensure canceled requests also update the counter.
- Log slow requests and API failures for observability.
- Wrap major application sections with **Error Boundaries** to isolate rendering errors; API errors should be handled separately.
- For SSR applications, distinguish server-rendering loading states from client-side request loaders.
- Optimize assets and deploy through a CDN; cache API responses appropriately to reduce unnecessary loading indicators.

---

# Pitfalls

- **Using a single boolean** instead of an active request counter, causing incorrect behavior with concurrent requests.
- **Forgetting to decrement** the counter on error or cancellation, leaving the loader visible indefinitely.
- **Managing loading state in every component**, leading to duplicated logic instead of using centralized interceptors or a shared data-fetching library.

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
