# Set 7

| #   | Question                                                                                                                                                                         |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What are default values for props and state?](#question-1-what-are-default-values-for-props-and-state)                                                                          |
| 2   | [How do you access DOM elements in class components?](#question-2-how-do-you-access-dom-elements-in-class-components)                                                            |
| 3   | [What is the difference between ReactDOM.render and ReactDOM.createRoot?](#question-3-what-is-the-difference-between-reactdomrender-and-reactdomcreateroot)                      |
| 4   | [How do you prevent a component from rendering?](#question-4-how-do-you-prevent-a-component-from-rendering)                                                                      |
| 5   | [What is the difference between local and global state in React?](#question-5-what-is-the-difference-between-local-and-global-state-in-react)                                    |
| 6   | [Can you update state directly without setState? Why not?](#question-6-can-you-update-state-directly-without-setstate-why-not)                                                   |
| 7   | [How do you handle multiple inputs in a form?](#question-7-how-do-you-handle-multiple-inputs-in-a-form)                                                                          |
| 8   | [How do you pass functions as props?](#question-8-how-do-you-pass-functions-as-props)                                                                                            |
| 9   | [How do you conditionally render components using logical operators?](#question-9-how-do-you-conditionally-render-components-using-logical-operators)                            |
| 10  | [How do you debug React component lifecycle issues?](#question-10-how-do-you-debug-react-component-lifecycle-issues)                                                             |
| 11  | [What is the difference between a controlled input and a read-only input?](#question-11-what-is-the-difference-between-a-controlled-input-and-a-read-only-input)                 |
| 12  | [How do you handle component cleanup in functional components?](#question-12-how-do-you-handle-component-cleanup-in-functional-components)                                       |
| 13  | [How do you update nested state objects?](#question-13-how-do-you-update-nested-state-objects)                                                                                   |
| 14  | [What is the difference between functional and arrow functions in event handlers?](#question-14-what-is-the-difference-between-functional-and-arrow-functions-in-event-handlers) |
| 15  | [How do you prevent unnecessary re-rendering of child components?](#question-15-how-do-you-prevent-unnecessary-re-rendering-of-child-components)                                 |
| 16  | [Explain useImperativeHandle hook](#question-16-explain-useimperativehandle-hook)                                                                                                |
| 17  | [How does React.memo work for functional components?](#question-17-how-does-reactmemo-work-for-functional-components)                                                            |
| 18  | [Difference between useEffect and useLayoutEffect](#question-18-difference-between-useeffect-and-uselayouteffect)                                                                |
| 19  | [How do you cancel async operations in useEffect?](#question-19-how-do-you-cancel-async-operations-in-useeffect)                                                                 |
| 20  | [How do you implement a custom hook? Give an example](#question-20-how-do-you-implement-a-custom-hook-give-an-example)                                                           |

## Question 1. What are default values for props and state?

Got it—React interview answer format engaged.

---

# Short answer

Default values for **props** are defined via default parameters or `defaultProps` (legacy for class components).
State default values are defined in the component’s initial state using `useState` (or constructor state in class components).

---

# Explanation

## 1. Default values for Props

Props are **inputs to a component**, so React does not enforce defaults automatically. If a parent does not pass a prop, it becomes `undefined` unless a default is defined.

### Modern React (preferred)

Default values are set using **default parameters**:

```tsx
function Button({
  label = "Click me",
  disabled = false,
}: {
  label?: string;
  disabled?: boolean;
}) {
  return <button disabled={disabled}>{label}</button>;
}
```

### Alternative (still valid)

Using destructuring with default values:

```tsx
const Button = (props: { label?: string }) => {
  const { label = "Default Label" } = props;
  return <button>{label}</button>;
};
```

### Legacy (class components)

```tsx
class Button extends React.Component {
  static defaultProps = {
    label: "Click me",
  };

  render() {
    return <button>{this.props.label}</button>;
  }
}
```

⚠️ Note: `defaultProps` is **deprecated for function components** in modern React patterns.

---

## 2. Default values for State

State always needs an **initial value**, which becomes the default state.

### Functional Components (modern approach)

```tsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState<number>(0); // default state = 0

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### Lazy initialization (performance optimization)

```tsx
const [value, setValue] = useState(() => expensiveComputation());
```

This ensures computation runs only once during mount.

---

### Class Components (legacy)

```tsx
class Counter extends React.Component {
  state = {
    count: 0,
  };

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

---

# Tooling & Setup

Use modern React setup:

```bash
npm create vite@latest react-props-state -- --template react-ts
cd react-props-state
npm install
npm run dev
```

- Vite is preferred over CRA (deprecated)
- Uses ESM by default → faster dev server and HMR
- TypeScript improves prop/state safety

---

# Performance

- Use **lazy state initialization** when initial state is expensive
- Avoid recalculating derived defaults inside render
- Memoize derived props using `useMemo` if needed
- Use `React.memo` for components receiving default-heavy props
- React 18 automatically batches state updates → default state updates inside events are optimized

---

# Testing

With **Vitest + React Testing Library**:

```ts
import { render, screen } from "@testing-library/react";
import Button from "./Button";

test("renders default prop value", () => {
  render(<Button />);
  expect(screen.getByRole("button")).toHaveTextContent("Click me");
});
```

Run:

```bash
npm run test
```

---

# Ops & Deployment

- Ensure default props don’t depend on runtime-only values unless handled safely
- Avoid undefined prop assumptions in SSR (Next.js hydration mismatch risk)
- Use error boundaries if default state depends on async initialization
- Bundle optimization: avoid inline heavy default objects/functions
- Deploy via Vite build (`npm run build`) → static output to CDN

---

# Pitfalls

- Assuming props always exist → leads to runtime `undefined` errors
- Using deprecated `defaultProps` in function components
- Initial state derived from props without syncing properly (stale state bug)

## Question 2. How do you access DOM elements in class components?

# Short answer

In **class components**, you access DOM elements using **refs**. Create a ref with `React.createRef()`, attach it to a JSX element via the `ref` prop, and access the DOM node using `this.ref.current`, typically inside lifecycle methods like `componentDidMount()`.

---

# Explanation

Refs provide a way to interact directly with DOM elements when declarative React patterns aren't sufficient. Common use cases include:

- Focusing an input
- Measuring an element's size or position
- Scrolling an element into view
- Integrating with third-party DOM libraries (e.g., charts, maps)

### How it works

1. Create a ref in the constructor (or as a class field).
2. Attach it to a DOM element using the `ref` attribute.
3. Access the DOM node through `this.ref.current`.

```text
React.createRef()
        ↓
 this.inputRef
        ↓
<input ref={this.inputRef} />
        ↓
this.inputRef.current
```

### Lifecycle considerations

- **`componentDidMount()`**: Safe place to access the DOM because the component has been mounted.
- **`componentDidUpdate()`**: Use when DOM interactions depend on updated props or state.
- **`componentWillUnmount()`**: Clean up listeners or third-party libraries attached to the DOM.

Avoid accessing refs inside `render()` because the DOM has not been committed yet.

---

# Example

## Create a Vite React + TypeScript project

```bash
npm create vite@latest react-class-ref-demo -- --template react-ts
cd react-class-ref-demo
npm install
npm run dev
```

### Class component using `createRef`

```tsx
import React from "react";

class InputFocus extends React.Component {
  private inputRef = React.createRef<HTMLInputElement>();

  componentDidMount() {
    this.inputRef.current?.focus();
  }

  handleFocus = () => {
    this.inputRef.current?.focus();
  };

  render() {
    return (
      <div>
        <input ref={this.inputRef} placeholder="Type here" />
        <button onClick={this.handleFocus}>Focus Input</button>
      </div>
    );
  }
}

export default InputFocus;
```

This example automatically focuses the input after mounting and allows focusing it again via a button.

---

# Tooling & Setup

- **Preferred bundler:** Vite for fast startup and HMR.
- **Avoid Create React App (CRA):** It is deprecated.
- **ESM:** Vite uses native ES Modules for faster development.
- **Framework choices:**
  - **Vite**: Standard React applications.
  - **Next.js**: SSR, SSG, React Server Components.
  - **Remix**: Nested routing and data loading.
  - **Turbopack**: Modern bundler used with Next.js development.

Although class components remain supported, new React development primarily uses functional components with `useRef`.

---

# Performance

- Refs do **not** trigger re-renders when `.current` changes.
- Avoid frequent DOM reads and writes during rendering.
- Use `React.Profiler` to detect unnecessary renders.
- Use `React.memo`, `useMemo`, and `useCallback` in functional components when optimizing surrounding components.
- Use code splitting (`React.lazy` and `Suspense`) for large applications.
- Cache API data using libraries like TanStack Query or SWR instead of storing everything in component state.

---

# Testing

Use **Vitest + React Testing Library** to verify DOM interactions.

```tsx
import { render, screen } from "@testing-library/react";
import InputFocus from "./InputFocus";

test("renders input", () => {
  render(<InputFocus />);
  expect(screen.getByPlaceholderText("Type here")).toBeInTheDocument();
});
```

Run:

```bash
npm run test
```

For end-to-end focus behavior, Playwright is a good choice.

---

# Ops & Deployment

- Use refs sparingly; prefer React's declarative state model whenever possible.
- Wrap class component trees in Error Boundaries for graceful error handling.
- Be cautious when measuring the DOM during SSR—DOM APIs are only available on the client.
- Remove any event listeners or third-party instances in `componentWillUnmount()`.
- Keep bundle size small using tree shaking and dynamic imports.

---

# Pitfalls

- Accessing `this.ref.current` before `componentDidMount()` (it may be `null`).
- Using refs for state management instead of React state.
- Forgetting to clean up DOM-related resources or third-party integrations in `componentWillUnmount()`.

## Question 3. What is the difference between ReactDOM.render and ReactDOM.createRoot?

# Short answer

- **`ReactDOM.render()`** is the **legacy rendering API** used in React 17 and earlier. It creates a **Legacy Root** and does **not** enable React 18's concurrent rendering features.
- **`ReactDOM.createRoot()`** is the **modern API** introduced in React 18. It creates a **Concurrent Root**, enabling features like **automatic batching**, **concurrent rendering**, improved scheduling, and newer React capabilities. For all new React applications, use `createRoot()`.

---

# Explanation

## `ReactDOM.render()` (Legacy)

Before React 18, applications were typically mounted like this:

```tsx
import ReactDOM from "react-dom";

ReactDOM.render(<App />, document.getElementById("root"));
```

Characteristics:

- Uses the **Legacy Root**.
- Updates are processed synchronously.
- Does **not** opt into React 18 concurrent features.
- Supported mainly for backward compatibility and migration.

---

## `ReactDOM.createRoot()` (React 18+)

Modern React applications use:

```tsx
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root")!);
root.render(<App />);
```

Characteristics:

- Creates a **Concurrent Root**.
- Enables React 18 features automatically.
- Better scheduling allows React to prioritize urgent UI updates.
- Supports automatic batching across more asynchronous boundaries.
- Works seamlessly with modern APIs like `startTransition`.

---

## Key Differences

| Feature               | `ReactDOM.render()`  | `createRoot()`          |
| --------------------- | -------------------- | ----------------------- |
| React version         | React 17 and earlier | React 18+               |
| Root type             | Legacy Root          | Concurrent Root         |
| Concurrent rendering  | ❌                   | ✅                      |
| Automatic batching    | Limited              | ✅ Across async updates |
| Future React features | ❌                   | ✅                      |
| Recommended today     | ❌ No                | ✅ Yes                  |

---

## React 18 Rendering Behavior

With `createRoot()`, React can interrupt low-priority rendering work to keep the UI responsive.

Example:

```tsx
startTransition(() => {
  setSearchQuery(value);
});
```

The user can continue typing while React schedules the expensive update appropriately.

---

# Example

## Create a Vite React + TypeScript project

```bash
npm create vite@latest react-create-root-demo -- --template react-ts
cd react-create-root-demo
npm install
npm run dev
```

### `main.tsx`

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const container = document.getElementById("root");

if (!container) {
  throw new Error("Root element not found");
}

const root = ReactDOM.createRoot(container);

root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
);
```

This is the standard entry point generated by modern Vite React projects.

---

# Tooling & Setup

- **Preferred:** Vite + React + TypeScript.
- Avoid **Create React App (CRA)** because it is deprecated.
- Vite uses **ES Modules (ESM)** for fast startup and Hot Module Replacement (HMR).
- Use:
  - **Vite** for SPAs.
  - **Next.js** when SSR, SSG, or React Server Components are needed.
  - **Remix** for nested routing and server-centric data loading.
  - **Turbopack** with modern Next.js development.

---

# Performance

Using `createRoot()` enables React 18 optimizations:

- ✅ Concurrent rendering
- ✅ Automatic batching of state updates
- ✅ Better scheduling of expensive renders
- ✅ Improved UI responsiveness
- Profile rendering with React DevTools Profiler.
- Continue using `React.memo`, `useMemo`, and `useCallback` to avoid unnecessary re-renders.
- Use `React.lazy` and `Suspense` for code splitting.
- Cache server state with libraries like TanStack Query or SWR.

---

# Testing

Use **Vitest + React Testing Library**.

Example command:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Basic test:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders app", () => {
  render(<App />);
  expect(screen.getByText(/hello/i)).toBeInTheDocument();
});
```

Use Playwright for end-to-end testing.

---

# Ops & Deployment

- Always initialize applications with `createRoot()` for React 18+.
- Wrap the app in `React.StrictMode` during development to catch side effects and deprecated patterns.
- Use Error Boundaries to isolate rendering failures.
- Optimize bundles with dynamic imports and tree shaking.
- Deploy Vite builds to a CDN or edge network for fast static asset delivery.

---

# Pitfalls

- Continuing to use `ReactDOM.render()` in React 18+ prevents access to concurrent features.
- Forgetting to import from **`react-dom/client`** when using `createRoot()`.
- Assuming concurrent rendering means all rendering is asynchronous—it is cooperative and controlled by React's scheduler.

## Question 4. How do you prevent a component from rendering?

## Question 5. What is the difference between local and global state in React?

## Question 6. Can you update state directly without setState? Why not?

## Question 7. How do you handle multiple inputs in a form?

## Question 8. How do you pass functions as props?

## Question 9. How do you conditionally render components using logical operators?

## Question 10. How do you debug React component lifecycle issues?

## Question 11. What is the difference between a controlled input and a read-only input?

## Question 12. How do you handle component cleanup in functional components?

## Question 13. How do you update nested state objects?

## Question 14. What is the difference between functional and arrow functions in event handlers?

## Question 15. How do you prevent unnecessary re-rendering of child components?

## Question 16. Explain useImperativeHandle hook

## Question 17. How does React.memo work for functional components?

## Question 18. Difference between useEffect and useLayoutEffect

## Question 19. How do you cancel async operations in useEffect?

## Question 20. How do you implement a custom hook? Give an example
