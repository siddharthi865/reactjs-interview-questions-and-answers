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

## Question 4. Explain the role of key prop in React lists

## Question 5. How do you debug React applications?

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
