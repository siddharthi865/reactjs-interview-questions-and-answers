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

# Short answer

JSX (JavaScript XML) is a syntax extension for JavaScript that lets you write HTML-like code inside React components. The main rules are:

- Return a **single parent element** (or a Fragment).
- Close all tags.
- Use **camelCase** for DOM attributes and event handlers.
- Embed JavaScript using **`{}`**.
- Use `className` instead of `class` and `htmlFor` instead of `for`.
- Components must start with a **capital letter**.
- Every item rendered in a list needs a unique **`key`** prop.

---

# Explanation

JSX is **syntactic sugar** for `React.createElement()`. During compilation (e.g., by Vite + Babel or SWC), JSX is transformed into JavaScript function calls.

Example:

```tsx
const element = <h1>Hello</h1>;
```

Compiles approximately to:

```tsx
import { jsx as _jsx } from "react/jsx-runtime";

const element = _jsx("h1", {
  children: "Hello",
});
```

React then uses these element objects to build the Virtual DOM.

---

## 1. Return a single parent element

❌ Invalid

```tsx
<h1>Hello</h1>
<p>World</p>
```

✅ Valid

```tsx
<div>
  <h1>Hello</h1>
  <p>World</p>
</div>
```

or use a Fragment:

```tsx
<>
  <h1>Hello</h1>
  <p>World</p>
</>
```

Fragments avoid adding unnecessary DOM nodes.

---

## 2. Close all tags

Unlike HTML, every JSX tag must be closed.

✅

```tsx
<img src="/logo.png" alt="Logo" />
<input />
<br />
```

❌

```tsx
<img>
```

---

## 3. Use camelCase for attributes

JSX uses JavaScript property names.

```tsx
<button onClick={handleClick}>
  Save
</button>

<div tabIndex={0} />

<input maxLength={20} />
```

Common examples:

| HTML        | JSX         |
| ----------- | ----------- |
| `onclick`   | `onClick`   |
| `tabindex`  | `tabIndex`  |
| `maxlength` | `maxLength` |

---

## 4. Use `className` and `htmlFor`

Because `class` and `for` are reserved JavaScript keywords:

```tsx
<label htmlFor="email">
  Email
</label>

<input id="email" />

<div className="container">
  Hello
</div>
```

---

## 5. Embed JavaScript with `{}`

Anything inside `{}` is evaluated as JavaScript.

```tsx
const name = "Alice";

<h1>Hello {name}</h1>;
```

Expressions are allowed:

```tsx
<p>{2 + 3}</p>
```

Method calls:

```tsx
<p>{name.toUpperCase()}</p>
```

Conditional expressions:

```tsx
{
  isLoggedIn ? <Dashboard /> : <Login />;
}
```

---

## 6. Components start with a capital letter

React distinguishes HTML elements from React components by capitalization.

```tsx
function UserCard() {
  return <h2>User</h2>;
}

<UserCard />;
```

Lowercase names are treated as native HTML elements:

```tsx
<usercard />
```

React interprets this as a custom HTML tag rather than a React component.

---

## 7. Lists require `key`

```tsx
users.map((user) => <UserCard key={user.id} user={user} />);
```

Keys help React efficiently reconcile list updates and preserve component state.

---

## 8. Inline styles are JavaScript objects

```tsx
<div
  style={{
    color: "red",
    fontSize: "20px",
  }}
>
  Hello
</div>
```

CSS property names use camelCase.

---

## 9. Comments use JavaScript syntax

```tsx
<div>
  {/* This is a JSX comment */}
  <p>Hello</p>
</div>
```

---

## 10. Rendering behavior (React 18)

JSX itself is declarative—it describes the desired UI. During rendering:

1. JSX is compiled into React element objects.
2. React builds a Virtual DOM tree.
3. React compares it with the previous tree (reconciliation).
4. Only changed DOM nodes are updated.

React 18's concurrent rendering and automatic batching optimize when and how these updates are processed, but JSX syntax remains unchanged.

---

# Example

**Scaffold using Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`App.tsx`**

