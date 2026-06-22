# Set 22

| #   | Question                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you render a default “No data available” message?](#question-1-how-do-you-render-a-default-no-data-available-message)                                              |
| 2   | [How do you use Fragment shorthand syntax?](#question-2-how-do-you-use-fragment-shorthand-syntax)                                                                          |
| 3   | [How do you handle onClick events for dynamically generated buttons?](#question-3-how-do-you-handle-onclick-events-for-dynamically-generated-buttons)                      |
| 4   | [How do you implement a “toggle visibility” component?](#question-4-how-do-you-implement-a-toggle-visibility-component)                                                    |
| 5   | [How do you implement a simple slider using state?](#question-5-how-do-you-implement-a-simple-slider-using-state)                                                          |
| 6   | [How do you implement a counter with step increments?](#question-6-how-do-you-implement-a-counter-with-step-increments)                                                    |
| 7   | [How do you render elements conditionally using ternary operators in JSX?](#question-7-how-do-you-render-elements-conditionally-using-ternary-operators-in-jsx)            |
| 8   | [How do you combine multiple state variables into a single object?](#question-8-how-do-you-combine-multiple-state-variables-into-a-single-object)                          |
| 9   | [How do you render HTML from strings safely using dangerouslySetInnerHTML?](#question-9-how-do-you-render-html-from-strings-safely-using-dangerouslysetinnerhtml)          |
| 10  | [How do you implement a simple tabbed interface?](#question-10-how-do-you-implement-a-simple-tabbed-interface)                                                             |
| 11  | [How do you implement text truncation with “read more” functionality?](#question-11-how-do-you-implement-text-truncation-with-read-more-functionality)                     |
| 12  | [How do you update nested array objects in state?](#question-12-how-do-you-update-nested-array-objects-in-state)                                                           |
| 13  | [How do you implement simple form validation for required fields?](#question-13-how-do-you-implement-simple-form-validation-for-required-fields)                           |
| 14  | [How do you implement an input that converts text to uppercase automatically?](#question-14-how-do-you-implement-an-input-that-converts-text-to-uppercase-automatically)   |
| 15  | [How do you create a reusable button component with custom styles?](#question-15-how-do-you-create-a-reusable-button-component-with-custom-styles)                         |
| 16  | [How do you implement a custom hook for form input validation?](#question-16-how-do-you-implement-a-custom-hook-for-form-input-validation)                                 |
| 17  | [How do you implement a responsive navigation bar with dropdowns?](#question-17-how-do-you-implement-a-responsive-navigation-bar-with-dropdowns)                           |
| 18  | [How do you implement a “scroll to section” feature?](#question-18-how-do-you-implement-a-scroll-to-section-feature)                                                       |
| 19  | [How do you handle multiple dependent API calls in useEffect?](#question-19-how-do-you-handle-multiple-dependent-api-calls-in-useeffect)                                   |
| 20  | [How do you implement infinite scrolling with useRef and IntersectionObserver?](#question-20-how-do-you-implement-infinite-scrolling-with-useref-and-intersectionobserver) |

## Question 1. How do you render a default “No data available” message?

# Short answer

Render a fallback UI conditionally when your data is empty.

The most common pattern is:

```tsx
{
  items.length === 0 ? (
    <p>No data available.</p>
  ) : (
    items.map((item) => <Item key={item.id} item={item} />)
  );
}
```

---

# Explanation

In React, you typically render a **fallback state** whenever the fetched or computed data is empty. This improves the user experience by clearly communicating that there is currently nothing to display instead of rendering a blank page.

A production-ready component usually handles **four UI states**:

1. **Loading** – Data is being fetched.
2. **Error** – Request failed.
3. **Empty** – Request succeeded, but no records exist.
4. **Success** – Display the data.

Example flow:

```
Loading → Error? → Empty? → Data
```

Instead of directly checking `items.length`, always ensure the value exists:

```tsx
if (loading) return <Spinner />;
if (error) return <ErrorMessage />;
if (!items?.length) return <EmptyState />;

return <ItemList items={items} />;
```

This makes the component easier to maintain and avoids nested conditional rendering.

React 18's automatic batching doesn't change this pattern—it simply makes multiple state updates (e.g., `setLoading(false)` and `setItems(data)`) more efficient.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users] = useState<User[]>([]);

  return (
    <div>
      <h2>Users</h2>

      {users.length === 0 ? (
        <p>No data available.</p>
      ) : (
        <ul>
          {users.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

A reusable approach:

```tsx
function EmptyState() {
  return <p>No data available.</p>;
}

function UserList({ users }: { users: User[] }) {
  if (!users.length) {
    return <EmptyState />;
  }

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

# Tooling & Setup

- Prefer **Vite** over Create React App (CRA), as CRA is deprecated.
- For SSR, SEO, or hybrid rendering, use **Next.js App Router**.
- Vite uses native **ES Modules (ESM)** during development for fast startup and HMR.
- Modern bundlers like **Vite (Rollup)** and **Turbopack** optimize code splitting and tree shaking automatically.

---

# Performance

Even though rendering an empty state is inexpensive, follow these practices for larger applications:

- Render a dedicated `<EmptyState />` component to keep rendering logic clean.
- Use **React Profiler** to identify unnecessary renders.
- Memoize expensive child components with `React.memo`.
- Use `useMemo` and `useCallback` only when profiling shows a benefit.
- Code-split large pages with `React.lazy()` and `Suspense`.
- Cache server data using libraries like **TanStack Query** or **SWR** to reduce unnecessary refetches.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("shows empty message", () => {
  render(<App />);
  expect(screen.getByText("No data available.")).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Log API failures to monitoring tools (e.g., Sentry) while showing a friendly empty or error state.
- Wrap pages with an Error Boundary to catch rendering errors.
- Consider SSR (Next.js) for SEO-sensitive pages and CSR for highly interactive dashboards.
- Keep bundles small with lazy loading and route-level code splitting.
- Deploy static Vite apps to a CDN for fast global delivery.

---

# Pitfalls

- **Don't assume the array exists.** Use `!items?.length` instead of `items.length` if data may be `undefined`.
- **Differentiate loading from empty.** Don't show "No data available" while a request is still in progress.
- **Create reusable empty-state components** instead of duplicating the same message throughout the application.

## Question 2. How do you use Fragment shorthand syntax?

## Question 3. How do you handle onClick events for dynamically generated buttons?

## Question 4. How do you implement a “toggle visibility” component?

## Question 5. How do you implement a simple slider using state?

## Question 6. How do you implement a counter with step increments?

## Question 7. How do you render elements conditionally using ternary operators in JSX?

## Question 8. How do you combine multiple state variables into a single object?

## Question 9. How do you render HTML from strings safely using dangerouslySetInnerHTML?

## Question 10. How do you implement a simple tabbed interface?

## Question 11. How do you implement text truncation with “read more” functionality?

## Question 12. How do you update nested array objects in state?

## Question 13. How do you implement simple form validation for required fields?

## Question 14. How do you implement an input that converts text to uppercase automatically?

## Question 15. How do you create a reusable button component with custom styles?

## Question 16. How do you implement a custom hook for form input validation?

## Question 17. How do you implement a responsive navigation bar with dropdowns?

## Question 18. How do you implement a “scroll to section” feature?

## Question 19. How do you handle multiple dependent API calls in useEffect?

## Question 20. How do you implement infinite scrolling with useRef and IntersectionObserver?
