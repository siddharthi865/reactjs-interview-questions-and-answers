# Set 12

| S.No. | Question                                                                                                                                                              |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you handle click events on dynamically created elements?](#question-1-how-do-you-handle-click-events-on-dynamically-created-elements)                         |
| 2.    | [How do you handle multiple input fields with one onChange handler?](#question-2-how-do-you-handle-multiple-input-fields-with-one-onchange-handler)                   |
| 3.    | [How do you use `map()` to render components in JSX?](#question-3-how-do-you-use-map-to-render-components-in-jsx)                                                     |
| 4.    | [How do you conditionally render `null` in React?](#question-4-how-do-you-conditionally-render-null-in-react)                                                         |
| 5.    | [How do you prevent re-rendering when props haven't changed?](#question-5-how-do-you-prevent-re-rendering-when-props-havent-changed)                                  |
| 6.    | [What are the rules of JSX syntax?](#question-6-what-are-the-rules-of-jsx-syntax)                                                                                     |
| 7.    | [How do you render components conditionally without using ternary operators?](#question-7-how-do-you-render-components-conditionally-without-using-ternary-operators) |
| 8.    | [How do you pass an event argument to a handler function?](#question-8-how-do-you-pass-an-event-argument-to-a-handler-function)                                       |
| 9.    | [How do you implement a simple counter using state?](#question-9-how-do-you-implement-a-simple-counter-using-state)                                                   |
| 10.   | [How do you render default content when a prop is missing?](#question-10-how-do-you-render-default-content-when-a-prop-is-missing)                                    |
| 11.   | [How do you use logical AND (`&&`) for conditional rendering?](#question-11-how-do-you-use-logical-and--for-conditional-rendering)                                    |
| 12.   | [How do you implement simple form validation in React?](#question-12-how-do-you-implement-simple-form-validation-in-react)                                            |
| 13.   | [How do you create a functional component that accepts children?](#question-13-how-do-you-create-a-functional-component-that-accepts-children)                        |
| 14.   | [How do you remove an item from a list in React state?](#question-14-how-do-you-remove-an-item-from-a-list-in-react-state)                                            |
| 15.   | [How do you implement a "show/hide" toggle component?](#question-15-how-do-you-implement-a-showhide-toggle-component)                                                 |
| 16.   | [How do you create a custom hook for fetching data?](#question-16-how-do-you-create-a-custom-hook-for-fetching-data)                                                  |
| 17.   | [How do you handle cleanup when a component unmounts?](#question-17-how-do-you-handle-cleanup-when-a-component-unmounts)                                              |
| 18.   | [How do you memoize an expensive calculation in a component?](#question-18-how-do-you-memoize-an-expensive-calculation-in-a-component)                                |
| 19.   | [How do you prevent unnecessary `useEffect` calls?](#question-19-how-do-you-prevent-unnecessary-useeffect-calls)                                                      |
| 20.   | [How do you implement a "scroll to top" button in React?](#question-20-how-do-you-implement-a-scroll-to-top-button-in-react)                                          |

## Question 1. How do you handle click events on dynamically created elements?

# Short answer

In React, you typically **attach `onClick` directly to each rendered element**, even if the elements are created dynamically using `map()`. React's synthetic event system efficiently handles these events through event delegation internally, so you don't need manual event delegation in most cases.

---

# Explanation

This question often tests whether you understand **React's event system** versus traditional DOM event handling.

### 1. Dynamic elements in React

React components frequently render lists dynamically:

```tsx
items.map((item) => (
  <button key={item.id} onClick={() => handleClick(item.id)}>
    {item.name}
  </button>
));
```

Although hundreds of buttons may be rendered, React doesn't attach hundreds of native DOM listeners in the same way vanilla JavaScript often would.

React uses a **Synthetic Event** system that delegates events efficiently (React 17+ delegates events to the React root container instead of the `document`).

---

### 2. Event delegation

In vanilla JavaScript:

```javascript
document.addEventListener("click", (e) => {
  if (e.target.matches(".item")) {
    console.log("clicked");
  }
});
```

This is called **event delegation**.

React already implements an optimized delegation mechanism internally, so you simply write:

```tsx
<button onClick={handleClick}>Click</button>
```

No manual delegation is needed.

---

### 3. Passing parameters

Dynamic elements usually require knowing which item was clicked.

Preferred approach:

```tsx
const handleClick = (id: number) => {
  console.log(id);
};

<button onClick={() => handleClick(item.id)}>
```

or

```tsx
<button
  data-id={item.id}
  onClick={(e) =>
    handleClick(Number((e.currentTarget as HTMLButtonElement).dataset.id))
  }
/>
```

Using `currentTarget` is safer than `target` because `target` could be a nested child.

---

### 4. Rendering behavior (React 18)

When a click handler updates multiple pieces of state:

```tsx
setCount((c) => c + 1);
setLoading(true);
```

React 18 performs **automatic batching**, producing only **one render**.

Click events are considered **discrete events**, so React gives them high priority to keep the UI responsive.

---

### 5. Component architecture

Instead of attaching one global handler, keep event handlers close to the component that owns the state.

Example:

```
TodoList
 ├── TodoItem
 ├── TodoItem
 └── TodoItem
```

Each `TodoItem` exposes:

```tsx
<TodoItem todo={todo} onSelect={handleSelect} />
```

This keeps components reusable and testable.

---

### 6. State management trade-offs

For small components:

- local `useState`

For shared interactions:

- Context
- Redux Toolkit
- Zustand
- Jotai

Avoid storing UI-only click state globally unless multiple components need it.

---

# Example

**Scaffold using Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

```tsx
import { useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [selected, setSelected] = useState<number | null>(null);

  const users: User[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
    { id: 3, name: "Charlie" },
  ];

  const handleClick = (id: number) => {
    setSelected(id);
  };

  return (
    <>
      <h2>Users</h2>

      {users.map((user) => (
        <button
          key={user.id}
          onClick={() => handleClick(user.id)}
          style={{ display: "block", marginBottom: 8 }}
        >
          {user.name}
        </button>
      ))}

      <p>Selected ID: {selected}</p>
    </>
  );
}
```

This demonstrates:

- dynamically rendered elements
- parameterized click handlers
- React's synthetic events
- React 18 automatic batching compatibility

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript for fast startup, HMR, and modern ESM support.
- **Avoid Create React App (CRA):** It is deprecated; use Vite, Next.js, or Remix for new projects.
- **ESM vs CommonJS:** Vite uses native **ES Modules (ESM)** during development for faster module loading. CommonJS is mainly encountered in older Node.js ecosystems.
- **Bundlers:** Vite uses **esbuild** for dependency pre-bundling and **Rollup** for production builds. Next.js uses Turbopack (development) and webpack/Turbopack depending on configuration.
- **Dev server:** Run `npm run dev` to start Vite's fast development server with Hot Module Replacement (HMR).

---

# Performance

- React already optimizes event handling using **event delegation**, so avoid manually adding listeners to individual DOM nodes outside React.
- Use **React DevTools Profiler** to identify unnecessary re-renders after click events.
- Memoize expensive child components with `React.memo` and stabilize callback identities with `useCallback` when passing handlers deep into the component tree.
- Use `useMemo` for expensive derived values that depend on click-driven state.
- Split large feature modules with `React.lazy` and `Suspense` to reduce initial bundle size.
- Use data-fetching libraries (e.g., React Query/TanStack Query) for caching and background updates rather than manually managing cached click-triggered requests.

---

# Testing

For unit and integration tests, use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

test("selects a user when clicked", async () => {
  render(<App />);

  await userEvent.click(screen.getByText("Alice"));

  expect(screen.getByText(/Selected ID: 1/i)).toBeInTheDocument();
});
```

For end-to-end testing, consider **Playwright**.

---

# Ops & Deployment

- Use **Error Boundaries** to prevent the entire UI from crashing due to rendering errors (note that they do not catch errors thrown inside event handlers; handle those with `try/catch` or error reporting).
- Log click-related failures to monitoring services such as Sentry or Datadog for production diagnostics.
- Choose **CSR** for highly interactive applications and **SSR/Server Components** (e.g., Next.js App Router) when SEO or faster initial rendering is important.
- Keep bundles small through route-based code splitting and lazy loading, and serve static assets via a CDN or edge network for low-latency delivery.

---

# Pitfalls

- Avoid using array indices as `key` values for dynamic lists because they can cause incorrect event associations after reordering.
- Avoid creating deeply nested inline callbacks when they cause unnecessary re-renders in memoized child components; use `useCallback` where it provides measurable benefit.
- Do not attach native DOM event listeners (`addEventListener`) to React-managed elements unless you have a specific integration need; always clean them up in `useEffect`.

## Question 2. How do you handle multiple input fields with one onChange handler?

# Short answer

Use a **single `onChange` handler** that identifies the input by its `name` attribute and updates the corresponding property in a state object. This is the standard and scalable approach for handling multiple form fields in React.

---

# Explanation

Instead of creating a separate handler for every input:

```tsx
onFirstNameChange();
onLastNameChange();
onEmailChange();
```

React applications typically maintain the form in a single state object:

```tsx
{
  firstName: "",
  lastName: "",
  email: ""
}
```

A generic handler updates only the changed field.

---

### 1. Using the `name` attribute

Each input has a unique `name`:

```tsx
<input name="firstName" />
<input name="lastName" />
<input name="email" />
```

The handler reads the field name and value:

```tsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  const { name, value } = e.target;

  setForm((prev) => ({
    ...prev,
    [name]: value,
  }));
};
```

The computed property (`[name]`) updates only the corresponding key while preserving the rest of the state.

---

### 2. Controlled components

Each input's value comes from React state.

```tsx
<input name="email" value={form.email} onChange={handleChange} />
```

Benefits:

- Single source of truth
- Easy validation
- Easy form submission
- Predictable UI
- Simplified testing

---

### 3. Handling different input types

Checkboxes use `checked` instead of `value`.

```tsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  const { name, type, value, checked } = e.target;

  setForm((prev) => ({
    ...prev,
    [name]: type === "checkbox" ? checked : value,
  }));
};
```

For `<select>` and `<textarea>`, use the appropriate event type or a union type if sharing the same handler.

---

### 4. Rendering behavior (React 18)

Each keystroke triggers an `onChange` event and updates state.

React 18:

- efficiently schedules updates
- re-renders only affected components
- automatically batches multiple state updates occurring within the same event

Since typing is a high-priority user interaction, React processes these updates promptly to keep the UI responsive.

---

### 5. Component architecture

For small forms:

- Keep form state local with `useState`.

For larger forms:

- Split into reusable field components.
- Lift state to the parent or use Context when multiple components share form data.
- Consider libraries like React Hook Form or Formik for complex validation and performance optimization.

---

# Example

**Scaffold using Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

```tsx
import { useState } from "react";

type FormData = {
  firstName: string;
  lastName: string;
  email: string;
};

export default function App() {
  const [form, setForm] = useState<FormData>({
    firstName: "",
    lastName: "",
    email: "",
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;

    setForm((prev) => ({
      ...prev,
      [name]: value,
    }));
  };

  return (
    <>
      <input
        name="firstName"
        placeholder="First Name"
        value={form.firstName}
        onChange={handleChange}
      />

      <input
        name="lastName"
        placeholder="Last Name"
        value={form.lastName}
        onChange={handleChange}
      />

      <input
        name="email"
        type="email"
        placeholder="Email"
        value={form.email}
        onChange={handleChange}
      />

      <pre>{JSON.stringify(form, null, 2)}</pre>
    </>
  );
}
```

This example demonstrates:

- one reusable `onChange` handler
- controlled components
- immutable state updates
- scalable form management

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript for fast development, HMR, and excellent TypeScript support.
- **Avoid Create React App (CRA):** It is deprecated. Prefer Vite for SPAs, or Next.js/Remix when SSR, routing, or server-side data fetching is required.
- **ESM vs CommonJS:** Vite uses native **ES Modules (ESM)** during development, enabling faster startup and module loading. CommonJS is primarily used in older Node.js environments.
- **Bundler:** Vite leverages **esbuild** for dependency pre-bundling and **Rollup** for optimized production builds.

---

# Performance

- Store related form fields in a single object when it simplifies updates, but avoid unnecessary re-renders in very large forms by splitting state or components where appropriate.
- Use **React DevTools Profiler** to identify expensive re-renders while typing.
- Memoize reusable field components with `React.memo` and stabilize callbacks with `useCallback` when passing handlers to memoized children.
- Use `useMemo` for expensive derived values (e.g., filtered options or computed summaries).
- For large forms, libraries such as React Hook Form minimize re-renders by relying on uncontrolled inputs where appropriate.
- Lazy-load large form sections with `React.lazy` and `Suspense` if they are not immediately needed.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration testing.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/user-event @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

test("updates first name", async () => {
  render(<App />);

  await userEvent.type(screen.getByPlaceholderText("First Name"), "John");

  expect(screen.getByText(/John/)).toBeInTheDocument();
});
```

For end-to-end form testing, use **Playwright**.

---

# Ops & Deployment

- Validate user input on both the client and the server; never rely solely on client-side validation.
- Wrap form sections in **Error Boundaries** to catch rendering errors (note that event handler errors should be handled separately with `try/catch` or error reporting).
- Log validation failures and submission errors using monitoring tools such as Sentry.
- Optimize bundle size with route-based code splitting and deploy static assets through a CDN. If SEO or initial load performance is important, consider SSR with Next.js.

---

# Pitfalls

- Forgetting the `name` attribute prevents the generic handler from identifying which field to update.
- Mutating the state object directly instead of creating a new object can prevent React from detecting changes.
- Using the wrong property (`value` instead of `checked`) for checkboxes leads to incorrect state updates.

## Question 3. How do you use `map()` to render components in JSX?

# Short answer

Use JavaScript's **`Array.prototype.map()`** to transform an array of data into an array of React elements. Each rendered element **must have a unique and stable `key` prop** so React can efficiently reconcile updates.

---

# Explanation

`map()` is the most common way to render lists in React. It takes each item in an array and returns a JSX element.

```tsx
const users = ["Alice", "Bob", "Charlie"];

return (
  <>
    {users.map((user) => (
      <p key={user}>{user}</p>
    ))}
  </>
);
```

React renders one `<p>` for each array item.

---

## 1. Why use `map()`?

Instead of manually writing:

```tsx
<UserCard name="Alice" />
<UserCard name="Bob" />
<UserCard name="Charlie" />
```

You render dynamically:

```tsx
users.map((user) => <UserCard key={user.id} user={user} />);
```

Benefits:

- Less repetitive code
- Easily handles changing data
- Works well with APIs
- Scales to thousands of items

---

## 2. Importance of the `key` prop

Every element rendered with `map()` needs a unique `key`.

```tsx
users.map((user) => <UserCard key={user.id} user={user} />);
```

React uses the `key` to:

- Identify which items changed
- Preserve component state
- Minimize DOM updates
- Improve reconciliation performance

### ❌ Bad

```tsx
key = { index };
```

Using the array index is acceptable only for static lists that never change order, are never filtered, and never have items inserted or removed.

### ✅ Good

```tsx
key={user.id}
```

Use a unique database ID or another stable identifier.

---

## 3. Rendering behavior (React 18)

When the underlying array changes:

```tsx
setUsers([...users, newUser]);
```

React:

1. Creates a new virtual DOM.
2. Compares it with the previous tree.
3. Uses `key` values during reconciliation.
4. Updates only the affected DOM nodes.

React 18 also performs **automatic batching** for multiple state updates triggered in the same event.

---

## 4. Component architecture

Instead of rendering HTML directly:

```tsx
users.map((user) => <div>{user.name}</div>);
```

Prefer reusable components:

```tsx
users.map((user) => <UserCard key={user.id} user={user} />);
```

Advantages:

- Better separation of concerns
- Easier testing
- Improved reusability
- Cleaner codebase

---

## 5. Conditional rendering with `map()`

You can combine `map()` with conditions:

```tsx
users.map((user) =>
  user.active ? <UserCard key={user.id} user={user} /> : null,
);
```

Or filter first:

```tsx
users
  .filter((user) => user.active)
  .map((user) => <UserCard key={user.id} user={user} />);
```

Filtering before mapping is often clearer and avoids returning `null` values.

---

# Example

**Scaffold using Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

**`App.tsx`**

```tsx
import React from "react";

type User = {
  id: number;
  name: string;
};

const UserCard = React.memo(({ user }: { user: User }) => <li>{user.name}</li>);

export default function App() {
  const users: User[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
    { id: 3, name: "Charlie" },
  ];

  return (
    <ul>
      {users.map((user) => (
        <UserCard key={user.id} user={user} />
      ))}
    </ul>
  );
}
```

Run the application:

```bash
npm run dev
```

This example demonstrates:

- `map()` rendering
- reusable components
- stable keys
- `React.memo` for memoizing child components

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript for fast startup, HMR, and excellent TypeScript support.
- **Avoid Create React App (CRA):** It is deprecated. Prefer Vite for SPAs and Next.js or Remix when SSR or routing is required.
- **ESM vs CommonJS:** Vite uses **ES Modules (ESM)** during development, enabling faster module loading and HMR. CommonJS is mainly encountered in legacy Node.js projects.
- **Bundler:** Vite uses **esbuild** for dependency pre-bundling and **Rollup** for production builds.

---

# Performance

- Always use **stable, unique keys** to help React's reconciliation algorithm minimize DOM updates.
- Profile large lists using the **React DevTools Profiler** to identify unnecessary re-renders.
- Use `React.memo` for list item components when their props rarely change.
- Use `useCallback` for event handlers and `useMemo` for expensive derived data only when profiling shows a measurable benefit.
- For very large datasets, use virtualization libraries such as `react-window` or `react-virtualized`.
- Lazy-load routes or heavy list components with `React.lazy` and `Suspense` to reduce the initial bundle size.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders all users", () => {
  render(<App />);

  expect(screen.getByText("Alice")).toBeInTheDocument();
  expect(screen.getByText("Bob")).toBeInTheDocument();
  expect(screen.getByText("Charlie")).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright** to verify list rendering and user interactions.

---

# Ops & Deployment

- Wrap list sections in **Error Boundaries** to prevent rendering failures from affecting the entire application.
- Log rendering or data-loading issues with monitoring tools such as Sentry.
- For SEO-sensitive pages, consider SSR or React Server Components (e.g., Next.js App Router) to render lists on the server.
- Optimize bundle size with route-based code splitting and serve static assets through a CDN for faster delivery.

---

# Pitfalls

- **Don't use array indices as keys** for lists that can be reordered, filtered, inserted into, or deleted from.
- **Always return JSX** from the `map()` callback; forgetting `return` when using braces (`{}`) results in nothing being rendered.
- **Avoid expensive computations inside `map()`**; compute derived data outside the render or memoize it when appropriate.

## Question 4. How do you conditionally render `null` in React?

## Question 5. How do you prevent re-rendering when props haven't changed?

## Question 6. What are the rules of JSX syntax?

## Question 7. How do you render components conditionally without using ternary operators?

## Question 8. How do you pass an event argument to a handler function?

## Question 9. How do you implement a simple counter using state?

## Question 10. How do you render default content when a prop is missing?

## Question 11. How do you use logical AND (`&&`) for conditional rendering?

## Question 12. How do you implement simple form validation in React?

## Question 13. How do you create a functional component that accepts children?

## Question 14. How do you remove an item from a list in React state?

## Question 15. How do you implement a "show/hide" toggle component?

## Question 16. How do you create a custom hook for fetching data?

## Question 17. How do you handle cleanup when a component unmounts?

## Question 18. How do you memoize an expensive calculation in a component?

## Question 19. How do you prevent unnecessary `useEffect` calls?

## Question 20. How do you implement a "scroll to top" button in React?