```tsx
type User = {
  id: number;
  name: string;
};

const users: User[] = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
];

export default function App() {
  return (
    <>
      <h1 className="title">Users</h1>

      <ul>
        {users.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>

      <button onClick={() => alert("Clicked")}>Click Me</button>
    </>
  );
}
```

This example demonstrates:

- Fragment (`<>...</>`)
- `className`
- `map()`
- `key`
- JavaScript expressions
- Event handling
- Type-safe React component

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript for fast HMR, native ESM support, and excellent TypeScript integration.
- **Avoid Create React App (CRA):** It is deprecated. Use Vite for SPAs or Next.js/Remix when SSR or routing is required.
- **ESM vs CommonJS:** Vite serves code as native **ES Modules (ESM)** during development. Production builds are optimized with Rollup.
- **JSX Transform:** Modern React uses the automatic JSX runtime, so you typically don't need `import React from "react";` solely to use JSX.

---

# Performance

- JSX itself has minimal runtime cost after compilation.
- Use the **React DevTools Profiler** to identify expensive component re-renders rather than optimizing JSX syntax.
- Use `React.memo` for components with stable props, and `useMemo`/`useCallback` only when profiling demonstrates a benefit.
- Render large collections efficiently using stable `key` props and consider virtualization (`react-window`, `react-virtualized`) for very large lists.
- Use `React.lazy` and `Suspense` for code splitting to reduce initial bundle size.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders the users heading", () => {
  render(<App />);
  expect(screen.getByRole("heading", { name: /users/i })).toBeInTheDocument();
});
```

Use **Playwright** for end-to-end testing of rendered JSX and user interactions.

---

# Ops & Deployment

- Wrap major UI sections in **Error Boundaries** to isolate rendering failures.
- Use SSR or React Server Components (e.g., Next.js App Router) for improved SEO and initial page load performance when appropriate.
- Monitor rendering errors with tools such as Sentry.
- Optimize production bundles using code splitting, tree shaking, and CDN delivery.

---

# Pitfalls

- Returning multiple sibling elements without a wrapper or Fragment.
- Using HTML attributes like `class` or `for` instead of `className` and `htmlFor`.
- Using array indices as `key` values for dynamic lists that can be reordered, inserted into, or deleted from.

## Question 7. How do you render components conditionally without using ternary operators?

# Short answer

You can conditionally render components **without using the ternary (`? :`) operator** by using:

- Logical **`&&`** operator
- Early `return` statements
- `if...else` before the `return`
- Returning `null`
- Variables that hold JSX
- Object/Map lookups (for multiple conditions)
- Immediately Invoked Function Expressions (IIFEs) (less common)

Choose the approach that makes the code the most readable.

---

# Explanation

React supports any JavaScript control flow outside JSX, and expressions inside JSX. The goal is to keep rendering logic clear and maintainable.

---

## 1. Using the logical `&&` operator (most common)

Render a component only when a condition is `true`.

```tsx
function App() {
  const isLoggedIn = true;

  return <div>{isLoggedIn && <Dashboard />}</div>;
}
```

If `isLoggedIn` is `false`, React renders nothing.

**Best for:** Rendering a component only when a condition is true.

**Caution:** If the left side can be `0`, it will render `0`.

```tsx
{
  count && <Message />;
} // Renders 0 when count === 0
```

A safer version:

```tsx
{
  count > 0 && <Message />;
}
```

---

## 2. Using an early return

Often the cleanest solution.

```tsx
function Dashboard({ user }: { user: string | null }) {
  if (!user) {
    return null;
  }

  return <h1>Welcome {user}</h1>;
}
```

This avoids deeply nested JSX.

---

## 3. Using `if...else`

Compute the UI before returning JSX.

```tsx
function App() {
  const isAdmin = true;

  if (isAdmin) {
    return <AdminPanel />;
  }

  return <UserPanel />;
}
```

This is often easier to read than nested ternaries.

---

## 4. Store JSX in a variable

Useful when rendering multiple alternatives.

```tsx
function App() {
  const isLoading = false;

  let content;

  if (isLoading) {
    content = <Spinner />;
  } else {
    content = <Dashboard />;
  }

  return <div>{content}</div>;
}
```

---

## 5. Return `null`

Render nothing.

```tsx
function Notification({ show }: { show: boolean }) {
  if (!show) {
    return null;
  }

  return <div>New Notification</div>;
}
```

This is the standard way to hide a component.

---

## 6. Object lookup (great for multiple conditions)

Instead of long `if...else` chains:

```tsx
function App() {
  const status = "success";

  const views = {
    loading: <Spinner />,
    success: <Dashboard />,
    error: <ErrorPage />,
  };

  return views[status as keyof typeof views];
}
```

This scales well for finite UI states.

---

## 7. IIFE (Immediately Invoked Function Expression)

Useful for more complex logic inside JSX, though less common.

```tsx
function App() {
  const role = "admin";

  return (
    <div>
      {(() => {
        if (role === "admin") return <AdminPanel />;
        if (role === "user") return <UserPanel />;
        return <GuestPanel />;
      })()}
    </div>
  );
}
```

Generally, moving this logic outside JSX improves readability.

---

## Rendering behavior (React 18)

Conditional rendering participates in React's normal rendering process:

- React evaluates the condition during render.
- The resulting React element tree is reconciled with the previous one.
- Components that become `null` are unmounted.
- Multiple state updates that affect conditions are **automatically batched** in React 18.

For example:

```tsx
setLoading(false);
setData(response);
```

These updates typically trigger a single render.

---

## Component architecture

Prefer encapsulating visibility logic inside the component when appropriate.

```tsx
function LoadingMessage({ loading }: { loading: boolean }) {
  if (!loading) return null;

  return <p>Loading...</p>;
}
```

Benefits:

- Better encapsulation
- Improved reusability
- Easier testing
- Cleaner parent components

---

## State management trade-offs

- Use local `useState` for component-specific visibility.
- Use Context, Redux Toolkit, or Zustand when visibility is shared across multiple parts of the application.
- Avoid global state for UI flags that are only relevant to a single component.

---

# Example

**Scaffold using Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx
import { useState } from "react";

function Spinner() {
  return <p>Loading...</p>;
}

function Dashboard() {
  return <h2>Dashboard</h2>;
}

export default function App() {
  const [loading, setLoading] = useState(true);

  let content;

  if (loading) {
    content = <Spinner />;
  } else {
    content = <Dashboard />;
  }

  return (
    <>
      <button onClick={() => setLoading((prev) => !prev)}>Toggle</button>

      {content}
    </>
  );
}
```

