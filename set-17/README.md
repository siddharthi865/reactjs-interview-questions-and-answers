# Set 17

| S.No. | Question                                                                                                                                                                 |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you toggle CSS classes dynamically in React?](#question-1-how-do-you-toggle-css-classes-dynamically-in-react)                                                    |
| 2.    | [How do you render a "loading" spinner while data is being fetched?](#question-2-how-do-you-render-a-loading-spinner-while-data-is-being-fetched)                        |
| 3.    | [How do you create a reusable input component?](#question-3-how-do-you-create-a-reusable-input-component)                                                                |
| 4.    | [How do you handle simple inline validation for an input field?](#question-4-how-do-you-handle-simple-inline-validation-for-an-input-field)                              |
| 5.    | [How do you implement a "show password" toggle?](#question-5-how-do-you-implement-a-show-password-toggle)                                                                |
| 6.    | [How do you display the length of a text input dynamically?](#question-6-how-do-you-display-the-length-of-a-text-input-dynamically)                                      |
| 7.    | [How do you clear input fields after submission?](#question-7-how-do-you-clear-input-fields-after-submission)                                                            |
| 8.    | [How do you implement simple pagination in React?](#question-8-how-do-you-implement-simple-pagination-in-react)                                                          |
| 9.    | [How do you conditionally render multiple elements inside a fragment?](#question-9-how-do-you-conditionally-render-multiple-elements-inside-a-fragment)                  |
| 10.   | [How do you use ReactDOM.hydrate for server-rendered content?](#question-10-how-do-you-use-reactdomhydrate-for-server-rendered-content)                                  |
| 11.   | [How do you handle focus and blur events in inputs?](#question-11-how-do-you-handle-focus-and-blur-events-in-inputs)                                                     |
| 12.   | [How do you update nested objects in state without mutating them?](#question-12-how-do-you-update-nested-objects-in-state-without-mutating-them)                         |
| 13.   | [How do you implement a simple rating component (e.g., stars)?](#question-13-how-do-you-implement-a-simple-rating-component-eg-stars)                                    |
| 14.   | [How do you add tooltips to buttons in React?](#question-14-how-do-you-add-tooltips-to-buttons-in-react)                                                                 |
| 15.   | [How do you prevent memory leaks in class components?](#question-15-how-do-you-prevent-memory-leaks-in-class-components)                                                 |
| 16.   | [How do you implement a countdown timer with hooks and cleanup?](#question-16-how-do-you-implement-a-countdown-timer-with-hooks-and-cleanup)                             |
| 17.   | [How do you implement a search filter that updates results as the user types?](#question-17-how-do-you-implement-a-search-filter-that-updates-results-as-the-user-types) |
| 18.   | [How do you implement dynamic class names based on multiple conditions?](#question-18-how-do-you-implement-dynamic-class-names-based-on-multiple-conditions)             |
| 19.   | [How do you handle API pagination in React?](#question-19-how-do-you-handle-api-pagination-in-react)                                                                     |
| 20.   | [How do you implement a collapsible sidebar menu?](#question-20-how-do-you-implement-a-collapsible-sidebar-menu)                                                         |

## Question 1. How do you toggle CSS classes dynamically in React?

# How do you toggle CSS classes dynamically in React?

## Short answer

In React, CSS classes are toggled dynamically by computing the `className` prop based on component state or props. The most common approaches are:

- Conditional (ternary) expressions
- Logical `&&` expressions
- Template literals
- Utility libraries like `clsx` or `classnames` (recommended for complex conditions)

React re-evaluates the `className` during each render whenever state or props change.

---

# Explanation

React does not manipulate the DOM classes directly like:

```js
element.classList.add("active");
```

Instead, React follows a **declarative approach**:

1. Store UI state.
2. Render classes based on that state.
3. React updates the DOM efficiently during reconciliation.

Example flow:

```
Button Click
      ↓
State changes
      ↓
Component re-renders
      ↓
className recalculated
      ↓
React updates DOM
```

This keeps UI predictable and avoids manual DOM manipulation.

### Common approaches

### 1. Ternary Operator (Most Common)

```jsx
className={isActive ? "active" : ""}
```

Suitable when toggling between two values.

---

### 2. Template Literals

Useful when combining fixed and conditional classes.

```jsx
className={`btn ${isActive ? "active" : ""}`}
```

Produces:

```
btn
```

or

```
btn active
```

---

### 3. Logical AND

Useful when only adding a class.

```jsx
className={`card ${selected && "selected"}`}
```

Although common, many teams prefer `clsx` because it avoids accidental `"false"` values in some patterns.

---

### 4. clsx (Recommended)

For multiple conditions, `clsx` keeps code clean.

```tsx
className={clsx(
  "btn",
  isPrimary && "btn-primary",
  disabled && "btn-disabled",
  loading && "loading"
)}
```

Much easier to maintain than nested ternaries.

---

## React Rendering Behavior (React 18)

When state changes:

```tsx
setIsActive(true);
```

React 18:

- Automatically batches multiple state updates.
- Schedules rendering efficiently with concurrent rendering capabilities.
- Recomputes `className` during render.
- Updates only the changed DOM attributes.

Example:

```tsx
setIsActive(true);
setLoading(false);
```

React performs **one render** instead of two (automatic batching).

---

## Component Architecture

For reusable components:

```tsx
<Button active disabled />
```

The component decides its own classes.

```tsx
function Button({ active, disabled }) {
  return (
    <button
      className={`btn ${active ? "active" : ""} ${disabled ? "disabled" : ""}`}
    />
  );
}
```

This keeps styling encapsulated and reusable.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

Install `clsx`:

```bash
npm install clsx
```

**`App.tsx`**

```tsx
import { useState } from "react";
import clsx from "clsx";
import "./App.css";

export default function App() {
  const [active, setActive] = useState(false);

  return (
    <div>
      <button
        className={clsx("btn", {
          active,
        })}
        onClick={() => setActive((prev) => !prev)}
      >
        {active ? "Active" : "Inactive"}
      </button>
    </div>
  );
}
```

**`App.css`**

```css
.btn {
  padding: 12px 20px;
  background: gray;
  color: white;
  border: none;
  cursor: pointer;
}

.active {
  background: royalblue;
}
```

Clicking the button toggles the `active` class without any direct DOM manipulation.

---

# Tooling & Setup

- **Use Vite** for React projects because it offers fast startup, instant hot module replacement (HMR), and native ES module development.
- Avoid **Create React App (CRA)** since it is deprecated.
- For SSR or hybrid rendering, prefer **Next.js App Router**; for nested routing and data loading, **Remix** is another strong option.
- Modern React tooling is ESM-first. Vite bundles with Rollup for production and serves native ES modules during development, resulting in fast rebuilds and efficient tree-shaking.

---

# Performance

Dynamic class toggling is inexpensive because React only updates the changed `className` attribute.

For larger applications:

- Use **React Profiler** to identify unnecessary re-renders.
- Memoize expensive child components with `React.memo`.
- Use `useCallback` when passing event handlers to memoized children.
- Use `useMemo` only for expensive class computations (rare).
- Split large routes/components using `React.lazy` and `Suspense`.
- Use caching libraries such as TanStack Query for server state to reduce unnecessary UI updates.

---

# Testing

For unit and integration testing, use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("toggles active class", () => {
  render(<App />);

  const button = screen.getByRole("button");

  fireEvent.click(button);

  expect(button).toHaveClass("active");
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Log UI state transitions for debugging in development; integrate structured logging or monitoring in production.
- Choose SSR (e.g., Next.js) when SEO or faster first paint is important; CSR with Vite is ideal for many dashboards and internal apps.
- Minimize bundle size with code splitting, tree-shaking, and lazy loading.
- Deploy static Vite builds behind a CDN for efficient asset delivery.

---

# Pitfalls

- **Avoid manually changing classes with `element.classList`** in React components; it can conflict with React's virtual DOM.
- **Prefer `clsx` or `classnames`** over deeply nested ternary expressions for readability and maintainability.
- **Use functional state updates** (`setState(prev => !prev)`) when toggling booleans to avoid stale state issues.

## Question 2. How do you render a "loading" spinner while data is being fetched?

# How do you render a "loading" spinner while data is being fetched?

## Short answer

Render a loading spinner by maintaining a loading state (e.g., `isLoading`) and conditionally rendering either the spinner or the fetched content. Set `isLoading` to `true` before starting the request and `false` when it completes (or fails).

```tsx
return isLoading ? <Spinner /> : <UserList users={users} />;
```

---

# Explanation

In React, asynchronous data fetching typically involves three UI states:

1. **Loading** – Request is in progress.
2. **Success** – Data has been loaded.
3. **Error** – Request failed.

A common pattern is:

```text
Component mounts
       ↓
isLoading = true
       ↓
Fetch data
       ↓
Success → Render data
Failure → Render error
       ↓
isLoading = false
```

Instead of manually manipulating the DOM, React declaratively renders different UI based on state.

### Basic flow

```tsx
const [data, setData] = useState([]);
const [isLoading, setIsLoading] = useState(true);
const [error, setError] = useState<string | null>(null);
```

During fetch:

```tsx
setIsLoading(true);

try {
  const result = await fetch(...);
  setData(...);
} catch {
  setError("Something went wrong");
} finally {
  setIsLoading(false);
}
```

Using `finally` ensures the spinner disappears whether the request succeeds or fails.

---

## React 18 Rendering Behavior

With React 18:

- **Automatic batching** groups multiple state updates into a single render.
- Concurrent rendering keeps the UI responsive while updates are scheduled.
- The component re-renders automatically when `isLoading`, `data`, or `error` changes.

Example:

```tsx
setData(users);
setIsLoading(false);
```

These updates are batched into one render.

---

## Component Architecture

Separate loading UI into reusable components.

```tsx
<Page>
  <LoadingSpinner />
</Page>
```

instead of repeating:

```tsx
<div>Loading...</div>
```

throughout the application.

Example:

```tsx
function LoadingSpinner() {
  return <div className="spinner">Loading...</div>;
}
```

Large applications often standardize loading indicators for consistency.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { useEffect, useState } from "react";
import "./App.css";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users, setUsers] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchUsers() {
      try {
        setIsLoading(true);

        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users",
        );

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data: User[] = await response.json();
        setUsers(data);
      } catch (err) {
        setError((err as Error).message);
      } finally {
        setIsLoading(false);
      }
    }

    fetchUsers();
  }, []);

  if (isLoading) {
    return <div className="spinner">Loading...</div>;
  }

  if (error) {
    return <p>{error}</p>;
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

**`App.css`**

```css
.spinner {
  font-size: 20px;
  padding: 20px;
  text-align: center;
}
```

This example displays a loading message while fetching data, then renders the user list or an error message.

---

# Tooling & Setup

- **Vite** is recommended for new React projects due to its fast dev server, HMR, and optimized production builds.
- Avoid **Create React App (CRA)** because it is deprecated.
- Use **Next.js App Router** when you need SSR, streaming, or Server Components.
- Vite is ESM-first, providing native module loading during development and Rollup-based production bundling.

---

# Performance

For production applications:

- Prefer **TanStack Query** or **SWR** over manual `useEffect` fetching. They provide built-in loading states, caching, retries, background refetching, and request deduplication.
- Use **React Profiler** to identify unnecessary re-renders.
- Memoize expensive child components with `React.memo`.
- Use `useCallback` for callbacks passed to memoized components.
- Lazy load routes/components with `React.lazy` and `Suspense`.
- Cache API responses to reduce repeated network requests.

Example with TanStack Query:

```tsx
const { data, isLoading, error } = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("shows loading spinner initially", () => {
  render(<App />);
  expect(screen.getByText(/loading/i)).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright** to verify loading indicators appear and disappear correctly during network requests.

---

# Ops & Deployment

- Handle loading, success, and error states consistently across the application.
- Use **Error Boundaries** for rendering errors (they do not catch async fetch errors, so handle those separately).
- For SSR frameworks like Next.js, use server-side data fetching where appropriate to reduce client-side loading states and improve SEO.
- Keep bundles small with code splitting and deploy static assets through a CDN.

---

# Pitfalls

- **Always reset `isLoading` in a `finally` block**, otherwise the spinner may never disappear after an error.
- **Don't render only a spinner indefinitely**; include proper error and empty-state UIs.
- **Avoid race conditions** by canceling or ignoring outdated requests when components unmount or when multiple fetches can overlap (e.g., using `AbortController`).

## Question 3. How do you create a reusable input component?

## Question 4. How do you handle simple inline validation for an input field?

## Question 5. How do you implement a "show password" toggle?

## Question 6. How do you display the length of a text input dynamically?

## Question 7. How do you clear input fields after submission?

## Question 8. How do you implement simple pagination in React?

## Question 9. How do you conditionally render multiple elements inside a fragment?

## Question 10. How do you use ReactDOM.hydrate for server-rendered content?

## Question 11. How do you handle focus and blur events in inputs?

## Question 12. How do you update nested objects in state without mutating them?

## Question 13. How do you implement a simple rating component (e.g., stars)?

## Question 14. How do you add tooltips to buttons in React?

## Question 15. How do you prevent memory leaks in class components?

## Question 16. How do you implement a countdown timer with hooks and cleanup?

## Question 17. How do you implement a search filter that updates results as the user types?

## Question 18. How do you implement dynamic class names based on multiple conditions?

## Question 19. How do you handle API pagination in React?

## Question 20. How do you implement a collapsible sidebar menu?
