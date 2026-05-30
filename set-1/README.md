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

## Question 5. What are React components? Difference between class and functional components?

## Question 6. What is the difference between props and state in React?

## Question 7. How do you pass data from parent to child component?

## Question 8. What are default props?

## Question 9. How do you handle events in React? Give examples

## Question 10. What is conditional rendering? Give examples

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