This demonstrates:

- `if...else`
- JSX variables
- conditional rendering without a ternary
- React 18-compatible state updates

---

# Tooling & Setup

- **Preferred stack:** Vite + React + TypeScript for fast startup, HMR, and modern ESM support.
- **Avoid Create React App (CRA):** It is deprecated. Prefer Vite for SPAs and Next.js or Remix when SSR or routing is required.
- **ESM vs CommonJS:** Vite uses native **ES Modules (ESM)** during development, while CommonJS is mainly found in older Node.js ecosystems.
- **Bundling:** Vite uses **esbuild** for dependency pre-bundling and **Rollup** for optimized production builds.

---

# Performance

- Conditional rendering itself is inexpensive; focus on avoiding unnecessary re-renders of expensive child components.
- Use **React DevTools Profiler** to identify render bottlenecks.
- Apply `React.memo` to components whose props remain stable.
- Use `useMemo` and `useCallback` only when profiling shows they reduce meaningful work.
- Lazy-load rarely used UI with `React.lazy` and `Suspense`.
- For large conditional lists, consider virtualization (`react-window` or `react-virtualized`).

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("shows loading initially", () => {
  render(<App />);
  expect(screen.getByText("Loading...")).toBeInTheDocument();
});
```

Use **Playwright** for end-to-end verification of conditional UI flows.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures in conditional branches.
- Log unexpected rendering states with monitoring tools such as Sentry.
- For SEO-sensitive pages, prefer SSR (e.g., Next.js App Router) when conditional content depends on server-side data.
- Keep bundles small with route-based code splitting and serve assets via a CDN.

---

# Pitfalls

- Using `&&` with numeric values can unintentionally render `0`; prefer explicit boolean expressions like `count > 0`.
- Writing deeply nested `if` statements or conditional logic directly in JSX reduces readability; extract logic into variables or helper functions.
- Returning `undefined` from a component instead of `null` when nothing should be rendered.

## Question 8. How do you pass an event argument to a handler function?

# How do you pass an event argument to a handler function?

## Short answer

You pass the event object by either:

- Passing the handler directly (`onClick={handleClick}`), where React automatically provides the event.
- Using an arrow function when you need to pass additional arguments (`onClick={(e) => handleClick(id, e)}`).

---

# Explanation

React automatically passes a **SyntheticEvent** object to event handlers. This object wraps the native browser event and provides a consistent API across browsers.

There are three common patterns:

### 1. Pass the handler directly (preferred)

Use this when only the event object is needed.

```tsx
<button onClick={handleClick}>Click</button>
```

```tsx
const handleClick = (event: React.MouseEvent<HTMLButtonElement>) => {
  console.log(event.currentTarget);
};
```

React automatically supplies the event.

---

### 2. Pass additional arguments

When you also need custom values (such as an ID), wrap the handler in an arrow function.

```tsx
<button onClick={(e) => handleClick(user.id, e)}>Delete</button>
```

```tsx
const handleClick = (
  id: number,
  event: React.MouseEvent<HTMLButtonElement>,
) => {
  console.log(id);
  console.log(event.type);
};
```

This is the standard approach for dynamically rendered lists.

---

### 3. Using `bind()`

Older React code sometimes uses `bind`.

```tsx
<button onClick={handleClick.bind(null, user.id)}>Delete</button>
```

Although valid, this is less common in modern React because arrow functions are more readable.

---

## Example (React + TypeScript)

### Create the project (Vite)

```bash
npm create vite@latest react-events-demo -- --template react-ts
cd react-events-demo
npm install
npm run dev
```

**App.tsx**

```tsx
import React from "react";

