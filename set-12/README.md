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

# Short answer

You conditionally render `null` in React by returning `null` from a component or expression when you want **nothing to be rendered**. React treats `null`, `undefined`, and `false` as “render nothing”.

---

# Explanation

In React, rendering is declarative. If a condition is not met, you simply return `null` instead of JSX.

### 1. Returning `null` from a component

```tsx id="x1q8ab"
function UserProfile({ user }: { user: { name: string } | null }) {
  if (!user) return null;

  return <h1>{user.name}</h1>;
}
```

If `user` is `null`, React renders nothing for this component.

---

### 2. Inline conditional rendering

You can also use logical conditions inside JSX:

```tsx id="v9k2lm"
return <div>{isLoggedIn ? <Dashboard /> : null}</div>;
```

Here:

- `true` → renders `<Dashboard />`
- `false` → renders `null` (nothing)

---

### 3. Using `&&` operator (common pattern)

```tsx id="c3p8xz"
return <div>{isAdmin && <AdminPanel />}</div>;
```

If `isAdmin` is `false`, React renders `false` → effectively nothing (React ignores it).

---

### 4. Why `null` matters in React rendering

React reconciliation treats `null` as:

- No DOM node created
- No update needed for that branch
- Clean removal of previous render output if condition changes

Example:

```tsx id="r7m2pq"
{
  showMessage ? <Message /> : null;
}
```

When `showMessage` flips:

- React removes `<Message />` from DOM
- No manual DOM manipulation required

---

### 5. React 18 rendering behavior

With React 18:

- Conditional rendering is still part of the normal render phase
- Updates are **batched automatically**
- If multiple state updates toggle rendering, React groups them into a single render

```tsx id="b4n7qz"
setShow(true);
setCount((c) => c + 1);
```

Both updates are batched → one re-render.

---

### 6. Component architecture perspective

Using `null` is often better than conditional logic in parent components when:

- A component has its own visibility rules
- You want encapsulation of rendering logic
- You want reusable UI components

Example:

```tsx id="m1z9kd"
function Tooltip({ text }: { text?: string }) {
  if (!text) return null;
  return <span>{text}</span>;
}
```

This makes the component self-contained.

---

### 7. State management trade-offs

- Use `null` rendering for **UI visibility**
- Use state management (Context, Redux, Zustand) only if visibility is shared across components
- Avoid storing “render flags” globally unless multiple parts of the app depend on them

---

# Example

**Vite setup (React + TypeScript)**

```bash id="z8kq1w"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx id="p9r2va"
import { useState } from "react";

function Notification({ message }: { message: string | null }) {
  if (!message) return null;

  return <div style={{ padding: 10, background: "lightgreen" }}>{message}</div>;
}

export default function App() {
  const [message, setMessage] = useState<string | null>(null);

  return (
    <div>
      <button onClick={() => setMessage("Hello!")}>Show Message</button>

      <button onClick={() => setMessage(null)}>Hide Message</button>

      <Notification message={message} />
    </div>
  );
}
```

This demonstrates:

- conditional rendering using `null`
- clean unmounting of components
- controlled state toggling
- reusable component design

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript (fast HMR, modern ESM, minimal config)
- Avoid CRA (deprecated)
- For SSR: use Next.js or Remix if visibility depends on server-side data
- React uses **ESM-based bundling in Vite**, improving build speed and tree-shaking

---

# Performance

- Returning `null` is cheap — it avoids DOM creation entirely
- Prefer conditional rendering over CSS `display: none` when you want to **unmount components**
- Use `React.memo` for components that frequently toggle visibility but receive stable props
- Use `useMemo`/`useCallback` only when visibility changes cause expensive re-renders
- Use React DevTools Profiler to ensure hidden components are not unnecessarily re-rendering

---

# Testing

Using **Vitest + React Testing Library**:

```bash id="q2k9ld"
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx id="t8m2pw"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("hides notification when message is null", () => {
  render(<App />);

  expect(screen.queryByText("Hello!")).not.toBeInTheDocument();
});
```

Key idea:

- `queryByText` returns `null` when element is not rendered

---

# Ops & Deployment

- Ensure conditional rendering aligns with SSR hydration (Next.js): mismatched `null` vs DOM output can cause hydration warnings
- Use Error Boundaries to prevent UI crashes when conditional branches fail
- Log unexpected null states in production (Sentry or similar)
- Keep UI logic declarative to avoid manual DOM manipulation issues

---

# Pitfalls

- Returning `undefined` accidentally from a component can lead to inconsistent behavior (prefer explicit `null`)
- Using `&&` can render unexpected falsy values (e.g., `0 && <Component />` renders `0`)
- Overusing conditional rendering instead of proper component decomposition can lead to cluttered JSX

## Question 5. How do you prevent re-rendering when props haven't changed?

# Short answer

You prevent unnecessary re-renders when props haven’t changed by using **`React.memo` for components**, and ensuring **referential stability of props using `useMemo` and `useCallback`**. React then skips re-rendering when shallow comparison shows props are unchanged.

