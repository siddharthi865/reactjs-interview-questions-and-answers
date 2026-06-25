# Set 1

| #   | Question                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is ReactJS and why is it used?](#question-1-what-is-reactjs-and-why-is-it-used)                                                                                      |
| 2   | [What are the key features of React?](#question-2-what-are-the-key-features-of-react)                                                                                      |
| 3   | [What is JSX? Why do we use it?](#question-3-what-is-jsx-why-do-we-use-it)                                                                                                 |
| 4   | [Explain the Virtual DOM. How is it different from the real DOM?](#question-4-explain-the-virtual-dom-how-is-it-different-from-the-real-dom)                               |
| 5   | [What are React components? Difference between class and functional components?](#question-5-what-are-react-components-difference-between-class-and-functional-components) |
| 6   | [What is the difference between props and state in React?](#question-6-what-is-the-difference-between-props-and-state-in-react)                                            |
| 7   | [How do you pass data from parent to child component?](#question-7-how-do-you-pass-data-from-parent-to-child-component)                                                    |
| 8   | [What are default props?](#question-8-what-are-default-props)                                                                                                              |
| 9   | [How do you handle events in React? Give examples](#question-9-how-do-you-handle-events-in-react-give-examples)                                                            |
| 10  | [What is conditional rendering? Give examples](#question-10-what-is-conditional-rendering-give-examples)                                                                   |
| 11  | [What are lists and keys in React? Why are keys important?](#question-11-what-are-lists-and-keys-in-react-why-are-keys-important)                                          |
| 12  | [How do you handle forms in React?](#question-12-how-do-you-handle-forms-in-react)                                                                                         |
| 13  | [What is controlled vs uncontrolled component?](#question-13-what-is-controlled-vs-uncontrolled-component)                                                                 |
| 14  | [How do you lift state up in React?](#question-14-how-do-you-lift-state-up-in-react)                                                                                       |
| 15  | [What are React fragments?](#question-15-what-are-react-fragments)                                                                                                         |
| 16  | [What is React strict mode?](#question-16-what-is-react-strict-mode)                                                                                                       |
| 17  | [How do you prevent component re-rendering?](#question-17-how-do-you-prevent-component-re-rendering)                                                                       |
| 18  | [Explain the difference between stateful and stateless components](#question-18-explain-the-difference-between-stateful-and-stateless-components)                          |
| 19  | [What is the difference between function and arrow function components?](#question-19-what-is-the-difference-between-function-and-arrow-function-components)               |
| 20  | [How do you apply inline styles in React?](#question-20-how-do-you-apply-inline-styles-in-react)                                                                           |

## Question 1. What is ReactJS and why is it used?

## Short answer

ReactJS is a JavaScript library for building fast, interactive user interfaces using a component-based architecture. It is used because it enables efficient UI updates through a virtual DOM, reusable components, and predictable state-driven rendering.

---

## Explanation

ReactJS (developed by Meta) is primarily focused on building **declarative, component-based UIs**. Instead of manually manipulating the DOM, you describe _what the UI should look like for a given state_, and React handles updating the DOM efficiently.

### Key architectural ideas

**1. Component-based architecture**

- UI is split into reusable, isolated components
- Encourages separation of concerns and scalability in large apps
- Components can be functional (modern standard) or class-based (legacy)

**2. Declarative rendering**

- You describe UI as a function of state: `UI = f(state)`
- React automatically re-renders when state changes

**3. Virtual DOM & reconciliation**

- React keeps a lightweight in-memory representation of the DOM
- On state change, it computes a diff (reconciliation)
- Only minimal DOM updates are applied → improves performance

**4. React 18 concurrency model**

- Supports concurrent rendering (non-blocking UI updates)
- Automatic batching of state updates improves performance
- Features like `startTransition` allow prioritizing urgent vs non-urgent updates

---

### Why React is used in real-world applications

**1. Scalability**

- Large applications (Meta, Netflix, Airbnb) rely on component modularity

**2. Performance optimization**

- Virtual DOM + diffing reduces expensive DOM operations
- Supports memoization strategies (`React.memo`, `useMemo`, `useCallback`)

**3. Strong ecosystem**

- Works well with Next.js, Remix, Vite
- Rich state management options (Redux, Zustand, React Query)

**4. Developer experience**

- JSX makes UI logic and markup co-located
- Hot module replacement via modern tooling (Vite, Next.js)

**5. Cross-platform capability**

- React Native enables mobile app development using same principles

---

## Example

### Setup (Vite + React + TypeScript)

```bash
npm create vite@latest react-intro -- --template react-ts
cd react-intro
npm install
npm run dev
```

---

### Simple React component

```tsx
import { useState } from "react";

type CounterProps = {
  initialValue?: number;
};

export default function Counter({ initialValue = 0 }: CounterProps) {
  const [count, setCount] = useState(initialValue);

  return (
    <div>
      <h1>Counter: {count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>

      <button onClick={() => setCount((c) => c - 1)}>Decrement</button>
    </div>
  );
}
```

---

## Tooling & Setup

- **Recommended stack**: Vite + React + TypeScript
  - Faster dev server (ESM-based, no bundling in dev)
  - Better HMR than legacy CRA

- Alternative:
  - Next.js (SSR/SSG, full-stack React)
  - Remix (server-first architecture)

**Why not CRA?**

- Deprecated and slower build tooling
- Lacks modern ESM-first architecture

---

## Performance

Key optimization strategies:

- `React.memo` → prevent unnecessary re-renders
- `useMemo` → memoize expensive computations
- `useCallback` → stable function references
- Code splitting: `React.lazy` + `Suspense`
- Use React DevTools Profiler to detect wasted renders
- Use concurrent features (`startTransition`) for non-urgent updates

---

## Testing

- Unit & integration: **Vitest + React Testing Library**
- E2E: **Playwright**

Example:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Basic test:

```tsx
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders counter", () => {
  render(<Counter initialValue={0} />);
  expect(screen.getByText(/Counter:/)).toBeInTheDocument();
});
```

---

## Ops & Deployment

- SSR vs CSR:
  - CSR (Vite): fast client apps, simpler deployment
  - SSR (Next.js): better SEO, faster initial load

- Use CDN for static assets (Vercel, Netlify, Cloudflare)
- Error boundaries for runtime safety
- Monitor bundle size with `vite-bundle-analyzer`
- Logging via services like Sentry

---

## Pitfalls

- Overusing state → unnecessary re-renders and complexity
- Ignoring dependency arrays in hooks
- Premature optimization without profiling
- Using global state when local state is sufficient

## Question 2. What are the key features of React?

# Key Features of React

## Short answer

React's key features include **component-based architecture, declarative UI, Virtual DOM, JSX, one-way data binding, Hooks, efficient reconciliation, React 18 concurrent rendering, and a rich ecosystem**. These features make React scalable, performant, and maintainable for modern web applications.

---

# Explanation

## 1. Component-Based Architecture

React applications are built using **reusable, independent components**.

Each component encapsulates:

- UI
- State
- Business logic
- Styling (optional)

Example hierarchy:

```
App
├── Header
├── Sidebar
├── ProductList
│     ├── ProductCard
│     ├── ProductCard
│     └── ProductCard
└── Footer
```

### Benefits

- Reusability
- Easier maintenance
- Better testing
- Separation of concerns

---

## 2. Declarative UI

Instead of manually manipulating the DOM, React lets you describe **what the UI should look like** based on the current state.

Instead of:

```javascript
document.getElementById("count").innerText = count;
```

React:

```tsx
<h1>{count}</h1>
```

When `count` changes, React updates the UI automatically.

---

## 3. Virtual DOM

React maintains an **in-memory representation** of the DOM called the **Virtual DOM**.

Workflow:

```
State changes
      ↓
Virtual DOM updated
      ↓
Diff (Reconciliation)
      ↓
Only changed nodes updated
      ↓
Real DOM
```

Benefits:

- Faster updates
- Less DOM manipulation
- Better performance

---

## 4. JSX

JSX allows writing HTML-like syntax inside JavaScript/TypeScript.

Example:

```tsx
function Welcome() {
  return <h1>Hello React</h1>;
}
```

JSX is compiled into:

```tsx
React.createElement(...)
```

Advantages:

- Readable
- Easier UI development
- JavaScript expressions inside markup

---

## 5. One-Way Data Flow

React follows **unidirectional data flow**.

```
Parent
   │
 props
   │
Child
```

Benefits:

- Predictable updates
- Easier debugging
- Better state management

---

## 6. Hooks

Hooks allow functional components to use React features such as state and lifecycle behavior.

Common Hooks:

- `useState`
- `useEffect`
- `useMemo`
- `useCallback`
- `useRef`
- `useContext`
- `useReducer`

Benefits:

- Less boilerplate
- Reusable logic through custom Hooks
- Cleaner code than class components

---

## 7. Efficient Reconciliation

React compares the previous and new Virtual DOM trees to determine the minimal changes needed.

React uses:

- Keys for list reconciliation
- Component identity
- Fiber architecture

This avoids unnecessary DOM updates.

---

## 8. React 18 Concurrent Rendering

React 18 introduced concurrent rendering capabilities that allow rendering work to be interrupted and prioritized, helping keep applications responsive.

Features include:

- Automatic batching
- `startTransition`
- `useTransition`
- `useDeferredValue`

Example:

```tsx
startTransition(() => {
  setSearchResults(data);
});
```

This improves perceived performance for large updates.

---

## 9. Automatic Batching

React 18 batches multiple state updates—even across asynchronous boundaries like promises and timeouts—into a single render where appropriate.

Instead of:

```
Render
Render
Render
```

React performs:

```
Multiple updates
      ↓
Single render
```

This reduces unnecessary re-renders.

---

## 10. Rich Ecosystem

React integrates well with modern tools and libraries.

Examples:

- Routing → React Router
- State → Redux Toolkit, Zustand
- Server state → TanStack Query
- Forms → React Hook Form
- SSR → Next.js
- Full-stack → Remix

---

## Example

### Setup (Vite + React + TypeScript)

```bash
npm create vite@latest react-features -- --template react-ts
cd react-features
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { useState, useMemo } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  const doubled = useMemo(() => count * 2, [count]);

  return (
    <main>
      <h1>Count: {count}</h1>
      <p>Doubled: {doubled}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </main>
  );
}
```

This example demonstrates:

- Functional component
- JSX
- `useState`
- Declarative rendering
- `useMemo` for memoization
- Automatic UI updates

---

# Tooling & Setup

**Recommended:** Vite + React + TypeScript

```bash
npm create vite@latest react-features -- --template react-ts
```

Why Vite?

- Extremely fast dev server
- Native ESM during development
- Excellent Hot Module Replacement (HMR)
- Smaller configuration surface

Framework choices:

- **Vite**: Client-side React apps
- **Next.js**: SSR, SSG, App Router, Server Components
- **Remix**: Server-first routing and data loading

**ESM vs CommonJS**

- Prefer **ES Modules (ESM)** (`import`/`export`) for modern React projects.
- Bundlers like Vite use ESM natively in development, improving startup time and tree shaking.

Avoid Create React App (CRA), as it is deprecated in favor of modern tooling.

---

# Performance

- Use **React DevTools Profiler** to identify expensive renders.
- Memoize expensive calculations with `useMemo`.
- Memoize callbacks with `useCallback` when passing them to memoized children.
- Prevent unnecessary re-renders with `React.memo`.
- Use `React.lazy` and `Suspense` for code splitting.
- Cache server data with libraries like TanStack Query to reduce duplicate network requests.
- Virtualize long lists with libraries such as `react-window` or `react-virtualized`.

---

# Testing

Recommended tools:

- **Vitest** + React Testing Library for unit/integration tests
- **Playwright** for end-to-end testing

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders count", () => {
  render(<App />);
  expect(screen.getByText(/Count:/)).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Use **Error Boundaries** to gracefully handle rendering errors.
- Add structured logging and monitoring (e.g., Sentry) for production issues.
- Choose **CSR** (Vite) for highly interactive apps and **SSR/SSG** (Next.js) for SEO and faster first paint.
- Analyze bundle size with tools like `rollup-plugin-visualizer` or `vite-bundle-analyzer`.
- Deploy static assets behind a CDN (e.g., Vercel, Netlify, Cloudflare) for low-latency delivery.

---

# Pitfalls

- **Overusing memoization:** `useMemo` and `useCallback` add complexity; use them only when profiling shows a benefit.
- **Mutating state directly:** Always create new objects/arrays so React can detect changes.
- **Missing or unstable keys in lists:** Avoid using array indexes as keys when items can be reordered or removed.

## Question 3. What is JSX? Why do we use it?

# JSX

## Short answer

**JSX (JavaScript XML)** is a syntax extension for JavaScript that allows you to write HTML-like markup inside JavaScript/TypeScript. React uses JSX to describe what the UI should look like. JSX improves readability, developer productivity, and enables powerful integration of JavaScript logic with UI.

---

# Explanation

## What is JSX?

JSX lets you write UI in a syntax that closely resembles HTML while still being JavaScript.

Example:

```tsx
function App() {
  return <h1>Hello, React!</h1>;
}
```

Although it looks like HTML, JSX is **not HTML**. It is syntactic sugar that is transformed during compilation into JavaScript function calls.

For example:

```tsx
const element = <h1>Hello, React!</h1>;
```

is compiled into something conceptually similar to:

```tsx
import { jsx as _jsx } from "react/jsx-runtime";

const element = _jsx("h1", {
  children: "Hello, React!",
});
```

> In modern React (17+), you no longer need to explicitly `import React` just to use JSX because the new JSX Transform handles this automatically.

---

## Why do we use JSX?

### 1. Improves Readability

JSX makes UI code easier to understand because it resembles the structure of the rendered page.

Without JSX:

```tsx
React.createElement("div", null, React.createElement("h1", null, "Welcome"));
```

With JSX:

```tsx
<div>
  <h1>Welcome</h1>
</div>
```

The JSX version is much easier to read and maintain.

---

### 2. JavaScript Inside UI

JSX allows embedding JavaScript expressions using `{}`.

```tsx
const name = "Alice";

function App() {
  return <h1>Hello, {name}</h1>;
}
```

You can use:

- Variables
- Function calls
- Ternary operators
- Array methods like `map()`
- Arithmetic expressions

Example:

```tsx
const isLoggedIn = true;

return <h1>{isLoggedIn ? "Dashboard" : "Login"}</h1>;
```

---

### 3. Component Composition

JSX makes it simple to compose reusable components.

```tsx
function Header() {
  return <h2>Header</h2>;
}

function App() {
  return (
    <>
      <Header />
      <p>Main content</p>
    </>
  );
}
```

This promotes modular and maintainable application architecture.

---

### 4. Declarative UI

JSX works naturally with React's declarative programming model.

Instead of manually updating the DOM:

```javascript
document.getElementById("status").textContent = "Loading...";
```

React:

```tsx
<p>{loading ? "Loading..." : "Loaded"}</p>
```

The UI automatically updates when the state changes.

---

### 5. Type Safety (with TypeScript)

JSX integrates well with TypeScript, providing compile-time checking for props and component usage.

```tsx
type ButtonProps = {
  label: string;
};

function Button({ label }: ButtonProps) {
  return <button>{label}</button>;
}
```

---

## JSX Rules

### Return a single parent element

✅

```tsx
return (
  <div>
    <h1>Hello</h1>
    <p>Welcome</p>
  </div>
);
```

Or use a Fragment:

```tsx
return (
  <>
    <h1>Hello</h1>
    <p>Welcome</p>
  </>
);
```

---

### Close all tags

```tsx
<img src="logo.png" alt="Logo" />
<input />
```

---

### Use `className` instead of `class`

```tsx
<div className="container">Hello</div>
```

---

### Use camelCase for DOM attributes

```tsx
<button onClick={handleClick}>Click</button>
```

Examples:

- `onClick`
- `tabIndex`
- `htmlFor`

---

## React 18 Considerations

JSX itself is syntax and does not change rendering behavior, but it integrates seamlessly with React 18 features such as:

- Automatic batching
- Concurrent rendering
- Suspense
- Server Components (frameworks like Next.js App Router)

React still reconciles the JSX output using the Virtual DOM and Fiber architecture to efficiently update the real DOM.

---

# Example

### Setup (Vite + React + TypeScript)

```bash
npm create vite@latest jsx-demo -- --template react-ts
cd jsx-demo
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { useState } from "react";

type User = {
  name: string;
};

export default function App() {
  const [count, setCount] = useState(0);

  const user: User = {
    name: "Alice",
  };

  return (
    <main>
      <h1>Hello, {user.name}</h1>

      <p>Count: {count}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>

      {count >= 5 && <p>Great job!</p>}
    </main>
  );
}
```

This example demonstrates:

- JSX syntax
- JavaScript expressions in JSX
- Conditional rendering
- Functional component
- TypeScript props and types
- Declarative state-driven UI

---

# Tooling & Setup

- **Recommended stack:** Vite + React + TypeScript for fast development and native ESM support.
- **Alternative frameworks:**
  - **Next.js** for SSR, SSG, and React Server Components.
  - **Remix** for server-first routing and data loading.

- **ESM vs CommonJS:**
  - Modern React projects use **ES Modules** (`import`/`export`), which Vite serves directly during development for faster startup and effective tree shaking.

- **Avoid Create React App (CRA):**
  - CRA is deprecated. Prefer Vite or framework-based solutions for modern projects.

---

# Performance

Although JSX itself doesn't optimize performance, use these React techniques:

- Profile renders with **React DevTools Profiler**.
- Use `React.memo` to avoid unnecessary child re-renders.
- Use `useMemo` for expensive calculations.
- Use `useCallback` for stable callback references.
- Use `React.lazy` and `Suspense` for code splitting.
- Cache server state with libraries like TanStack Query to reduce unnecessary network requests.

---

# Testing

Use **Vitest** and **React Testing Library** for unit and integration testing, and **Playwright** for end-to-end tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders greeting", () => {
  render(<App />);
  expect(screen.getByText(/Hello, Alice/i)).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Use **Error Boundaries** to catch rendering errors in production.
- Add centralized logging and monitoring (e.g., Sentry).
- Choose **CSR** (Vite) for highly interactive applications and **SSR/SSG** (Next.js) when SEO or faster initial rendering is important.
- Monitor bundle size with tools like `rollup-plugin-visualizer`.
- Deploy static assets via a CDN (e.g., Vercel, Netlify, Cloudflare) for improved performance.

---

# Pitfalls

- **Using `class` instead of `className`:** JSX follows JavaScript naming conventions.
- **Returning multiple sibling elements without a wrapper:** Wrap them in a parent element or a Fragment (`<>...</>`).
- **Embedding statements instead of expressions:** JSX supports expressions (e.g., ternary operators), but not statements like `if` or `for` directly inside markup.

## Question 4. Explain the Virtual DOM. How is it different from the real DOM?

# Virtual DOM vs Real DOM

## Short answer

The **Virtual DOM** is a lightweight in-memory representation of the real DOM used by React to efficiently update the UI. Instead of directly manipulating the browser DOM on every state change, React updates the Virtual DOM, compares it with the previous version (diffing), and applies only the minimal necessary changes to the real DOM.

---

# Explanation

## 1. What is the Real DOM?

The **Real DOM (Document Object Model)** is the browser’s actual tree structure representing the UI.

### Characteristics:

- Directly manipulated by the browser
- Each update can be expensive (reflows, repaints)
- Slow for frequent UI updates
- DOM changes can cascade and trigger layout recalculations

### Example:

```javascript id="3y3q9m"
document.getElementById("title").innerText = "Hello";
```

This directly updates the real DOM.

---

## 2. What is the Virtual DOM?

The **Virtual DOM (VDOM)** is a JavaScript object representation of the real DOM.

React keeps a copy of the UI in memory and updates this virtual tree first.

### Workflow:

```text id="vdom-flow"
State Change
   ↓
New Virtual DOM Tree
   ↓
Diffing (Reconciliation)
   ↓
Minimal updates calculated
   ↓
Real DOM updated efficiently
```

---

## 3. How React uses Virtual DOM

React follows a 3-step process:

### Step 1: Render Virtual DOM

When state changes:

```tsx id="v1"
setCount(count + 1);
```

React creates a new Virtual DOM tree.

---

### Step 2: Diffing (Reconciliation)

React compares:

- Previous Virtual DOM
- New Virtual DOM

It identifies **what changed** using a diffing algorithm.

Key optimizations:

- Element type comparison
- Key-based list reconciliation
- Component identity tracking

---

### Step 3: Batch update to Real DOM

Only the changed nodes are updated in the real DOM.

---

## 4. Key Differences: Virtual DOM vs Real DOM

| Feature        | Real DOM               | Virtual DOM             |
| -------------- | ---------------------- | ----------------------- |
| Nature         | Browser representation | JavaScript object       |
| Performance    | Slower updates         | Faster updates          |
| Update type    | Direct manipulation    | Diff + batch update     |
| Cost of update | High (reflow/repaint)  | Low (in-memory diffing) |
| Usage          | Native browser API     | React abstraction       |

---

## 5. Why Virtual DOM is faster (in practice)

The Virtual DOM improves performance because:

### 1. Batch updates

React groups multiple state updates (React 18 automatic batching):

```text id="batch"
setState → setState → setState
          ↓
     Single render cycle
```

---

### 2. Minimal DOM operations

Instead of updating entire UI:

❌ Inefficient:

- Re-render full page

✅ Efficient:

- Update only changed text/node

---

### 3. Reconciliation optimizations

React uses:

- **Keys in lists**
- **Fiber architecture**
- **Heuristic diffing**

This avoids full tree comparison.

---

## 6. React 18 impact (important for interviews)

With React 18:

### Concurrent rendering

- Rendering can be paused and resumed
- UI remains responsive during heavy updates

### Automatic batching

- State updates inside promises/timeouts are batched

### Transition API

```tsx id="transition"
import { startTransition } from "react";

startTransition(() => {
  setSearchResults(data);
});
```

This tells React:

> "This update is non-urgent"

---

## 7. Example

### Setup (Vite + React + TypeScript)

```bash id="setup-vdom"
npm create vite@latest vdom-demo -- --template react-ts
cd vdom-demo
npm install
npm run dev
```

---

### Example Component

```tsx id="example-vdom"
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  console.log("Component re-rendered");

  return (
    <div>
      <h1>Count: {count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### What happens internally:

1. Button click triggers state update
2. React creates new Virtual DOM
3. Diff algorithm compares previous and new trees
4. Only `<h1>` text node updates in real DOM

---

## 8. Tooling & Setup

- **Vite (recommended)**: Fast ESM-based dev server, instant HMR
- **Next.js**: Adds SSR + Server Components
- **React DevTools Profiler**: Inspects virtual DOM rendering behavior

Why not CRA:

- Deprecated
- Slower bundling
- Not optimized for modern ESM workflows

---

## 9. Performance considerations

Even though Virtual DOM improves performance, it is not “free”:

### Optimization strategies:

- Use `React.memo` to prevent unnecessary re-renders
- Use `useMemo` for expensive computations
- Use `useCallback` for stable function references
- Avoid unnecessary state updates
- Use list keys properly (avoid index keys in dynamic lists)
- Use virtualization for large lists (`react-window`)

### Profiling tools:

- React DevTools Profiler
- Chrome Performance tab

---

## 10. Testing

Using **Vitest + React Testing Library**:

```bash id="test-vdom"
npm install -D vitest @testing-library/react jsdom
```

```tsx id="test-example"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders counter", () => {
  render(<App />);
  expect(screen.getByText(/Count:/)).toBeInTheDocument();
});
```

---

## 11. Ops & Deployment

- Use error boundaries to catch rendering issues
- Monitor render performance in production (Sentry, Datadog)
- Code split with `React.lazy`
- Deploy via CDN (Vercel, Netlify, Cloudflare)
- Prefer SSR (Next.js) for SEO-heavy apps

---

## 12. Common pitfalls

- Assuming Virtual DOM eliminates all re-renders (it doesn’t)
- Not using keys properly in lists → causes incorrect diffing
- Over-optimizing with memoization → can hurt performance
- Direct state mutation breaks diffing logic

## Question 5. What are React components? Difference between class and functional components?

# React Components & Class vs Functional Components

## Short answer

**React components** are independent, reusable building blocks of a UI. They can be written as **class components** or **functional components**. Today, functional components with Hooks are the standard because they are simpler, more performant, and better aligned with modern React (React 16.8+ and React 18+).

---

# Explanation

## 1. What are React components?

A **React component** is a JavaScript/TypeScript function or class that:

- Returns UI (JSX)
- Accepts inputs called **props**
- Maintains internal state (optional)
- Can be reused across the application

### Types of components:

- **Functional Components (modern standard)**
- **Class Components (legacy, still supported)**

---

## 2. Functional Components

Functional components are **plain JavaScript functions** that return JSX.

### Example:

```tsx id="func1"
type Props = {
  name: string;
};

export default function Greeting({ name }: Props) {
  return <h1>Hello, {name}</h1>;
}
```

### With state (Hooks):

```tsx id="func2"
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

---

## 3. Class Components

Class components are ES6 classes that extend `React.Component`.

### Example:

```tsx id="class1"
import React from "react";

type Props = {
  name: string;
};

type State = {
  count: number;
};

class Counter extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <h1>Hello, {this.props.name}</h1>
        <p>{this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}

export default Counter;
```

---

## 4. Key Differences

| Feature           | Functional Components            | Class Components                                |
| ----------------- | -------------------------------- | ----------------------------------------------- |
| Syntax            | Functions                        | ES6 Classes                                     |
| State management  | Hooks (`useState`, `useReducer`) | `this.state`, `setState`                        |
| Lifecycle methods | `useEffect`                      | `componentDidMount`, `componentDidUpdate`, etc. |
| Boilerplate       | Minimal                          | Verbose                                         |
| `this` keyword    | Not required                     | Required and error-prone                        |
| Performance       | Slightly better (less overhead)  | Slightly heavier                                |
| Current usage     | Recommended (modern React)       | Legacy (rare in new codebases)                  |

---

## 5. Lifecycle Handling

### Class components:

```tsx id="lifecycle-class"
componentDidMount() {
  console.log("Mounted");
}

componentDidUpdate() {
  console.log("Updated");
}

componentWillUnmount() {
  console.log("Cleanup");
}
```

### Functional components:

```tsx id="lifecycle-func"
import { useEffect } from "react";

useEffect(() => {
  console.log("Mounted");

  return () => {
    console.log("Cleanup");
  };
}, []);
```

---

## 6. React 18 Behavior

Both component types work with:

- Concurrent rendering
- Automatic batching
- Suspense
- Transitions (`startTransition`)

However:

- Functional components integrate naturally with modern concurrent features
- Class components are not deprecated but are not evolving with new React APIs

---

## 7. Why functional components are preferred

### 1. Hooks simplify logic reuse

Custom hooks replace HOCs and render props:

```tsx id="hook1"
function useCounter() {
  const [count, setCount] = useState(0);
  return { count, setCount };
}
```

---

### 2. Less boilerplate

No constructors, no `this`, no binding methods.

---

### 3. Better composition

Logic can be split into reusable hooks.

---

### 4. Future-proof

React's modern features are built around functional components:

- Server Components (Next.js)
- Suspense
- Concurrent rendering APIs

---

## 8. Example (Modern Setup)

### Setup (Vite + React + TypeScript)

```bash id="setup-components"
npm create vite@latest react-components -- --template react-ts
cd react-components
npm install
npm run dev
```

---

### Functional Component Example

```tsx id="func-example"
import { useState } from "react";

type Props = {
  title: string;
};

export default function Counter({ title }: Props) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>{title}</h1>
      <p>Count: {count}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

---

### Class Component Equivalent

```tsx id="class-example"
import React from "react";

type Props = {
  title: string;
};

type State = {
  count: number;
};

class Counter extends React.Component<Props, State> {
  state: State = {
    count: 0,
  };

  render() {
    return (
      <div>
        <h1>{this.props.title}</h1>
        <p>Count: {this.state.count}</p>

        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}

export default Counter;
```

---

## 9. Performance considerations

- Functional components are generally more optimized with modern React
- React 18 concurrency features work best with function components
- Use:
  - `React.memo` for memoization
  - `useMemo` for expensive computations
  - `useCallback` for stable handlers

- Avoid unnecessary re-renders by structuring components properly

---

## 10. Testing

Recommended stack:

- **Vitest + React Testing Library**

```bash id="test-components"
npm install -D vitest @testing-library/react jsdom
```

### Example test:

```tsx id="test-comp"
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders counter title", () => {
  render(<Counter title="Demo" />);
  expect(screen.getByText("Demo")).toBeInTheDocument();
});
```

---

## 11. Ops & Deployment

- Prefer functional components for new applications
- Use error boundaries (still class-based in many cases)
- Combine with:
  - Next.js for SSR/SSG
  - Vite for SPA apps

- Monitor render performance using React DevTools Profiler
- Deploy via CDN (Vercel, Netlify, Cloudflare)

---

## 12. Common pitfalls

- Using class components in new projects unnecessarily
- Misusing `this` in class components (binding issues)
- Overusing `useEffect` instead of proper state design
- Mixing patterns inconsistently in large codebases

## Question 6. What is the difference between props and state in React?

# Props vs State in React

## Short answer

**Props** are read-only inputs passed from a parent component to a child, while **state** is internal, mutable data managed within a component that can change over time and triggers re-rendering when updated.

---

# Explanation

## 1. What are Props?

**Props (properties)** are used to pass data from one component to another, typically from parent → child.

### Key idea:

> Props are immutable (read-only) inside the receiving component.

### Example:

```tsx id="props1"
type Props = {
  name: string;
};

export default function Greeting({ name }: Props) {
  return <h1>Hello, {name}</h1>;
}
```

### Usage:

```tsx id="props2"
<Greeting name="Alice" />
```

### Characteristics of Props:

- Passed from parent to child
- Read-only
- Used for configuration and data flow
- Enable component reusability

---

## 2. What is State?

**State** is data managed inside a component that can change over time.

### Key idea:

> State is mutable and triggers re-render when updated.

### Example:

```tsx id="state1"
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>{count}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

### Characteristics of State:

- Managed inside a component
- Mutable via `setState` / `useState`
- Triggers re-render on update
- Used for dynamic UI behavior

---

## 3. Key Differences

| Feature           | Props                 | State                        |
| ----------------- | --------------------- | ---------------------------- |
| Definition        | Input to a component  | Internal data of a component |
| Mutability        | Immutable (read-only) | Mutable                      |
| Ownership         | Parent component      | Component itself             |
| Purpose           | Pass data down        | Manage dynamic behavior      |
| Re-render trigger | Parent change         | State update                 |
| Direction         | Top-down              | Internal                     |

---

## 4. Data Flow Concept

React follows **unidirectional data flow**:

```text id="flow1"
Parent State
     ↓
   Props
     ↓
Child Component
```

Example:

```tsx id="flow2"
function Parent() {
  const [name] = useState("Alice");

  return <Child name={name} />;
}

function Child({ name }: { name: string }) {
  return <h1>{name}</h1>;
}
```

Here:

- Parent owns the data (state)
- Child receives it (props)

---

## 5. When to use Props vs State

### Use Props when:

- Passing data to child components
- Configuring reusable components
- Sharing read-only values

### Use State when:

- Data changes over time
- Handling user interactions
- Managing UI behavior (toggle, form input, modal open/close)

---

## 6. React 18 behavior (important for interviews)

When state or props change:

- React schedules a re-render
- Uses **automatic batching** (React 18)
- Updates Virtual DOM
- Applies minimal changes to real DOM via reconciliation

### Example:

```tsx id="react18"
setCount(1);
setName("John");
// React batches both updates into a single render
```

---

## 7. Example combining Props + State

### Setup (Vite + React + TypeScript)

```bash id="setup-props-state"
npm create vite@latest props-state-demo -- --template react-ts
cd props-state-demo
npm install
npm run dev
```

---

### Example:

```tsx id="example1"
import { useState } from "react";

type ButtonProps = {
  label: string;
  onClick: () => void;
};

function Button({ label, onClick }: ButtonProps) {
  return <button onClick={onClick}>{label}</button>;
}

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Count: {count}</h1>

      <Button label="Increment" onClick={() => setCount((c) => c + 1)} />
    </div>
  );
}
```

### What this demonstrates:

- `count` → state (internal to App)
- `label`, `onClick` → props (passed to Button)
- Clear separation of responsibilities

---

## 8. Performance considerations

- **State changes trigger re-renders** → avoid unnecessary state duplication
- Lift state up only when necessary (avoid prop drilling explosion)
- Use memoization:
  - `React.memo` for props-only components
  - `useMemo` for derived values
  - `useCallback` for stable function props

- Avoid passing new object/array props every render unless needed

---

## 9. Testing

Using **Vitest + React Testing Library**:

```bash id="test-props-state"
npm install -D vitest @testing-library/react jsdom
```

### Example test:

```tsx id="test1"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders count and button", () => {
  render(<App />);
  expect(screen.getByText(/Count:/)).toBeInTheDocument();
  expect(screen.getByRole("button")).toBeInTheDocument();
});
```

---

## 10. Ops & Deployment

- State-heavy apps should be optimized for re-renders (React DevTools Profiler)
- Use state management libraries when props drilling becomes complex:
  - Zustand
  - Redux Toolkit
  - React Context (for lightweight sharing)

- Prefer SSR (Next.js) for SEO-heavy or initial-load-sensitive apps
- Deploy via Vercel/Netlify with CDN caching for static assets
- Use error boundaries to handle runtime failures gracefully

---

## 11. Common pitfalls

- ❌ Mutating props inside a component (React enforces immutability)
- ❌ Overusing state for data that could be props
- ❌ Prop drilling too deeply instead of using context or state libraries
- ❌ Treating state updates as synchronous (they are batched in React 18)

## Question 7. How do you pass data from parent to child component?

# Passing Data from Parent to Child in React

## Short answer

In React, data is passed from a **parent component to a child component using props**. Props are read-only values passed as attributes when rendering the child component.

---

# Explanation

## 1. Core Concept: Unidirectional Data Flow

React follows a **top-down (unidirectional) data flow**:

```text
Parent Component
       ↓ props
Child Component
```

- Parent owns the data
- Child receives data via props
- Child cannot directly modify parent data (it can only request changes via callbacks)

This makes the data flow predictable and easier to debug.

---

## 2. Basic Props Passing

### Parent Component

```tsx id="parent1"
import Child from "./Child";

export default function Parent() {
  const message = "Hello from Parent";

  return <Child text={message} />;
}
```

### Child Component

```tsx id="child1"
type Props = {
  text: string;
};

export default function Child({ text }: Props) {
  return <h1>{text}</h1>;
}
```

---

## 3. Passing Different Types of Data

Props can pass:

- Strings
- Numbers
- Booleans
- Arrays
- Objects
- Functions (callbacks)
- JSX elements

### Example:

```tsx id="types1"
<Child
  name="Alice"
  age={25}
  isLoggedIn={true}
  hobbies={["coding", "music"]}
  user={{ id: 1, role: "admin" }}
/>
```

---

## 4. Passing Functions (Important Pattern)

To allow child → parent communication, you pass a function as a prop.

### Parent

```tsx id="parent2"
import Child from "./Child";

export default function Parent() {
  const handleMessage = (msg: string) => {
    console.log("Child says:", msg);
  };

  return <Child onSendMessage={handleMessage} />;
}
```

### Child

```tsx id="child2"
type Props = {
  onSendMessage: (msg: string) => void;
};

export default function Child({ onSendMessage }: Props) {
  return (
    <button onClick={() => onSendMessage("Hello Parent!")}>Send Message</button>
  );
}
```

### Why this is important:

- Enables child-to-parent communication
- Used in forms, modals, event handling, etc.

---

## 5. Passing JSX as Props (Composition Pattern)

React allows passing UI as props for flexible layouts.

### Parent

```tsx id="parent3"
import Child from "./Child";

export default function Parent() {
  return (
    <Child>
      <p>This is passed as children</p>
    </Child>
  );
}
```

### Child

```tsx id="child3"
type Props = {
  children: React.ReactNode;
};

export default function Child({ children }: Props) {
  return <div>{children}</div>;
}
```

---

## 6. React 18 Behavior (Important Interview Insight)

When props change:

- Parent re-renders
- Child re-renders (unless memoized)
- React uses **Virtual DOM diffing** to update only changed parts

### Optimization tools:

- `React.memo` → prevents unnecessary child re-renders
- `useCallback` → stabilizes function props
- `useMemo` → stabilizes computed props

---

## 7. Example (Complete)

### Setup (Vite + React + TypeScript)

```bash id="setup1"
npm create vite@latest parent-child-demo -- --template react-ts
cd parent-child-demo
npm install
npm run dev
```

---

### Parent Component

```tsx id="parent4"
import { useState } from "react";
import Child from "./Child";

export default function Parent() {
  const [count, setCount] = useState(0);

  const increment = () => setCount((c) => c + 1);

  return (
    <div>
      <h1>Parent Count: {count}</h1>

      <Child value={count} onIncrement={increment} />
    </div>
  );
}
```

---

### Child Component

```tsx id="child4"
type Props = {
  value: number;
  onIncrement: () => void;
};

export default function Child({ value, onIncrement }: Props) {
  return (
    <div>
      <h2>Child sees value: {value}</h2>

      <button onClick={onIncrement}>Increment from Child</button>
    </div>
  );
}
```

### Key idea:

- Parent owns state
- Child displays and triggers updates
- Data flows down, actions flow up

---

## 8. Performance considerations

- Passing new object/array props every render causes child re-renders
- Use memoization:

```tsx id="memo1"
const memoizedValue = useMemo(() => computeExpensiveValue(), []);
const memoizedFn = useCallback(() => {}, []);
```

- Wrap child in `React.memo`:

```tsx id="memo2"
export default React.memo(Child);
```

- Avoid unnecessary re-renders in large component trees

---

## 9. Testing

Using **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react jsdom
```

### Example test:

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import Parent from "./Parent";

test("passes data to child", () => {
  render(<Parent />);
  expect(screen.getByText(/Child sees value:/)).toBeInTheDocument();
});
```

---

## 10. Ops & Deployment

- Avoid excessive prop drilling in deep component trees
- Use:
  - Context API (lightweight global state)
  - Zustand / Redux Toolkit (complex apps)

- Use React DevTools to inspect prop flow
- Deploy via Vite build (`npm run build`) to CDN (Vercel, Netlify, Cloudflare)
- Add error boundaries for runtime safety

---

## 11. Common pitfalls

- ❌ Mutating props inside child components
- ❌ Overusing prop drilling instead of context/state libraries
- ❌ Passing unstable references (causing unnecessary re-renders)
- ❌ Mixing state ownership between parent and child (confusing data flow)

## Question 8. What are default props?

# Default Props in React

## Short answer

**Default props** are fallback values assigned to component props when no value is provided by the parent. In modern React (functional components), default values are typically handled using **ES6 default parameters** instead of `defaultProps`.

---

# Explanation

## 1. What are Default Props?

Default props ensure that a component behaves correctly even if some props are not passed.

### Example problem:

If a parent forgets to pass a prop, the component might render `undefined`.

```tsx id="bad1"
function Greeting({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

If used like:

```tsx id="bad2"
<Greeting />
```

Output:

```
Hello, undefined ❌
```

---

## 2. Solution: Default Props

### ✅ Modern approach (recommended): Default parameters

```tsx id="default1"
type Props = {
  name?: string;
};

export default function Greeting({ name = "Guest" }: Props) {
  return <h1>Hello, {name}</h1>;
}
```

Now:

```tsx id="default2"
<Greeting />
```

Output:

```
Hello, Guest ✅
```

---

## 3. Alternative (Legacy): defaultProps

Used mainly in **class components** or older React codebases.

```tsx id="legacy1"
type Props = {
  name?: string;
};

function Greeting({ name }: Props) {
  return <h1>Hello, {name}</h1>;
}

Greeting.defaultProps = {
  name: "Guest",
};
```

⚠️ Note:

- Still supported, but **not recommended for functional components**
- Considered legacy in modern React

---

## 4. Default Props in Class Components

```tsx id="class1"
import React from "react";

type Props = {
  name?: string;
};

class Greeting extends React.Component<Props> {
  static defaultProps = {
    name: "Guest",
  };

  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

---

## 5. Default Props with Destructuring (Best Practice)

### Recommended modern pattern:

```tsx id="best1"
type Props = {
  name?: string;
  age?: number;
};

export default function User({ name = "Guest", age = 18 }: Props) {
  return (
    <p>
      {name} is {age} years old
    </p>
  );
}
```

---

## 6. Why Default Props are important

### 1. Prevent undefined UI bugs

Avoid rendering issues like:

- `undefined`
- `null`
- broken layouts

---

### 2. Improve component reusability

Components become self-contained and safe to use.

---

### 3. Better developer experience

Callers don’t need to pass every prop.

---

## 7. React 18 Behavior

Default props are applied:

- During function execution (before rendering)
- Before React reconciliation
- No special runtime cost from React itself

React treats them like normal JavaScript values.

---

## 8. Example (Full setup)

### Setup (Vite + React + TypeScript)

```bash id="setup1"
npm create vite@latest default-props-demo -- --template react-ts
cd default-props-demo
npm install
npm run dev
```

---

### Component Example

```tsx id="example1"
type Props = {
  title?: string;
  count?: number;
};

export default function Card({ title = "Default Title", count = 0 }: Props) {
  return (
    <div>
      <h2>{title}</h2>
      <p>Count: {count}</p>
    </div>
  );
}
```

### Usage:

```tsx id="example2"
<Card />
<Card title="Custom Title" count={5} />
```

---

## 9. Performance considerations

- Default props are simple JS defaults → no performance overhead
- Avoid computing expensive defaults inline unnecessarily:

❌ Bad:

```tsx id="perf1"
function Comp({ data = expensiveCalculation() }) {}
```

✔ Better:

```tsx id="perf2"
const defaultValue = expensiveCalculation();

function Comp({ data = defaultValue }) {}
```

- Works well with memoized components (`React.memo`)

---

## 10. Testing

Using **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react jsdom
```

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import Card from "./Card";

test("renders default values", () => {
  render(<Card />);
  expect(screen.getByText(/Default Title/)).toBeInTheDocument();
});
```

---

## 11. Ops & Deployment

- Default props help reduce runtime errors in production
- Combine with TypeScript optional props (`?`) for safety
- Works well in SSR frameworks (Next.js, Remix)
- Helps prevent hydration mismatches caused by undefined values
- Use error boundaries for fallback UI at a higher level

---

## 12. Common pitfalls

- ❌ Using `defaultProps` in modern functional components (deprecated pattern)
- ❌ Forgetting optional props (`?`) in TypeScript
- ❌ Computing expensive default values inline
- ❌ Assuming default props override explicitly passed `null` (they do not)

## Question 9. How do you handle events in React? Give examples

# Handling Events in React

## Short answer

In React, events are handled using **camelCase event handlers** (like `onClick`, `onChange`) and passing **functions** instead of strings. React wraps native browser events using a **Synthetic Event system** to ensure consistent behavior across browsers.

---

# Explanation

## 1. React Event System

React does not use raw DOM event strings like HTML:

### ❌ HTML-style (not used in React)

```html
<button onclick="handleClick()">Click</button>
```

### ✅ React style

```tsx id="event1"
<button onClick={handleClick}>Click</button>
```

### Key differences:

- Event names are **camelCase** (`onClick`, `onChange`, `onSubmit`)
- You pass a **function reference**, not a string
- React uses a **SyntheticEvent wrapper** for cross-browser consistency

---

## 2. Basic Event Handling

### Example: Click event

```tsx id="click1"
export default function App() {
  function handleClick() {
    console.log("Button clicked!");
  }

  return <button onClick={handleClick}>Click me</button>;
}
```

---

## 3. Inline Event Handlers

You can also define handlers inline:

```tsx id="inline1"
<button onClick={() => console.log("Clicked!")}>Click me</button>
```

⚠️ Use inline handlers carefully (can cause re-renders in large apps)

---

## 4. Passing Parameters to Event Handlers

You must use an arrow function to pass arguments:

```tsx id="param1"
function greet(name: string) {
  console.log("Hello", name);
}

export default function App() {
  return <button onClick={() => greet("Alice")}>Greet Alice</button>;
}
```

---

## 5. Handling Form Events

### Example: onChange + controlled input

```tsx id="form1"
import { useState } from "react";

export default function App() {
  const [value, setValue] = useState("");

  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    setValue(e.target.value);
  }

  return (
    <div>
      <input value={value} onChange={handleChange} />
      <p>You typed: {value}</p>
    </div>
  );
}
```

---

## 6. Form Submission Event

```tsx id="submit1"
function App() {
  function handleSubmit(e: React.FormEvent) {
    e.preventDefault();
    console.log("Form submitted");
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Important:

- `e.preventDefault()` stops page reload

---

## 7. React Synthetic Events

React wraps native browser events into a **SyntheticEvent**.

### Benefits:

- Cross-browser compatibility
- Consistent API
- Event pooling (older React versions)

Example:

```tsx id="synthetic1"
function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
  console.log(e.type); // "click"
  console.log(e.target);
}
```

---

## 8. Common Event Types in React

| Event        | Type                           |
| ------------ | ------------------------------ |
| Click        | `onClick`                      |
| Input change | `onChange`                     |
| Form submit  | `onSubmit`                     |
| Key press    | `onKeyDown`, `onKeyUp`         |
| Focus        | `onFocus`                      |
| Blur         | `onBlur`                       |
| Mouse events | `onMouseEnter`, `onMouseLeave` |

---

## 9. Example: Multiple Events Together

```tsx id="multi1"
import { useState } from "react";

export default function App() {
  const [text, setText] = useState("");

  return (
    <div>
      <input
        value={text}
        onChange={(e) => setText(e.target.value)}
        onFocus={() => console.log("Focused")}
        onBlur={() => console.log("Blurred")}
      />

      <button onClick={() => alert(text)}>Show Text</button>
    </div>
  );
}
```

---

## 10. React 18 Behavior

Event handling in React 18 integrates with:

- **Automatic batching**
  - Multiple state updates inside events are batched

```tsx id="batch1"
function handleClick() {
  setCount((c) => c + 1);
  setName("John");
  // React batches both updates → single re-render
}
```

- **Concurrent rendering**
  - Event updates can be interrupted or prioritized

- **Synthetic event system still applies**, but is now more aligned with native browser behavior (less pooling than older React versions)

---

## 11. Example (Full Setup)

### Setup (Vite + React + TypeScript)

```bash id="setup1"
npm create vite@latest react-events -- --template react-ts
cd react-events
npm install
npm run dev
```

---

### Example Component

```tsx id="example1"
import { useState } from "react";

export default function App() {
  const [message, setMessage] = useState("");

  function handleClick() {
    setMessage("Button clicked!");
  }

  function handleInput(e: React.ChangeEvent<HTMLInputElement>) {
    setMessage(e.target.value);
  }

  return (
    <div>
      <input value={message} onChange={handleInput} />

      <button onClick={handleClick}>Click Me</button>

      <p>{message}</p>
    </div>
  );
}
```

---

## 12. Performance considerations

- Avoid creating inline functions unnecessarily in large lists
- Use `useCallback` to memoize event handlers:

```tsx id="perf1"
const handleClick = useCallback(() => {
  setCount((c) => c + 1);
}, []);
```

- Prevent unnecessary re-renders with `React.memo`
- Avoid heavy computations inside event handlers
- Debounce/throttle input-heavy events (search, scroll)

---

## 13. Testing events

Using **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
```

### Example:

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("handles click event", async () => {
  render(<App />);

  const button = screen.getByText("Click Me");
  await userEvent.click(button);

  expect(screen.getByText(/clicked/i)).toBeInTheDocument();
});
```

---

## 14. Ops & Deployment

- Ensure event handlers are stable to reduce re-renders
- Use error boundaries for event-driven runtime failures
- Monitor performance using React DevTools Profiler
- Optimize large forms with controlled vs uncontrolled inputs
- Deploy via Vite build (`npm run build`) to CDN (Vercel/Netlify/Cloudflare)

---

## 15. Common pitfalls

- ❌ Calling function instead of passing it: `onClick={handleClick()}`
- ❌ Using inline handlers everywhere in large components
- ❌ Forgetting `preventDefault()` in forms
- ❌ Not typing event objects in TypeScript
- ❌ Overusing state updates inside rapid events without debouncing

## Question 10. What is conditional rendering? Give examples

# Conditional Rendering in React

## Short answer

**Conditional rendering** in React means showing or hiding UI elements based on certain conditions (like state, props, or logic). It allows components to render different outputs dynamically depending on application state.

---

# Explanation

## 1. Core idea

In React, UI is a function of state:

```text id="cr1"
UI = f(state)
```

So when state or props change, React decides **what to render using conditions**.

Unlike frameworks with templates, React uses **JavaScript logic directly inside JSX**.

---

## 2. Common ways to do conditional rendering

---

## 2.1 If-else (outside JSX)

Best for complex logic.

```tsx id="cr2"
function App() {
  const isLoggedIn = true;

  if (isLoggedIn) {
    return <h1>Welcome Back!</h1>;
  }

  return <h1>Please Login</h1>;
}
```

---

## 2.2 Ternary operator (inside JSX)

Most commonly used.

```tsx id="cr3"
function App() {
  const isLoggedIn = true;

  return <div>{isLoggedIn ? <h1>Welcome</h1> : <h1>Login</h1>}</div>;
}
```

---

## 2.3 Logical AND (&&)

Used when you want to render **something or nothing**.

```tsx id="cr4"
function App() {
  const showMessage = true;

  return <div>{showMessage && <p>This is a conditional message</p>}</div>;
}
```

### Behavior:

- If `true` → renders element
- If `false` → renders nothing

---

## 2.4 Multiple conditions

```tsx id="cr5"
function App() {
  const status = "loading";

  return (
    <div>
      {status === "loading" && <p>Loading...</p>}
      {status === "success" && <p>Data loaded!</p>}
      {status === "error" && <p>Something went wrong</p>}
    </div>
  );
}
```

---

## 2.5 Switch-case (outside JSX)

Useful for complex state-based rendering.

```tsx id="cr6"
function renderContent(status: string) {
  switch (status) {
    case "loading":
      return <p>Loading...</p>;
    case "success":
      return <p>Success!</p>;
    case "error":
      return <p>Error occurred</p>;
    default:
      return null;
  }
}

export default function App() {
  const status = "loading";

  return <div>{renderContent(status)}</div>;
}
```

---

## 3. Conditional rendering with state

### Example: Toggle UI

```tsx id="cr7"
import { useState } from "react";

export default function App() {
  const [isOpen, setIsOpen] = useState(false);

  return (
    <div>
      <button onClick={() => setIsOpen((prev) => !prev)}>Toggle</button>

      {isOpen ? <p>Modal is Open</p> : <p>Modal is Closed</p>}
    </div>
  );
}
```

---

## 4. Conditional rendering of components

You can conditionally render entire components.

```tsx id="cr8"
function AdminPanel() {
  return <h1>Admin Dashboard</h1>;
}

function UserPanel() {
  return <h1>User Dashboard</h1>;
}

export default function App() {
  const role = "admin";

  return <div>{role === "admin" ? <AdminPanel /> : <UserPanel />}</div>;
}
```

---

## 5. Conditional rendering with null

Returning `null` means “render nothing”.

```tsx id="cr9"
function App() {
  const showHeader = false;

  return <div>{showHeader ? <h1>Header</h1> : null}</div>;
}
```

---

## 6. React 18 behavior

Conditional rendering interacts with:

### 1. Reconciliation

React compares previous and new Virtual DOM and:

- Mounts new elements when condition becomes true
- Unmounts when condition becomes false

### 2. Automatic batching

State updates that affect conditions are batched:

```tsx id="cr10"
setIsLoggedIn(true);
setLoading(false);
// React batches → single re-render
```

### 3. Component lifecycle effects

When conditionally rendering components:

- Mount → `useEffect(() => {}, [])`
- Unmount → cleanup function runs

```tsx id="cr11"
useEffect(() => {
  return () => {
    console.log("Component unmounted");
  };
}, []);
```

---

## 7. Example (Full App)

### Setup (Vite + React + TypeScript)

```bash id="setup1"
npm create vite@latest conditional-demo -- --template react-ts
cd conditional-demo
npm install
npm run dev
```

---

### Example:

```tsx id="cr12"
import { useState } from "react";

export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [loading, setLoading] = useState(false);

  return (
    <div>
      <button onClick={() => setIsLoggedIn((v) => !v)}>Toggle Login</button>

      <button onClick={() => setLoading((v) => !v)}>Toggle Loading</button>

      {loading ? (
        <p>Loading...</p>
      ) : isLoggedIn ? (
        <h1>Welcome User</h1>
      ) : (
        <h1>Please Login</h1>
      )}
    </div>
  );
}
```

---

## 8. Performance considerations

- Avoid complex inline conditions in JSX (extract logic into variables/functions)
- Be careful with deeply nested ternaries (hard to maintain)
- Use memoized components (`React.memo`) to avoid unnecessary re-renders
- Conditional rendering causes mount/unmount cycles → can be expensive if overused
- Use `Suspense` for async conditional UI instead of manual loading flags

---

## 9. Testing conditional rendering

Using **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react jsdom
```

### Example:

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("shows login message", () => {
  render(<App />);
  expect(screen.getByText(/Please Login/i)).toBeInTheDocument();
});
```

---

## 10. Ops & Deployment

- Conditional rendering impacts bundle usage via code splitting (`React.lazy`)
- Use error boundaries to handle failed conditional components
- SSR frameworks (Next.js) may pre-render different UI paths
- Avoid rendering heavy components unnecessarily (performance optimization)
- Monitor UI branching complexity in large apps

---

## 11. Common pitfalls

- ❌ Using `&&` with non-boolean values (e.g., numbers like `0`)
- ❌ Overusing nested ternary operators (hard to read)
- ❌ Forgetting component unmount cleanup in conditional rendering
- ❌ Rendering expensive components without memoization
- ❌ Mixing multiple conditional styles without structure

## Question 11. What are lists and keys in React? Why are keys important?

## Question 12. How do you handle forms in React?

## Question 13. What is controlled vs uncontrolled component?

## Question 14. How do you lift state up in React?

## Question 15. What are React fragments?

## Question 16. What is React strict mode?

## Question 17. How do you prevent component re-rendering?

## Question 18. Explain the difference between stateful and stateless components

## Question 19. What is the difference between function and arrow function components?

## Question 20. How do you apply inline styles in React?