export default function App() {
  const users = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
  ];

  const handleDelete = (
    id: number,
    event: React.MouseEvent<HTMLButtonElement>,
  ) => {
    console.log(`Deleting user ${id}`);
    console.log(event.currentTarget.textContent);
  };

  return (
    <div>
      {users.map((user) => (
        <button key={user.id} onClick={(e) => handleDelete(user.id, e)}>
          Delete {user.name}
        </button>
      ))}
    </div>
  );
}
```

Clicking each button prints both the user ID and event information.

---

# Tooling & Setup

- Prefer **Vite** for React applications because it offers fast startup, instant HMR, and native ESM support.
- Avoid **Create React App (CRA)** since it is deprecated.
- For production applications:
  - **Vite** → SPA and component libraries
  - **Next.js** → SSR, SSG, Server Components
  - **Remix** → Nested routing and data loading

- Modern React projects use **ES Modules (ESM)**. Bundlers like Vite (Rollup for builds + esbuild during development) optimize code splitting and tree shaking automatically.

---

# Performance

- Inline arrow functions (`onClick={(e) => ...}`) create a new function on each render. In most applications, this cost is negligible.
- For large lists or frequently re-rendered memoized children:
  - Use `React.memo`.
  - Memoize callbacks with `useCallback` when passing handlers to memoized components.

- Use the **React Profiler** to determine whether function recreation is actually causing unnecessary renders before optimizing.
- Lazy-load large components with `React.lazy()` and `Suspense` to reduce initial bundle size.
- Use caching libraries like **TanStack Query** or **Apollo Client** to avoid unnecessary network requests.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

test("button click calls handler", async () => {
  render(<App />);

  await userEvent.click(screen.getByText(/Delete Alice/i));
});
```

Use **Playwright** for end-to-end interaction testing.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Log UI errors using services such as Sentry.
- Prefer SSR or Server Components (Next.js App Router) for SEO-sensitive pages and faster initial loads.
- Keep bundles small with dynamic imports and route-level code splitting.
- Serve static assets through a CDN and enable long-term caching for hashed files.