---

# Explanation

In React, a component re-renders when:

- its **parent re-renders**
- its **state changes**
- its **context changes**

Even if props are “logically the same”, React compares them using **shallow equality**, meaning:

- primitives → compared by value
- objects/functions → compared by reference

So this causes re-renders:

```tsx
<User name="John" />
```

inside a parent that re-renders, even if `"John"` didn’t change.

---

## 1. `React.memo` (primary solution)

Wrap components with `React.memo` to memoize rendering:

```tsx id="memo1"
const User = React.memo(function User({ name }: { name: string }) {
  console.log("render");
  return <div>{name}</div>;
});
```

### How it works:

- React compares previous and next props
- If shallowly equal → skips render
- If different → re-renders

---

## 2. Referential equality problem

Even with `React.memo`, this still re-renders:

```tsx id="ref1"
<User user={{ name: "John" }} />
```

Because `{ name: "John" }` is a **new object reference every render**.

---

## 3. Fix with `useMemo`

Stabilize object props:

```tsx id="memo2"
const user = useMemo(() => {
  return { name: "John" };
}, []);

<User user={user} />;
```

Now React sees the same reference → skips re-render.

---

## 4. Fix with `useCallback` (functions)

Functions also break memoization:

```tsx id="cb1"
<User onClick={() => doSomething()} />
```

Fix:

```tsx id="cb2"
const handleClick = useCallback(() => {
  doSomething();
}, []);

<User onClick={handleClick} />;
```

---

## 5. Component architecture strategy

### Good structure:

- Keep state as low as possible (local state)
- Memoize heavy children
- Split large components into smaller ones

```txt
App
 ├── Header (memoized)
 ├── UserList (memoized)
 │     └── UserItem (memoized)
```

---

## 6. React 18 rendering behavior

React 18 introduces:

- **automatic batching** → multiple state updates trigger one render
- **concurrent rendering** → renders may be interrupted and restarted
- **memoization still applies**, but React may re-render if priorities change

Important: `React.memo` is still effective, but not a guarantee if:

- context changes
- state inside component changes
- props references change

---

## 7. Advanced optimization patterns

### A. Split state to reduce re-renders

```tsx id="split1"
const [count, setCount] = useState(0);
const [text, setText] = useState("");
```

Avoid mixing unrelated state in one object unless necessary.

---

### B. Context optimization

Context causes re-renders of all consumers.

Fix:

- split contexts
- memoize provider values

```tsx id="ctx1"
const value = useMemo(() => ({ user, setUser }), [user]);
```

---

### C. Virtualization for large lists

Instead of optimizing renders, reduce DOM nodes:

- `react-window`
- `react-virtualized`

---

# Example

**Vite setup (React + TypeScript)**

```bash id="vite1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

---

### Optimized parent + memoized child

```tsx id="ex1"
import React, { useCallback, useState } from "react";

type Props = {
  name: string;
  onClick: () => void;
};

const Child = React.memo(({ name, onClick }: Props) => {
  console.log("Child rendered");
  return <button onClick={onClick}>{name}</button>;
});

export default function App() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return (
    <div>
      <h1>Count: {count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>
        Increment Parent State
      </button>

      <Child name="Stable Button" onClick={handleClick} />
    </div>
  );
}
```

### Behavior:

- Clicking "Increment Parent State" re-renders App
- `Child` does NOT re-render because:
  - `name` is stable
  - `onClick` is memoized with `useCallback`
  - `React.memo` blocks unnecessary render

---

# Tooling & Setup

- Use **Vite + React + TypeScript**
  - fast HMR
  - ESM-based dev server
  - optimized production builds via Rollup

- Avoid CRA (deprecated)
- Use Next.js if SSR or server components are required

---

# Performance

- Use `React.memo` for pure components
- Stabilize props with `useMemo` and `useCallback`
- Avoid inline object/function creation in JSX
- Use React DevTools Profiler to detect wasted renders
- Use virtualization for large datasets
- Prefer splitting components over deep memo chains

---

# Testing

Using Vitest + React Testing Library:

```bash id="test1"
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

```tsx id="test2"
import { render } from "@testing-library/react";
import App from "./App";

test("does not re-render memoized child unnecessarily", () => {
  render(<App />);
  // Use console spy or render counters in real test setups
});
```

For deeper verification:

- mock `console.log`
- assert render counts

---

# Ops & Deployment

- Memoization reduces CPU work but can increase memory usage—measure before optimizing
- Use React Profiler before adding `React.memo` everywhere
- Avoid premature optimization; focus on list-heavy or expensive components
- Ensure SSR frameworks (Next.js) don’t break memo assumptions during hydration
- Use CDN caching for static bundles and code-splitting for large apps

---

# Pitfalls

- Overusing `React.memo` → adds complexity without real benefit
- Forgetting that new object/function references break memoization
- Using `useMemo` everywhere → unnecessary overhead
- Assuming memoization prevents all re-renders (it doesn’t block state/context updates)

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
