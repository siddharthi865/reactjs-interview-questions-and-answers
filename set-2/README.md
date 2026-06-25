# Set 2

| #   | Question                                                                                                                                                   |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is the difference between className and class in JSX?](#question-1-what-is-the-difference-between-classname-and-class-in-jsx)                        |
| 2   | [What are synthetic events in React?](#question-2-what-are-synthetic-events-in-react)                                                                      |
| 3   | [How do you conditionally apply CSS classes?](#question-3-how-do-you-conditionally-apply-css-classes)                                                      |
| 4   | [Explain the role of key prop in React lists](#question-4-explain-the-role-of-key-prop-in-react-lists)                                                     |
| 5   | [How do you debug React applications?](#question-5-how-do-you-debug-react-applications)                                                                    |
| 6   | [What is ReactDOM? Difference between React and ReactDOM?](#question-6-what-is-reactdom-difference-between-react-and-reactdom)                             |
| 7   | [How do you include images in React projects?](#question-7-how-do-you-include-images-in-react-projects)                                                    |
| 8   | [What is React component lifecycle?](#question-8-what-is-react-component-lifecycle)                                                                        |
| 9   | [Difference between mounting, updating, and unmounting phases](#question-9-difference-between-mounting-updating-and-unmounting-phases)                     |
| 10  | [How do you use ref in React?](#question-10-how-do-you-use-ref-in-react)                                                                                   |
| 11  | [What is the difference between props drilling and context API?](#question-11-what-is-the-difference-between-props-drilling-and-context-api)               |
| 12  | [Explain lifting state up with an example](#question-12-explain-lifting-state-up-with-an-example)                                                          |
| 13  | [How do you pass a callback function as a prop?](#question-13-how-do-you-pass-a-callback-function-as-a-prop)                                               |
| 14  | [What is JSX transpilation?](#question-14-what-is-jsx-transpilation)                                                                                       |
| 15  | [How do you create React app from scratch using CRA (Create React App)?](#question-15-how-do-you-create-react-app-from-scratch-using-cra-create-react-app) |
| 16  | [What are React hooks? Name a few commonly used hooks](#question-16-what-are-react-hooks-name-a-few-commonly-used-hooks)                                   |
| 17  | [Explain useState hook with an example](#question-17-explain-usestate-hook-with-an-example)                                                                |
| 18  | [Explain useEffect hook with an example](#question-18-explain-useeffect-hook-with-an-example)                                                              |
| 19  | [What is dependency array in useEffect?](#question-19-what-is-dependency-array-in-useeffect)                                                               |
| 20  | [Explain useRef hook and its use cases](#question-20-explain-useref-hook-and-its-use-cases)                                                                |

## Question 1. What is the difference between className and class in JSX?

## Short answer

In JSX, you use `className` instead of `class` because `class` is a reserved keyword in JavaScript. Both serve the same purpose: assigning CSS classes to elements.

---

## Explanation

### Why `className` exists in React (JSX)

JSX is not HTML—it is syntactic sugar over `React.createElement()` calls. Since JavaScript already uses `class` as a reserved keyword for ES6 classes, React avoids conflicts by using `className`.

```jsx
// JSX
<div className="container" />
```

Compiles roughly to:

```js
React.createElement("div", { className: "container" });
```

### Rendering behavior

- `className` is passed as a prop to the DOM element.
- React reconciles it during the virtual DOM diffing process.
- It updates only when the value changes (React 18 batching optimizes this further).

### React 18 concurrency note

In concurrent rendering, class updates are still part of the commit phase. React batches multiple state updates (even across async boundaries) and applies DOM changes efficiently, including `className` updates.

---

## Example

### React + TypeScript (Vite setup)

#### Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

#### Component example

```tsx
import { useState } from "react";

export default function Button() {
  const [active, setActive] = useState(false);

  return (
    <button
      className={active ? "btn btn-active" : "btn"}
      onClick={() => setActive((prev) => !prev)}
    >
      Toggle
    </button>
  );
}
```

---

## Tooling & Setup

- Prefer **Vite** for fast ESM-based development (native ES modules, fast HMR).
- Avoid CRA (deprecated and slower tooling).
- In modern stacks:
  - Vite → CSR apps
  - Next.js → SSR/SSG + React Server Components

JSX compilation happens via:

- Vite: esbuild / SWC (fast transforms)
- Babel (older setups)

---

## Performance

- `className` updates trigger DOM diffing; avoid unnecessary re-renders.
- Use:
  - `React.memo` for component memoization
  - `useMemo` for computed class strings
  - `clsx` or `classnames` for efficient conditional classes

Example optimization:

```tsx
import { useMemo } from "react";

const className = useMemo(() => (active ? "btn btn-active" : "btn"), [active]);
```

- Use React DevTools Profiler to detect re-render causes.

---

## Testing

Using **Vitest + React Testing Library**:

```ts
import { render, screen } from "@testing-library/react";
import Button from "./Button";

test("renders button with correct class", () => {
  render(<Button />);
  const btn = screen.getByRole("button");
  expect(btn.className).toContain("btn");
});
```

Run:

```bash
npm run test
```

---

## Ops & Deployment

- Class-based styling integrates well with:
  - CSS Modules
  - Tailwind CSS (preferred in modern apps)

- Ensure:
  - Avoid inline string concatenation for large apps → use utility libraries
  - SSR frameworks (Next.js) ensure consistent hydration className matching

- Deploy via:
  - Vercel (Next.js)
  - Netlify / Cloudflare Pages (Vite apps)

---

## Pitfalls

- Using `class` instead of `className` causes JSX errors
- Overusing string concatenation for dynamic classes reduces readability
- Not memoizing complex class logic can lead to unnecessary re-renders

## Question 2. What are synthetic events in React?

## Short answer

**Synthetic Events** are React's cross-browser wrapper around native browser events. They provide a consistent API across different browsers, normalize event behavior, and integrate with React's event delegation and rendering system.

---

# Explanation

A **SyntheticEvent** is an object that React creates whenever an event (like `click`, `change`, or `submit`) occurs.

Instead of attaching event listeners to every DOM element, React uses **event delegation** by attaching a small number of listeners to the root of the React application and dispatching `SyntheticEvent` objects to your components.

### Why React uses Synthetic Events

- Provides a consistent API across browsers.
- Normalizes browser inconsistencies.
- Improves performance through event delegation.
- Integrates cleanly with React's rendering lifecycle.

Example:

```tsx
function Button() {
  function handleClick(event: React.MouseEvent<HTMLButtonElement>) {
    console.log(event.type); // "click"
    console.log(event.currentTarget);
  }

  return <button onClick={handleClick}>Click me</button>;
}
```

Here, `event` is a **SyntheticEvent**, not the raw browser event.

---

## React 18 rendering behavior

When an event handler runs:

1. React receives the browser event.
2. Creates a `SyntheticEvent`.
3. Executes your event handler.
4. Batches all state updates made during the handler.
5. Performs reconciliation.
6. Updates the DOM once during the commit phase.

Example:

```tsx
const handleClick = () => {
  setCount((c) => c + 1);
  setVisible(true);
};
```

Both updates are **automatically batched** in React 18, resulting in a single re-render.

---

## Event Pooling (Important Interview Question)

### React 16 and earlier

Synthetic events were **pooled** for performance.

```tsx
function handleClick(e) {
  setTimeout(() => {
    console.log(e.target); // ❌ null (event released)
  }, 100);
}
```

Developers had to call:

```tsx
e.persist();
```

to retain the event.

---

### React 17+

**Event pooling was removed.**

Now this works:

```tsx
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
  setTimeout(() => {
    console.log(e.target);
  }, 100);
}
```

No need for `event.persist()` anymore.

---

# Example

### Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

export default function App() {
  const [message, setMessage] = useState("Not clicked");

  function handleClick(event: React.MouseEvent<HTMLButtonElement>) {
    console.log("Synthetic Event:", event.type);
    console.log("Native Event:", event.nativeEvent);

    setMessage("Button clicked!");
  }

  return (
    <div>
      <h2>{message}</h2>

      <button onClick={handleClick}>Click Me</button>
    </div>
  );
}
```

Notice:

- `event.type` → from the SyntheticEvent API
- `event.nativeEvent` → underlying browser event if you need it

---

# Tooling & Setup

- Use **Vite** for new React projects. It provides fast startup, hot module replacement (HMR), and native ESM support.
- Avoid **Create React App (CRA)**, as it is deprecated.
- Choose:
  - **Vite** for client-rendered SPAs.
  - **Next.js** when SSR, SSG, or React Server Components are required.

- Vite uses modern ESM during development and a fast bundler (esbuild/SWC for transforms, Rollup for production builds).

---

# Performance

React's event system already optimizes event handling through delegation, but you should still consider:

- Use `React.memo` to prevent unnecessary child re-renders caused by parent state changes.
- Use `useCallback` when passing event handlers to memoized children.
- Use `useMemo` for expensive derived values used during rendering.
- Lazy-load route-level components with `React.lazy` and `Suspense`.
- Use the React DevTools **Profiler** to identify expensive renders triggered by event handlers.

---

# Testing

Use **Vitest** with React Testing Library to verify event handling:

```tsx
import { fireEvent, render, screen } from "@testing-library/react";
import App from "./App";

test("updates message on click", () => {
  render(<App />);

  fireEvent.click(screen.getByRole("button"));

  expect(screen.getByText("Button clicked!")).toBeInTheDocument();
});
```

Run:

```bash
npm run test
```

For end-to-end interaction testing, use Playwright.

---

# Ops & Deployment

- Use **Error Boundaries** to catch rendering errors (not errors thrown inside event handlers, which should be handled explicitly with `try/catch` or error reporting).
- Log unexpected event handler failures to monitoring tools such as Sentry.
- In SSR applications (e.g., Next.js), event handlers are attached during hydration; ensure the server and client render consistent markup.
- Reduce bundle size with route-based code splitting and lazy loading, and serve assets via a CDN or edge network.

---

# Pitfalls

- **Confusing `event.target` with `event.currentTarget`**—`target` is the element that initiated the event, while `currentTarget` is the element whose handler is executing.
- **Assuming event pooling still exists**—since React 17, `SyntheticEvent` objects are no longer pooled, so `event.persist()` is unnecessary.
- **Using `nativeEvent` without a need**—prefer the normalized `SyntheticEvent` API unless you specifically require browser-specific event details.

## Question 3. How do you conditionally apply CSS classes?

## Short answer

In React, you conditionally apply CSS classes by setting the `className` prop based on state, props, or other conditions. Common approaches include **ternary operators**, **logical AND (`&&`)**, template literals, or helper libraries like **`clsx`** (recommended) or `classnames` for complex class combinations.

---

# Explanation

React doesn't have special syntax for conditional classes—you use normal JavaScript expressions inside JSX.

### 1. Ternary operator (most common)

Use when choosing between two class names.

```tsx
<button className={isActive ? "btn btn-active" : "btn"}>Save</button>
```

### 2. Logical AND (`&&`)

Useful for appending a class only when a condition is true.

```tsx
<div className={`card ${isSelected && "selected"}`}>Card</div>
```

> Note: This pattern can produce unwanted values (`false`) in some cases. Using `clsx` is cleaner and safer.

### 3. Using `clsx` (recommended for production)

```tsx
import clsx from "clsx";

<div
  className={clsx("card", {
    selected: isSelected,
    disabled: isDisabled,
  })}
/>;
```

Benefits:

- Cleaner code
- Handles multiple conditions
- Avoids manual string concatenation
- Easy to maintain in large applications

---

## Rendering behavior

Whenever the state or props affecting `className` change:

1. React re-renders the component.
2. Computes the new `className`.
3. Compares it with the previous value during reconciliation.
4. Updates the DOM only if the class string has changed.

Example:

```tsx
const [dark, setDark] = useState(false);

<button
  className={dark ? "dark-btn" : "light-btn"}
  onClick={() => setDark(!dark)}
>
  Toggle Theme
</button>;
```

Only the `class` attribute is updated when `dark` changes.

---

## React 18 considerations

With automatic batching:

```tsx
setDark(true);
setLoading(false);
```

Both state updates are batched into a single render, so the `className` is recalculated only once.

---

# Example

## Scaffold with Vite (React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install clsx
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";
import clsx from "clsx";

export default function App() {
  const [active, setActive] = useState(false);

  return (
    <div>
      <button
        className={clsx("btn", {
          "btn-active": active,
        })}
        onClick={() => setActive((prev) => !prev)}
      >
        Toggle
      </button>
    </div>
  );
}
```

---

# Tooling & Setup

- **Use Vite** for modern React development (fast HMR, native ESM support).
- Avoid **Create React App (CRA)**, as it is deprecated.
- For styling:
  - CSS Modules for scoped styles
  - Tailwind CSS for utility-first styling
  - CSS-in-JS (e.g., Emotion, Styled Components) when dynamic styling is a key requirement

- Vite uses ESM in development and Rollup for optimized production bundles.

---

# Performance

- `className` computation is generally inexpensive, but avoid complex calculations during every render.
- Use:
  - `React.memo` for memoized child components.
  - `useMemo` if generating large or expensive class strings.
  - `useCallback` for event handlers passed to memoized components.

- Use `React.lazy` and `Suspense` for route-level code splitting.
- Profile renders with the React DevTools Profiler before optimizing.

---

# Testing

Use **Vitest** with React Testing Library:

```tsx
import { render, screen } from "@testing-library/react";

test("renders active class", () => {
  render(<button className="btn btn-active">Save</button>);

  expect(screen.getByRole("button")).toHaveClass("btn-active");
});
```

Run tests:

```bash
npm run test
```

For end-to-end verification of visual states, use Playwright.

---

# Ops & Deployment

- Keep styling predictable to avoid server/client hydration mismatches in SSR frameworks like Next.js.
- Use Error Boundaries for rendering errors (they do not catch errors thrown in event handlers).
- Minimize CSS bundle size by removing unused styles (e.g., Tailwind's purge/content configuration).
- Serve static CSS assets via a CDN for better caching and performance.

---

# Pitfalls

- Avoid long nested ternary expressions for class selection—they quickly become hard to read.
- Prefer helper libraries like `clsx` over manual string concatenation when multiple conditions are involved.
- Keep class names deterministic between server and client to prevent hydration warnings in SSR.

## Question 4. Explain the role of key prop in React lists

## Short answer

The `key` prop in React lists uniquely identifies each item so React can efficiently track changes (add, remove, reorder) during reconciliation and update only what’s necessary instead of re-rendering the entire list.

---

# Explanation

## Why `key` exists

When you render a list:

```tsx
items.map((item) => <li>{item.name}</li>);
```

React needs a way to understand:

- Which items are **new**
- Which items are **removed**
- Which items are **moved**
- Which items are **unchanged**

Without `key`, React falls back to **index-based diffing**, which can lead to incorrect UI updates.

---

## How React uses `key` internally

During reconciliation:

1. React builds a virtual DOM tree.
2. It compares previous and new trees.
3. `key` helps React match old and new elements.
4. React reuses DOM nodes where possible.
5. Only mismatched nodes are updated.

This improves:

- Performance (less DOM mutation)
- Correctness (avoids state misalignment)
- Stability in dynamic lists

---

## Important behavior in React 18

With concurrent rendering:

- React may pause and resume rendering work.
- Stable `key`s ensure React can safely resume work without mixing up list items.
- Incorrect keys can lead to UI inconsistencies under concurrent mode.

---

## Key rules (very important for interviews)

### 1. Keys must be unique among siblings

```tsx
items.map((item) => <li key={item.id}>{item.name}</li>);
```

### 2. Keys must be stable (not changing on re-render)

❌ Bad:

```tsx
key={Math.random()}
```

❌ Bad:

```tsx
key = { index };
```

✔ Good:

```tsx
key={user.id}
```

---

## Why using index as key is risky

```tsx
items.map((item, index) => <li key={index}>{item.name}</li>);
```

Problems:

- Wrong item reuse when list order changes
- State bugs in controlled inputs
- Animation glitches
- Incorrect component identity preservation

### Example bug scenario

If you reorder a list:

- Input fields may "move" to wrong items
- Local component state may stick to wrong row

---

## Component identity & state preservation

React uses `key` to decide whether a component is:

- **Same component → preserve state**
- **Different component → reset state**

```tsx
{
  show && <Counter key="counter1" />;
}
```

Changing the key forces remount:

```tsx
<Counter key={version} />
```

Useful for:

- Resetting forms
- Reinitializing components
- Forcing fresh state

---

# Example

## Vite setup

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Correct usage of keys

```tsx
type User = {
  id: string;
  name: string;
};

const users: User[] = [
  { id: "u1", name: "Alice" },
  { id: "u2", name: "Bob" },
  { id: "u3", name: "Charlie" },
];

export default function App() {
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

## Demonstrating remount via key

```tsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}

export default function App() {
  const [version, setVersion] = useState(1);

  return (
    <div>
      <button onClick={() => setVersion((v) => v + 1)}>Reset Counter</button>

      <Counter key={version} />
    </div>
  );
}
```

Changing `key` forces a full remount → state resets.

---

# Tooling & Setup

- Use **Vite** (recommended) for fast ESM-based development.
- Avoid **Create React App (CRA)** (deprecated).
- For large apps:
  - Next.js (SSR, routing, server components)
  - Remix (full-stack React patterns)

- Bundlers:
  - Vite uses esbuild (dev) + Rollup (prod)
  - Next.js uses Turbopack (modern builds)

---

# Performance

- Correct keys reduce unnecessary DOM operations.
- Avoid index keys in dynamic lists.
- Use:
  - `React.memo` for list items
  - virtualization (`react-window`, `react-virtualized`) for large lists

- Use React DevTools Profiler to detect:
  - unnecessary re-renders
  - unstable key issues

- Code split large lists or pages if needed

---

# Testing

Using Vitest + React Testing Library:

```tsx
import { render, screen } from "@testing-library/react";

test("renders list items", () => {
  render(<App />);

  const items = screen.getAllByRole("listitem");
  expect(items).toHaveLength(3);
});
```

For dynamic list behavior, also test:

- reorder correctness
- state persistence
- DOM stability

---

# Ops & Deployment

- Ensure stable IDs from backend APIs (preferred key source).
- Avoid generating keys on client side unless necessary.
- In SSR (Next.js):
  - keys must match between server and client render to avoid hydration mismatches.

- Use CDN caching carefully when list data is static.
- Log rendering anomalies in large lists (e.g., duplicate key warnings).

---

# Pitfalls

- Using `index` as key in dynamic/reorderable lists
- Using unstable keys like `Math.random()`
- Assuming keys are passed as props (they are not accessible in component props)
- Changing keys unnecessarily causing remounts and lost state

## Question 5. How do you debug React applications?

## Short answer

React apps are debugged using a combination of **React DevTools**, **browser DevTools (Console + Network + Performance tabs)**, **logging strategies**, and **runtime profiling tools** like the React Profiler. For complex issues, you trace rendering behavior, state updates, and side effects step-by-step through component re-renders and hooks execution.

---

# Explanation

Debugging React is mainly about understanding **render cycles, state flow, and side effects**.

A React app typically fails due to:

- Incorrect state updates
- Unintended re-renders
- Broken props flow
- Async side effects (API calls, subscriptions)
- Context or Redux state mismatches
- Rendering performance issues

---

## 1. React DevTools (most important tool)

Install:

- Chrome / Firefox React DevTools extension

### Key features:

#### a) Component tree inspection

- View props and state live
- Check context values
- Identify unexpected re-renders

#### b) Highlight updates

Shows which components re-rendered

#### c) Profiler tab

Measures render cost and timing

---

## 2. Browser DevTools

### Console

- Log state changes and props
- Catch runtime errors
- Use `console.trace()` for call stack debugging

```tsx id="q8d2v3"
console.log("State:", state);
console.trace("Render trace");
```

---

### Network tab

Used for:

- API request debugging
- Checking failed or slow requests
- Inspecting payloads and headers

---

### Performance tab

Helps identify:

- Long scripting tasks
- Re-render bottlenecks
- Layout thrashing

---

## 3. Debugging rendering behavior

React re-renders when:

- State changes (`useState`, `useReducer`)
- Props change
- Context changes
- Parent re-renders

### Example debug pattern:

```tsx id="k2d9x1"
useEffect(() => {
  console.log("Component rendered");
});
```

Better:

```tsx id="z7v1m2"
console.count("Render count");
```

---

## React 18 concurrency insight

In React 18:

- Rendering may be interrupted and restarted
- State updates are automatically batched
- Effects run after commit phase

So debugging must consider:

- Multiple render attempts
- Strict Mode double rendering (development only)

---

## 4. Debugging hooks

### Common issues:

#### useEffect dependency bugs

```tsx id="p9x4m1"
useEffect(() => {
  fetchData();
}, []); // missing dependencies bug
```

Fix using ESLint plugin:

- `eslint-plugin-react-hooks`

---

### stale closures

```tsx id="t5n8q3"
useEffect(() => {
  setInterval(() => {
    console.log(count); // stale value
  }, 1000);
}, []);
```

Fix using refs or correct dependencies.

---

## 5. Debugging state management

### useState / useReducer

- Log state transitions
- Ensure immutability

```tsx id="m4k8d9"
setState((prev) => {
  console.log(prev);
  return prev + 1;
});
```

### Redux / Context

- Use Redux DevTools
- Inspect dispatched actions
- Time travel debugging

---

## 6. Breakpoint debugging

You can use `debugger`:

```tsx id="u2k9d8"
function handleClick() {
  debugger;
  setCount((c) => c + 1);
}
```

Then:

- Open Chrome DevTools → Sources
- Step through execution

---

## 7. Performance debugging

### React Profiler

- Records render duration
- Shows wasted renders

Look for:

- unnecessary re-renders
- slow components

### Optimization tools:

- `React.memo`
- `useMemo`
- `useCallback`
- virtualization (`react-window`)

---

## 8. Logging strategies (production-safe)

Avoid excessive console logs in production.

Use:

- Feature flags
- Logging libraries (e.g., Sentry, LogRocket)

```tsx id="r8m1q7"
if (process.env.NODE_ENV === "development") {
  console.log("Debug info:", data);
}
```

---

## Example

## Vite setup

```bash id="v9x1c2"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Debug-friendly component

```tsx id="d4p8q1"
import { useState, useEffect } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("Component rendered");
  });

  return (
    <div>
      <h1>Count: {count}</h1>

      <button
        onClick={() => {
          debugger;
          setCount((c) => c + 1);
        }}
      >
        Increment
      </button>
    </div>
  );
}
```

---

# Tooling & Setup

### Recommended stack:

- **Vite** (fast dev server, modern ESM)
- **React DevTools**
- **TypeScript**
- **ESLint + React Hooks plugin**
- **Redux DevTools (if Redux used)**
- **Sentry / LogRocket (production monitoring)**

### Why not CRA?

- Deprecated
- Slower builds
- Less flexible than Vite/Next.js

---

# Performance

Key debugging-performance workflow:

1. Open React Profiler
2. Record interaction
3. Identify slow commits
4. Fix with:
   - memoization (`React.memo`)
   - stable callbacks (`useCallback`)
   - derived state cleanup
   - virtualization for large lists

---

# Testing (debugging via tests)

Use **Vitest + React Testing Library**:

```tsx id="t1v8m3"
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("increments counter", () => {
  render(<App />);

  fireEvent.click(screen.getByText("Increment"));

  expect(screen.getByText(/Count: 1/)).toBeInTheDocument();
});
```

Run:

```bash id="x8k2v9"
npm run test
```

---

# Ops & Deployment

- Use **Error Boundaries** for runtime UI errors
- Add **global error tracking** (Sentry)
- Monitor performance via **Web Vitals**
- Ensure source maps are enabled in staging for debugging
- Use CI logs for build-time errors

---

# Pitfalls

- Relying only on `console.log` instead of React DevTools
- Ignoring dependency arrays in `useEffect`
- Not considering Strict Mode double rendering in development
- Debugging without reproducing the exact state/context conditions

## Question 6. What is ReactDOM? Difference between React and ReactDOM?

## Short answer

**React** is the core library for building UI components, while **ReactDOM** is the platform-specific package that renders React components into the **browser DOM**. React handles _what to render_, and ReactDOM handles _how to render it in the browser_.

---

# Explanation

## 1. What is React?

React is a **UI library** focused on:

- Component creation (functional components, hooks)
- State management (`useState`, `useReducer`)
- Reconciliation (Virtual DOM diffing)
- Declarative UI logic

It is **platform-agnostic**, meaning React itself does NOT care where UI is rendered.

React can be used with:

- ReactDOM (web)
- React Native (mobile)
- React Three Fiber (3D)
- Custom renderers (Canvas, VR, etc.)

---

## 2. What is ReactDOM?

ReactDOM is a **rendering layer for the web**.

It provides:

- DOM mounting (`createRoot`)
- DOM updates during reconciliation
- Hydration (SSR apps like Next.js)
- Event binding to browser DOM

---

## Key API:

```tsx id="k1p9d2"
import ReactDOM from "react-dom/client";

const root = ReactDOM.createRoot(document.getElementById("root")!);

root.render(<App />);
```

---

## 3. Difference between React and ReactDOM

| Feature  | React                       | ReactDOM                 |
| -------- | --------------------------- | ------------------------ |
| Purpose  | Build UI logic              | Render UI to browser DOM |
| Focus    | Components, state, hooks    | DOM manipulation         |
| Platform | Platform-agnostic           | Web only                 |
| Provides | Virtual DOM, reconciliation | DOM APIs, hydration      |
| Example  | `useState`, `useEffect`     | `createRoot`, `render`   |

---

## 4. Rendering flow (important for interviews)

When React app runs:

1. React builds component tree
2. Creates Virtual DOM
3. ReactDOM takes that tree
4. Compares it with real DOM
5. Updates only changed parts

### React 18 modern flow:

```tsx id="r9x1k4"
import { createRoot } from "react-dom/client";

createRoot(document.getElementById("root")!).render(<App />);
```

### What changed in React 18:

- `ReactDOM.render()` ❌ deprecated
- `createRoot()` enables:
  - concurrent rendering
  - automatic batching
  - better streaming support

---

## 5. React without ReactDOM

React alone cannot render UI to the browser:

```tsx id="m8d2p1"
// React alone only describes UI
const element = <h1>Hello</h1>;
```

You need a renderer:

- ReactDOM → Web
- React Native → Mobile
- React Test Renderer → Testing

---

## 6. ReactDOM in SSR (important modern concept)

In frameworks like Next.js:

- Server uses `react-dom/server`
- Client uses `react-dom/client`

### Hydration:

```tsx id="h2k8d9"
import { hydrateRoot } from "react-dom/client";

hydrateRoot(document.getElementById("root")!, <App />);
```

This attaches React behavior to server-rendered HTML.

---

# Example

## Vite setup (recommended modern approach)

```bash id="v9k2d1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## React + ReactDOM usage

### `main.tsx`

```tsx id="p4x9d2"
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const rootElement = document.getElementById("root");

if (!rootElement) throw new Error("Root element not found");

ReactDOM.createRoot(rootElement).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

---

### `App.tsx`

```tsx id="c8m2p1"
export default function App() {
  return <h1>Hello React + ReactDOM</h1>;
}
```

---

# Tooling & Setup

## Modern stack:

- **Vite** (recommended)
- React 18+
- TypeScript

### Why Vite?

- Native ESM support
- Fast HMR
- Minimal bundling overhead
- Replaces older CRA approach

### ReactDOM role in tooling:

- Entry point for web apps
- Works with bundlers (Vite, Webpack, Turbopack)
- Handles DOM mounting & hydration

---

# Performance

ReactDOM performance features:

- **Batching updates (React 18+)**
- **Concurrent rendering support**
- **Selective DOM updates via diffing**

Optimization strategies:

- Use `React.memo` to avoid unnecessary renders
- Keep DOM updates minimal
- Use `useTransition` for non-blocking updates
- Avoid unnecessary root re-renders

Profiling:

- React DevTools Profiler
- Chrome Performance tab (long tasks, layout shifts)

---

# Testing

ReactDOM is often replaced in tests:

### React Testing Library (preferred)

```tsx id="t3v8m1"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders heading", () => {
  render(<App />);
  expect(screen.getByText("Hello React + ReactDOM")).toBeInTheDocument();
});
```

### Under the hood:

- Uses `react-dom/client` to mount components into a virtual DOM container

Run:

```bash id="r1x9d3"
npm run test
```

---

# Ops & Deployment

- ReactDOM is only used in **browser environments**
- In SSR frameworks:
  - Server uses `react-dom/server`
  - Client uses `react-dom/client`

- Ensure hydration consistency to avoid mismatches
- Deploy via:
  - Vercel (Next.js)
  - Netlify / Cloudflare Pages (Vite SPA)

- Use CDN for static assets

---

# Pitfalls

- Using deprecated `ReactDOM.render()` in React 18+
- Confusing React (logic) with ReactDOM (renderer)
- Hydration mismatch in SSR apps
- Mounting multiple roots unnecessarily instead of one app root

## Question 7. How do you include images in React projects?

## Short answer

In React, images can be included by **importing them as modules (recommended in Vite/CRA-like setups)**, using **public folder paths**, or loading them from **remote URLs (CDN/API)**. The best approach depends on whether the image is static, dynamic, or externally hosted.

---

# Explanation

React doesn’t treat images specially—Webpack/Vite handle them during bundling. There are three main approaches:

---

## 1. Import images (recommended for static assets)

Best for images inside `src/`.

```tsx id="img1"
import logo from "./assets/logo.png";

export default function Header() {
  return <img src={logo} alt="Logo" />;
}
```

### How it works

- Vite/Webpack processes the image
- Optimizes and hashes filename for caching
- Returns final URL at build time

### Rendering behavior

- Image is bundled into the app
- Cache-friendly hashed output in production

---

## 2. Using the `public` folder

Best for:

- Static files not processed by bundler
- SEO assets, favicon, robots.txt, etc.

```tsx id="img2"
export default function Banner() {
  return <img src="/images/banner.png" alt="Banner" />;
}
```

### Key difference

- Files are served **as-is**
- No hashing or optimization
- Accessed directly via URL

---

## 3. External image URLs (CDN / API)

Best for:

- User-uploaded images
- Cloud storage (S3, Cloudinary)
- Dynamic content

```tsx id="img3"
export default function Avatar() {
  return (
    <img src="https://cdn.example.com/user/avatar.png" alt="User avatar" />
  );
}
```

---

## 4. Dynamic images

When image paths are dynamic:

```tsx id="img4"
const getImage = (name: string) =>
  new URL(`./assets/${name}.png`, import.meta.url).href;

export default function Icon({ name }: { name: string }) {
  return <img src={getImage(name)} alt={name} />;
}
```

---

# React 18 rendering behavior

- Image rendering is part of DOM commit phase
- React does not manage image loading internally
- Browser handles loading, caching, decoding

### Important:

- Re-renders do NOT reload images if `src` stays the same
- Changing `src` triggers new network request

---

# Example

## Vite setup (modern recommended approach)

```bash id="vite1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Example project structure

```
src/
  assets/
    logo.png
  App.tsx
public/
  images/
    banner.png
```

---

## `App.tsx`

```tsx id="appimg1"
import logo from "./assets/logo.png";

export default function App() {
  return (
    <div>
      {/* Imported image */}
      <img src={logo} alt="Company Logo" width={120} />

      {/* Public folder image */}
      <img src="/images/banner.png" alt="Banner" width={300} />

      {/* External image */}
      <img src="https://via.placeholder.com/150" alt="Placeholder" />
    </div>
  );
}
```

---

# Tooling & Setup

## Recommended stack

- **Vite (preferred)** → fast ESM bundling, asset handling
- **Next.js** → built-in image optimization (`next/image`)
- **TypeScript** → safer asset imports

## Why Vite over CRA?

- Faster dev server (native ESM)
- Better asset pipeline
- Modern Rollup-based production builds
- CRA is deprecated

---

## Advanced (Next.js option)

Next.js provides optimized image handling:

```tsx id="nextimg"
import Image from "next/image";

export default function Page() {
  return <Image src="/logo.png" alt="Logo" width={200} height={200} />;
}
```

Benefits:

- Lazy loading by default
- Automatic resizing
- WebP/AVIF optimization

---

# Performance

## Key considerations:

### 1. Lazy loading

```tsx id="lazyimg"
<img src="/image.png" loading="lazy" alt="..." />
```

### 2. Use optimized formats

- Prefer WebP / AVIF over PNG/JPEG

### 3. Avoid layout shift

Always set dimensions:

```tsx id="dims"
<img src={logo} width={200} height={100} alt="logo" />
```

### 4. CDN usage

- Serve images via CDN for lower latency
- Use caching headers (`Cache-Control`)

### 5. React rendering optimization

- Changing image props triggers re-render
- Use `React.memo` if image component is part of larger tree

---

# Testing

Using React Testing Library:

```tsx id="imgtest"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders logo image", () => {
  render(<App />);
  const img = screen.getByAltText("Company Logo");
  expect(img).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Static images:
  - Go in `public/` or bundled via `src/assets`

- Production builds:
  - Vite hashes asset filenames for caching

- CDN best practice:
  - Store images in S3 / Cloudinary / Cloudflare Images

- SSR frameworks (Next.js):
  - Use `next/image` for optimization

- Monitor:
  - Largest Contentful Paint (LCP)
  - image load failures

---

# Pitfalls

- ❌ Using wrong path in `public/` (missing leading `/`)
- ❌ Not setting `alt` attributes (accessibility issue)
- ❌ Missing width/height → layout shift (CLS)
- ❌ Importing large images unnecessarily into bundle
- ❌ Using non-optimized formats (PNG instead of WebP)

## Question 8. What is React component lifecycle?

## Short answer

The **React component lifecycle** is the sequence of phases a component goes through: **mounting, updating, and unmounting**. In functional components, lifecycle behavior is handled using **Hooks like `useEffect`, `useLayoutEffect`, and cleanup functions**, rather than class lifecycle methods.

---

# Explanation

React components go through three main lifecycle phases:

---

## 1. Mounting (component is created and added to DOM)

This happens when a component is rendered for the first time.

### What happens:

- Component function runs
- JSX is returned
- DOM is created and inserted
- Effects run after render

### Functional component equivalent:

```tsx id="mount1"
useEffect(() => {
  console.log("Component mounted");

  return () => {
    console.log("Cleanup on unmount");
  };
}, []);
```

---

## 2. Updating (re-render due to state/props change)

Occurs when:

- State changes (`useState`, `useReducer`)
- Props change
- Context updates
- Parent re-renders

### Example:

```tsx id="update1"
useEffect(() => {
  console.log("Runs on every render or dependency change");
}, [count]);
```

---

## 3. Unmounting (component removed from DOM)

Happens when:

- Conditional rendering removes component
- Route changes
- Parent stops rendering it

### Cleanup example:

```tsx id="unmount1"
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running...");
  }, 1000);

  return () => {
    clearInterval(timer);
    console.log("Component unmounted");
  };
}, []);
```

---

# Lifecycle in Class Components (for interviews)

Even though modern React uses hooks, class lifecycle methods are still asked in interviews:

| Phase      | Method                                                  |
| ---------- | ------------------------------------------------------- |
| Mounting   | `constructor`, `render`, `componentDidMount`            |
| Updating   | `shouldComponentUpdate`, `render`, `componentDidUpdate` |
| Unmounting | `componentWillUnmount`                                  |

---

### Example:

```tsx id="class1"
class MyComponent extends React.Component {
  componentDidMount() {
    console.log("Mounted");
  }

  componentDidUpdate() {
    console.log("Updated");
  }

  componentWillUnmount() {
    console.log("Unmounted");
  }

  render() {
    return <h1>Hello</h1>;
  }
}
```

---

# React 18 behavior (important modern concept)

### 1. Rendering vs committing

React separates lifecycle into two phases:

- **Render phase** (pure calculation)
- **Commit phase** (DOM updates + effects)

### 2. Strict Mode (development only)

React intentionally double-invokes:

- functions
- effects

to detect side effects:

```tsx id="strict1"
<React.StrictMode>
  <App />
</React.StrictMode>
```

So you may see:

- mount logs twice in dev (not production behavior)

---

### 3. Automatic batching

Multiple state updates in lifecycle are grouped:

```tsx id="batch1"
setCount((c) => c + 1);
setFlag(true);
```

→ triggers only one re-render

---

# Example

## Vite setup (recommended)

```bash id="vite2"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Functional lifecycle demo

```tsx id="life1"
import { useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  // Mount + update
  useEffect(() => {
    console.log("Mounted or updated: count =", count);
  }, [count]);

  // Mount only
  useEffect(() => {
    console.log("Mounted");

    return () => {
      console.log("Unmounted");
    };
  }, []);

  return (
    <div>
      <h1>{count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

---

# Tooling & Setup

## Recommended stack:

- **Vite (preferred)** → fast ESM dev server
- React 18+
- TypeScript

## Why Vite?

- Instant HMR
- Native ES modules
- Minimal config
- Replaces CRA (deprecated)

## Framework alternatives:

- Next.js → SSR + routing + server components
- Remix → full-stack React

---

# Performance

Lifecycle awareness helps optimize:

### 1. Prevent unnecessary updates

- `React.memo`
- `useMemo`
- `useCallback`

### 2. Control side effects

- Proper dependency arrays in `useEffect`
- Avoid infinite loops

### 3. Cleanup effects

- Clear timers
- Unsubscribe from events
- Cancel API requests

### 4. React Profiler

- Identify expensive mount/update cycles

---

# Testing

Using Vitest + React Testing Library:

```tsx id="test1"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders counter", () => {
  render(<App />);
  expect(screen.getByText("0")).toBeInTheDocument();
});
```

Run:

```bash id="run1"
npm run test
```

---

# Ops & Deployment

- Ensure cleanup logic prevents memory leaks in:
  - intervals
  - subscriptions
  - WebSockets

- Use Error Boundaries for render-time errors
- Monitor lifecycle-heavy components for performance issues
- In SSR (Next.js):
  - mount lifecycle happens only on client hydration

- Deploy via Vercel / Netlify / Cloudflare Pages

---

# Pitfalls

- Missing cleanup in `useEffect` → memory leaks
- Incorrect dependency arrays → stale or infinite effects
- Confusing render phase vs commit phase
- Overusing `useEffect` for derived state instead of computing during render

## Question 9. Difference between mounting, updating, and unmounting phases

## Short answer

- **Mounting**: Component is created and inserted into the DOM for the first time.
- **Updating**: Component re-renders due to changes in state, props, or context.
- **Unmounting**: Component is removed from the DOM and cleanup logic runs.

---

# Explanation

React component lifecycle is divided into three main phases that describe how a component behaves over time in the UI.

---

## 1. Mounting phase

This is when the component is **created and added to the DOM**.

### What happens:

- Component function executes (or class constructor runs)
- JSX is rendered
- Virtual DOM is created
- Real DOM node is inserted
- Effects run after render (`useEffect` with `[]`)

### Functional equivalent:

```tsx id="mount2"
useEffect(() => {
  console.log("Mounted");

  return () => {
    console.log("Unmount cleanup");
  };
}, []);
```

### Key characteristics:

- Runs only once per component lifecycle (in normal cases)
- Used for initialization (API calls, subscriptions)

---

## 2. Updating phase

This happens when a component **re-renders due to changes**.

### Triggered by:

- `useState` updates
- `props` changes
- `context` updates
- Parent re-rendering

### Functional equivalent:

```tsx id="update2"
useEffect(() => {
  console.log("Runs on mount + when count changes");
}, [count]);
```

### Key characteristics:

- Happens multiple times
- React re-executes component function
- Virtual DOM is diffed (reconciliation)
- Only changed parts update in real DOM

### React 18 behavior:

- Updates are **automatically batched**
- Multiple state changes cause a single re-render

---

## 3. Unmounting phase

This is when the component is **removed from the DOM**.

### What happens:

- Component is destroyed
- Event listeners cleaned up
- Timers/subscriptions stopped
- Cleanup function runs

### Functional equivalent:

```tsx id="unmount2"
useEffect(() => {
  const timer = setInterval(() => {
    console.log("Running...");
  }, 1000);

  return () => {
    clearInterval(timer);
    console.log("Component unmounted");
  };
}, []);
```

---

# Key differences (interview-ready table)

| Phase      | When it happens   | What triggers it                     | Key purpose          |
| ---------- | ----------------- | ------------------------------------ | -------------------- |
| Mounting   | First render      | Component is added                   | Initialize component |
| Updating   | Re-render         | State/props/context change           | Sync UI with data    |
| Unmounting | Component removed | Conditional rendering / route change | Cleanup resources    |

---

# React internal behavior (important)

### 1. Mounting

- Fiber node is created
- DOM nodes are inserted
- Effects scheduled after commit

### 2. Updating

- New virtual tree is created
- Diffing (reconciliation) occurs
- Minimal DOM updates applied
- Effects re-run if dependencies change

### 3. Unmounting

- Fiber node is removed
- Cleanup functions executed
- DOM nodes destroyed

---

# Example

## Vite setup

```bash id="vite3"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Lifecycle demo component

```tsx id="life2"
import { useEffect, useState } from "react";

export default function App() {
  const [show, setShow] = useState(true);
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setShow((s) => !s)}>Toggle Component</button>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>

      {show && <Child count={count} />}
    </div>
  );
}

function Child({ count }: { count: number }) {
  useEffect(() => {
    console.log("Mounted");

    return () => {
      console.log("Unmounted or before re-run cleanup");
    };
  }, []);

  useEffect(() => {
    console.log("Updated count:", count);
  }, [count]);

  return <h1>{count}</h1>;
}
```

---

# Tooling & Setup

- Use **Vite** (preferred modern setup)
- React 18+ for concurrent rendering
- TypeScript for type safety

Why Vite:

- Fast dev server (ESM-based)
- Instant HMR
- Modern build pipeline

Avoid CRA (deprecated).

---

# Performance

### Mount optimization

- Avoid heavy computations during initial render
- Lazy load components (`React.lazy`)

### Update optimization

- `React.memo` prevents unnecessary re-renders
- `useMemo` avoids recalculating expensive values
- `useCallback` stabilizes functions

### Unmount optimization

- Always clean up:
  - intervals
  - event listeners
  - subscriptions
  - WebSockets

### Profiling tools

- React DevTools Profiler
- Chrome Performance tab

---

# Testing

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders toggle button", () => {
  render(<App />);
  expect(screen.getByText("Toggle Component")).toBeInTheDocument();
});
```

For lifecycle behavior:

- simulate mount/unmount
- verify cleanup logic

Run:

```bash id="test-run2"
npm run test
```

---

# Ops & Deployment

- Ensure cleanup prevents memory leaks (especially in SPAs)
- Use Error Boundaries for crash recovery
- In SSR (Next.js):
  - mount happens only on client hydration

- Monitor:
  - render frequency
  - memory usage
  - long-running effects

Deploy via:

- Vercel (Next.js)
- Netlify / Cloudflare Pages (Vite)

---

# Pitfalls

- Forgetting cleanup in `useEffect` → memory leaks
- Incorrect dependency arrays → infinite updates or stale values
- Confusing re-render with remount
- Assuming unmount always means app shutdown (it can be temporary in routing)

## Question 10. How do you use ref in React?

## Short answer

`ref` in React is used to **directly access DOM elements or persist values across renders without causing re-renders**. It is created using `useRef()` in functional components and attached to elements via the `ref` attribute.

---

# Explanation

## What is `ref`?

A **ref (reference)** is a mutable container that React does not track for re-renders.

It is mainly used for:

- Accessing DOM elements directly (imperative actions)
- Storing mutable values that don’t trigger re-render
- Managing focus, scroll, animations, timers, etc.

---

## 1. Creating a ref

```tsx id="ref1"
import { useRef } from "react";

const inputRef = useRef<HTMLInputElement>(null);
```

- `useRef()` returns an object: `{ current: ... }`
- `current` holds the actual value or DOM node

---

## 2. Accessing DOM elements

```tsx id="ref2"
import { useRef } from "react";

export default function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

### Key idea:

- React gives you the DOM node after render
- You can call native DOM APIs like `.focus()`, `.scrollIntoView()`

---

## 3. Storing mutable values (no re-render)

```tsx id="ref3"
import { useRef, useState } from "react";

export default function App() {
  const renderCount = useRef(0);
  const [count, setCount] = useState(0);

  renderCount.current++;

  return (
    <div>
      <p>Count: {count}</p>
      <p>Renders: {renderCount.current}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### Important:

- Updating `ref.current` does NOT trigger re-render
- Unlike `useState`

---

## 4. React 18 behavior

- Refs are initialized during render
- DOM assignment happens in **commit phase**
- Ref updates are stable across re-renders

React ensures:

- `ref.current` is updated after DOM commit
- No concurrent rendering inconsistencies

---

## 5. Forwarding refs (advanced pattern)

Used when passing refs to child components:

```tsx id="ref4"
import { forwardRef, useRef } from "react";

const Input = forwardRef<HTMLInputElement>((props, ref) => {
  return <input ref={ref} {...props} />;
});

export default function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  return (
    <>
      <Input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>Focus</button>
    </>
  );
}
```

---

## 6. When NOT to use refs

Avoid refs for:

- UI state (use `useState`)
- Derived values
- Conditional rendering logic

Bad example:

```tsx id="ref5"
const isOpen = useRef(false); // ❌ wrong for UI state
```

Correct:

```tsx id="ref6"
const [isOpen, setIsOpen] = useState(false);
```

---

# Example

## Vite setup

```bash id="vite4"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Full working example

```tsx id="ref7"
import { useRef } from "react";

export default function App() {
  const boxRef = useRef<HTMLDivElement>(null);

  const scrollToBox = () => {
    boxRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  return (
    <div style={{ height: "200vh", padding: "20px" }}>
      <button onClick={scrollToBox}>Go to Box</button>

      <div style={{ marginTop: "150vh" }}>
        <div ref={boxRef} style={{ height: "100px", background: "lightblue" }}>
          Target Box
        </div>
      </div>
    </div>
  );
}
```

---

# Tooling & Setup

- Use **Vite (recommended)** for modern React apps
- React 18+ ensures stable ref handling in concurrent rendering
- TypeScript improves ref safety:

  ```tsx
  useRef<HTMLInputElement>(null);
  ```

Framework options:

- Vite → SPA apps
- Next.js → SSR + server components (refs only on client)

---

# Performance

- Refs do NOT trigger re-renders → useful for performance-sensitive values
- Avoid using state when mutation is enough
- Use refs for:
  - DOM access
  - timers/intervals
  - previous value tracking

### Optimization tips:

- Avoid excessive DOM manipulation via refs
- Combine with `useEffect` for lifecycle-controlled DOM access
- Use `React.memo` to avoid unnecessary child re-renders

---

# Testing

Using React Testing Library:

```tsx id="test3"
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("renders button and input interaction", () => {
  render(<App />);

  const button = screen.getByText("Go to Box");
  fireEvent.click(button);

  expect(button).toBeInTheDocument();
});
```

For ref-based logic:

- Test DOM behavior (focus, scroll)
- Not internal ref values directly

---

# Ops & Deployment

- Refs are client-only concepts (not meaningful in SSR rendering output)
- In Next.js:
  - refs only attach after hydration

- Avoid SSR assumptions for DOM refs
- Ensure cleanup when refs manage resources (timers, listeners)

---

# Pitfalls

- Using refs for state management instead of `useState`
- Accessing `ref.current` before mount (null issue)
- Mutating DOM too heavily → bypassing React’s reconciliation
- Forgetting that ref updates do NOT trigger re-renders

## Question 11. What is the difference between props drilling and context API?

## Question 12. Explain lifting state up with an example

## Question 13. How do you pass a callback function as a prop?

## Question 14. What is JSX transpilation?

## Question 15. How do you create React app from scratch using CRA (Create React App)?

## Question 16. What are React hooks? Name a few commonly used hooks

## Question 17. Explain useState hook with an example

## Question 18. Explain useEffect hook with an example

## Question 19. What is dependency array in useEffect?

## Question 20. Explain useRef hook and its use cases