---

# Pitfalls

- **Don't call the handler immediately:** `onClick={handleClick()}` executes during rendering.
- **Avoid relying on `event.target`** when you need the element the handler is attached to; prefer `event.currentTarget`.
- **Don't overuse `useCallback`**—memoize handlers only when it provides measurable benefits, such as with memoized child components.

## Question 9. How do you implement a simple counter using state?

# How do you implement a simple counter using state?

## Short answer

Use the `useState` hook to store the counter value and update it with the setter function (`setCount`). In React 18, state updates are automatically batched for better performance.

---

# Explanation

A simple counter demonstrates the core React concept of **state**.

- `useState` creates state local to a component.
- Calling `setCount` schedules a re-render with the new state value.
- React compares the new Virtual DOM with the previous one and updates only the changed DOM nodes.
- In **React 18**, multiple state updates in the same event handler are **automatically batched**, reducing unnecessary renders.

### Basic flow

1. Component renders with an initial state.
2. User clicks a button.
3. `setCount()` updates the state.
4. React schedules a re-render.
5. The UI displays the updated count.

### Updating based on previous state

When the next value depends on the previous one, always use the functional update form:

```tsx
setCount((prev) => prev + 1);
```

This avoids stale state issues when multiple updates are queued.

---

# Example (React + TypeScript)

### Create the project (Vite)

```bash
npm create vite@latest react-counter-demo -- --template react-ts
cd react-counter-demo
npm install
npm run dev
```

**App.tsx**

```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState<number>(0);

  return (
    <main style={{ padding: "2rem" }}>
      <h1>Counter</h1>

      <p>Current Count: {count}</p>

      <button onClick={() => setCount((prev) => prev + 1)}>Increment</button>

      <button onClick={() => setCount((prev) => prev - 1)}>Decrement</button>

      <button onClick={() => setCount(0)}>Reset</button>
    </main>
  );
}
```

---

# Tooling & Setup

- Use **Vite** for new React projects because it provides a fast development server, Hot Module Replacement (HMR), and optimized production builds.
- Avoid **Create React App (CRA)** since it is deprecated.
- Recommended stacks:
  - **Vite** → Single Page Applications (SPA)
  - **Next.js** → SSR, SSG, React Server Components
  - **Remix** → Data-driven routing

- Modern React projects use **ES Modules (ESM)**. Vite leverages native ESM during development and Rollup for optimized production bundles.

---

# Performance

Even a simple counter highlights React performance concepts:

- React only re-renders the component whose state changes.
- React 18 **automatic batching** groups multiple state updates in one render.

Example:

```tsx
const handleClick = () => {
  setCount((c) => c + 1);
  setCount((c) => c + 1);
};
```

The counter increases by **2** but triggers only **one render**.

Optimization tips:

