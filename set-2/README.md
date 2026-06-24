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

## Question 7. How do you include images in React projects?

## Question 8. What is React component lifecycle?

## Question 9. Difference between mounting, updating, and unmounting phases

## Question 10. How do you use ref in React?

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