- Use **React Profiler** to inspect render frequency.
- Use `React.memo` for child components that don't depend on changing state.
- Use `useCallback` when passing callbacks to memoized children.
- Use `useMemo` only for expensive calculations.
- Use `React.lazy` and `Suspense` for code splitting.
- For server data, use caching libraries like **TanStack Query** or **Apollo Client** instead of local state.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("increments counter", async () => {
  render(<App />);

  await userEvent.click(screen.getByRole("button", { name: /increment/i }));

  expect(screen.getByText(/Current Count: 1/i)).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Wrap your application with an **Error Boundary** to catch rendering errors.
- Log runtime issues using tools like Sentry.
- For SEO-sensitive applications, consider SSR with **Next.js**.
- Keep bundles small with route-based code splitting and lazy loading.
- Deploy static Vite builds through a CDN for efficient caching and fast delivery.

---

# Pitfalls

- **Use functional updates** (`setCount(prev => prev + 1)`) when the next state depends on the previous state.
- **Never mutate state directly** (e.g., `count++`); always use the setter returned by `useState`.
- **Remember that state updates are asynchronous**, so don't expect the state variable to change immediately after calling `setCount`.

## Question 10. How do you render default content when a prop is missing?

# How do you render default content when a prop is missing?

## Short answer

Use **default parameter values** (recommended for functional components) or the **nullish coalescing operator (`??`)** to provide fallback content when a prop is `undefined` or `null`. Avoid relying on `defaultProps` for function components in modern React.

---

# Explanation

Props are optional unless explicitly required (e.g., with TypeScript). When a prop is missing, you can render a default value to keep the UI predictable.

### 1. Default parameter values (Recommended)

The cleanest and most modern approach is to assign defaults during destructuring.

```tsx
function Greeting({ name = "Guest" }: { name?: string }) {
  return <h1>Hello, {name}!</h1>;
}
```

If `name` isn't passed, `"Guest"` is used automatically.

---

### 2. Nullish coalescing (`??`)

Use `??` when rendering to fall back only if the value is `null` or `undefined`.

```tsx
<p>{description ?? "No description available."}</p>
```

Unlike `||`, this preserves valid falsy values like `0`, `false`, and `""`.

---

### 3. Logical OR (`||`)

```tsx
<p>{title || "Untitled"}</p>
```

Be careful: `||` treats **all falsy values** (`0`, `false`, `""`, `NaN`) as missing.

Example:

```tsx
title = ""; // Renders "Untitled"
```

Often this is **not** what you want.

---

### TypeScript example

```tsx
type UserCardProps = {
  name?: string;
  age?: number;
};
```

Optional props pair naturally with default values.

---

# Example (React + TypeScript)

### Create the project (Vite)

```bash
npm create vite@latest react-default-props-demo -- --template react-ts
cd react-default-props-demo
npm install
npm run dev
```

**App.tsx**

```tsx
type UserCardProps = {
  name?: string;
  city?: string;
};

function UserCard({ name = "Guest", city = "Unknown" }: UserCardProps) {
  return (
    <div>
      <h2>{name}</h2>
      <p>City: {city}</p>
    </div>
  );
}

export default function App() {
  return (
    <>
      <UserCard name="Alice" city="London" />
      <UserCard name="Bob" />
      <UserCard />
    </>
  );
}
```

**Output**

```
Alice
City: London

Bob
City: Unknown

Guest
City: Unknown
```

---

# Tooling & Setup

- Prefer **Vite** for modern React development because it provides fast startup, HMR, and optimized builds.
- Avoid **Create React App (CRA)** since it is deprecated.
- Use:
  - **Vite** for SPAs.
  - **Next.js** for SSR, SSG, and React Server Components.
  - **Remix** for nested routing and data loading.

- Modern React tooling uses **ES Modules (ESM)**. Vite uses native ESM during development and Rollup for production builds.

---

# Performance

Providing default prop values has virtually no performance cost.

For larger applications:

- Use **React.memo** to avoid unnecessary re-renders when props haven't changed.
- Use **useCallback** for stable callback props passed to memoized children.
- Use **useMemo** for expensive derived values.
- Profile renders with the **React Profiler** before optimizing.
- Use `React.lazy()` and `Suspense` for code splitting.
- Cache server state with **TanStack Query** or **Apollo Client** instead of repeatedly fetching data.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import UserCard from "./UserCard";

test("renders default name", () => {
  render(<UserCard />);

  expect(screen.getByText("Guest")).toBeInTheDocument();
});
```

Use **Playwright** for end-to-end testing of complete user flows.

---

# Ops & Deployment

- Use **Error Boundaries** to catch rendering errors.
- Log UI issues with tools like Sentry.
- Prefer SSR (e.g., Next.js) when default content affects SEO or initial page load.
- Optimize bundles using code splitting and tree shaking.
- Deploy static assets via a CDN with long-term caching for hashed files.

---

# Pitfalls

- **Prefer `??` over `||`** when `0`, `false`, or an empty string are valid values.
- **Avoid `defaultProps` for function components**; use default parameter values instead.
- **Mark optional props correctly in TypeScript** (`prop?: Type`) when providing defaults.

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
