# Set 6

| #   | Question                                                                                                                                                                                                 |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What are the differences between React and Angular?](#question-1-what-are-the-differences-between-react-and-angular)                                                                                    |
| 2   | [What are the differences between React and Vue.js?](#question-2-what-are-the-differences-between-react-and-vuejs)                                                                                       |
| 3   | [How does React handle DOM updates?](#question-3-how-does-react-handle-dom-updates)                                                                                                                      |
| 4   | [What is the purpose of React.createElement?](#question-4-what-is-the-purpose-of-reactcreateelement)                                                                                                     |
| 5   | [Explain the difference between functional and class components in terms of lifecycle methods](#question-5-explain-the-difference-between-functional-and-class-components-in-terms-of-lifecycle-methods) |
| 6   | [How do you pass multiple props to a child component?](#question-6-how-do-you-pass-multiple-props-to-a-child-component)                                                                                  |
| 7   | [Can you use props to modify parent state? Why or why not?](#question-7-can-you-use-props-to-modify-parent-state-why-or-why-not)                                                                         |
| 8   | [How do you render a component conditionally without using if statements?](#question-8-how-do-you-render-a-component-conditionally-without-using-if-statements)                                          |
| 9   | [What are fragments used for in React?](#question-9-what-are-fragments-used-for-in-react)                                                                                                                |
| 10  | [How do you prevent default form submission in React?](#question-10-how-do-you-prevent-default-form-submission-in-react)                                                                                 |
| 11  | [How do you bind this in class components?](#question-11-how-do-you-bind-this-in-class-components)                                                                                                       |
| 12  | [What is the difference between state updates in class vs functional components?](#question-12-what-is-the-difference-between-state-updates-in-class-vs-functional-components)                           |
| 13  | [What is the difference between React.Component and React.PureComponent?](#question-13-what-is-the-difference-between-reactcomponent-and-reactpurecomponent)                                             |
| 14  | [How do you reuse components in React?](#question-14-how-do-you-reuse-components-in-react)                                                                                                               |
| 15  | [How do you handle errors in event handlers?](#question-15-how-do-you-handle-errors-in-event-handlers)                                                                                                   |
| 16  | [How can you pass children to a component?](#question-16-how-can-you-pass-children-to-a-component)                                                                                                       |
| 17  | [How do you render multiple components inside a parent?](#question-17-how-do-you-render-multiple-components-inside-a-parent)                                                                             |
| 18  | [What is the role of React.StrictMode?](#question-18-what-is-the-role-of-reactstrictmode)                                                                                                                |
| 19  | [Can you return multiple elements from a component? How?](#question-19-can-you-return-multiple-elements-from-a-component-how)                                                                            |
| 20  | [How do you convert class components to functional components?](#question-20-how-do-you-convert-class-components-to-functional-components)                                                               |

## Question 1. What are the differences between React and Angular?

## Short answer

React is a **UI library** focused on building component-based views, while Angular is a **full-fledged framework** that provides a complete opinionated solution (routing, DI, forms, HTTP, etc.) out of the box.

---

## Explanation

### 1. Core philosophy

- **React**
  - Library for building UI
  - Unopinionated → you choose routing, state management, architecture
  - Composition-based (functions + hooks)
  - React 18 introduces concurrent rendering, automatic batching

- **Angular**
  - Full MVC-ish framework (now component-based)
  - Highly opinionated architecture
  - Built-in solutions: routing, forms, HTTP client, DI, RxJS integration

---

### 2. Rendering & architecture

- **React**
  - Virtual DOM + reconciliation
  - Functional components + hooks
  - Flexible rendering strategies (CSR, SSR with Next.js, RSC)

- **Angular**
  - Real DOM with change detection strategy (zone.js)
  - Two-way binding is common
  - Component templates tightly coupled with logic

---

### 3. Data flow

- **React**
  - One-way data flow (props down, events up)
  - State managed via hooks, Redux, Zustand, etc.

- **Angular**
  - Two-way binding supported (`[(ngModel)]`)
  - RxJS heavily used for reactive state streams

---

### 4. Learning curve

- **React**
  - Easier to start
  - Harder to architect at scale (because choices are open)

- **Angular**
  - Steeper learning curve
  - Easier to scale consistently in large teams due to strict conventions

---

### 5. Ecosystem

- **React**
  - Huge ecosystem (Next.js, Remix, React Query, Zustand)
  - Fast innovation cycle

- **Angular**
  - Stable ecosystem
  - Strong CLI + official tooling

---

### 6. Performance model

- React relies on:
  - reconciliation + memoization (`React.memo`, `useMemo`)
  - concurrent rendering (React 18)

- Angular relies on:
  - change detection optimization strategies (`OnPush`)
  - zone.js tracking async updates

---

## Example

### React (Vite + TypeScript)

**Scaffold:**

```bash
npm create vite@latest react-app -- --template react-ts
cd react-app
npm install
npm run dev
```

**Component:**

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <h1>React Counter</h1>
      <p>{count}</p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

---

### Angular equivalent (conceptual)

```ts
@Component({
  selector: "app-counter",
  template: `
    <h1>Angular Counter</h1>
    <p>{{ count }}</p>
    <button (click)="increment()">Increment</button>
  `,
})
export class CounterComponent {
  count = 0;

  increment() {
    this.count++;
  }
}
```

---

## Tooling & Setup

### React

- Preferred: **Vite / Next.js**
- Vite uses **ESM + fast HMR via esbuild**
- Flexible architecture choices:
  - React Router / TanStack Router
  - Zustand/Redux/React Query

### Angular

- Angular CLI (`ng new`)
- Webpack-based (or modern esbuild in newer Angular versions)
- Fully integrated toolchain

**Why Vite over CRA?**

- CRA is deprecated/legacy
- Vite provides faster dev server, better ESM support, and modern bundling

---

## Performance

### React

- `React.memo` for memoized components
- `useMemo` / `useCallback` for stable references
- Code splitting via `React.lazy`
- Profiler: React DevTools Profiler
- React 18: automatic batching + concurrent rendering

### Angular

- `ChangeDetectionStrategy.OnPush`
- `trackBy` in `*ngFor`
- Lazy-loaded modules
- RxJS stream optimization

---

## Testing

### React

- Unit/Integration: **Vitest + React Testing Library**

```bash
npm install -D vitest @testing-library/react jsdom
```

```ts
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders counter", () => {
  render(<Counter />);
  expect(screen.getByText(/React Counter/i)).toBeInTheDocument();
});
```

### Angular

- Jasmine/Karma (legacy) or Jest (modern setups)
- Angular Testing Utilities (`TestBed`)

---

## Ops & Deployment

- React:
  - SSR: Next.js (Vercel/Edge)
  - CSR: static hosting (S3 + CDN)

- Angular:
  - Angular Universal for SSR
  - Strong enterprise deployment patterns

Other concerns:

- Error boundaries (React)
- Logging: Sentry / Datadog
- Bundle optimization: tree-shaking, dynamic imports
- CDN caching for static assets

---

## Pitfalls

- React:
  - Over-flexibility leads to inconsistent architecture
  - Improper dependency arrays in hooks
  - Unoptimized re-renders

- Angular:
  - Overhead from zone.js change detection
  - Complex RxJS learning curve
  - Verbosity for simple apps

## Question 2. What are the differences between React and Vue.js?

## Short answer

React is a **UI library** focused on building user interfaces with JavaScript/TypeScript and JSX, while Vue.js is a **progressive framework** that provides more built-in features (such as templating, reactivity, transitions, and official routing/state libraries). React offers greater flexibility, whereas Vue generally provides a smoother learning curve and more opinionated conventions.

---

# Explanation

## 1. Core philosophy

### React

- UI library maintained by Meta.
- Uses a component-based architecture with JSX.
- Minimal core; routing, state management, and data fetching are chosen separately.
- Highly flexible for projects of any size.

### Vue.js

- Progressive framework maintained by the open-source community.
- Supports both small incremental adoption and large applications.
- Official ecosystem includes Vue Router, Pinia, and build tooling.
- More convention-driven than React.

---

## 2. Syntax

### React

Uses **JSX**, where HTML-like syntax is written directly inside JavaScript/TypeScript.

```tsx
function Welcome() {
  return <h1>Hello React</h1>;
}
```

### Vue

Uses **Single File Components (SFCs)**.

```vue
<template>
  <h1>Hello Vue</h1>
</template>

<script setup lang="ts"></script>
```

Vue separates template, logic, and styles while keeping them in one file.

---

## 3. Rendering

### React

- Virtual DOM
- Reconciliation algorithm
- React 18 concurrent rendering
- Automatic batching
- Server Components (framework-dependent, e.g., Next.js)

### Vue

- Virtual DOM
- Fine-grained reactive dependency tracking
- Efficient updates because Vue knows exactly which reactive values changed

Both are highly performant for most applications.

---

## 4. State management

### React

No built-in global state.

Popular choices include:

- Context API
- Redux Toolkit
- Zustand
- Jotai
- TanStack Query (server state)

### Vue

Official ecosystem:

- Pinia
- Vue Router
- VueUse
- Composables

State management tends to feel more integrated.

---

## 5. Reactivity

### React

Updates happen through state setters.

```tsx
setCount(count + 1);
```

### Vue

Reactive variables update automatically.

```ts
count.value++;
```

Vue's reactivity system tracks dependencies automatically.

---

## 6. Learning curve

### React

- Easier to learn initially
- More architectural decisions for larger applications
- Requires understanding hooks and JSX

### Vue

- Very beginner friendly
- HTML templates feel familiar
- Less boilerplate for common patterns

---

## 7. Ecosystem

### React

- Largest ecosystem
- Next.js
- Remix
- Expo (React Native)
- React Query
- Redux Toolkit

### Vue

- Nuxt
- Pinia
- Vue Router
- VueUse

---

## 8. TypeScript support

Both support TypeScript well.

- React works naturally with TypeScript because components are plain functions.
- Vue 3 significantly improved TypeScript support with the Composition API and `<script setup>`.

---

## 9. Performance

Both frameworks are extremely fast.

### React optimization

- React.memo
- useMemo
- useCallback
- React.lazy
- Suspense
- Concurrent rendering

### Vue optimization

- Computed properties
- watch/watchEffect
- Lazy-loaded routes
- KeepAlive
- Fine-grained reactivity

---

## Example

### React (Vite + TypeScript)

**Scaffold:**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}
```

### Vue 3 (Vite + TypeScript)

```bash
npm create vite@latest my-vue-app -- --template vue-ts
cd my-vue-app
npm install
npm run dev
```

```vue
<script setup lang="ts">
import { ref } from "vue";

const count = ref(0);
</script>

<template>
  <button @click="count++">Count: {{ count }}</button>
</template>
```

---

# Tooling & Setup

### React

- Prefer **Vite** for client-side applications.
- **Next.js** for SSR, Server Components, and full-stack features.
- Uses ESM by default and benefits from fast HMR.

### Vue

- Prefer **Vite** with Vue 3.
- **Nuxt** for SSR, static generation, and full-stack capabilities.
- Also uses ESM and provides excellent developer experience.

**Avoid Create React App (CRA)** because it is deprecated. Vite offers significantly faster startup, hot module replacement, and optimized builds.

---

# Performance

For both React and Vue:

- Use browser DevTools and framework profilers to identify rendering bottlenecks.
- Memoize expensive computations (`useMemo` in React, `computed` in Vue).
- Lazy-load routes and large components.
- Split bundles using dynamic imports.
- Cache server data with libraries such as TanStack Query (React) or Vue Query.

---

# Testing

### React

Unit/Integration:

```bash
npm install -D vitest @testing-library/react jsdom
```

### Vue

Unit/Integration:

```bash
npm install -D vitest @vue/test-utils jsdom
```

For end-to-end testing, **Playwright** is an excellent choice for both ecosystems.

---

# Ops & Deployment

- Deploy static builds to a CDN for fast global delivery.
- Use Next.js (React) or Nuxt (Vue) when SSR or SEO is required.
- Implement centralized logging (e.g., Sentry).
- Use error boundaries in React; in Vue, use `errorCaptured` or global error handlers.
- Analyze bundle size regularly and remove unused dependencies.

---

# Pitfalls

- **React**
  - Choosing too many third-party libraries without clear standards can lead to inconsistent architecture.
  - Overusing `useMemo` and `useCallback` without measuring performance.
  - Mutating state instead of treating it as immutable.

- **Vue**
  - Mixing the Options API and Composition API inconsistently within a codebase.
  - Overusing watchers where computed properties are more appropriate.
  - Forgetting that refs require `.value` in JavaScript/TypeScript (outside templates).

## Question 3. How does React handle DOM updates?

# Short answer

React updates the DOM efficiently by using a **Virtual DOM**. When state or props change, React creates a new Virtual DOM tree, compares it with the previous one using the **reconciliation (diffing) algorithm**, determines the minimal set of changes, and applies only those updates to the real DOM. In React 18, **automatic batching** and **concurrent rendering** further optimize rendering performance and responsiveness.

---

# Explanation

## 1. What is the Virtual DOM?

The **Virtual DOM (VDOM)** is a lightweight JavaScript representation of the real DOM.

When a component renders:

1. React creates a Virtual DOM tree.
2. State or props change.
3. React creates a new Virtual DOM tree.
4. React compares the old and new trees.
5. Only the changed nodes are updated in the real DOM.

This minimizes expensive DOM operations.

```
State Change
      │
      ▼
Render Component
      │
      ▼
New Virtual DOM
      │
      ▼
Compare with Previous Virtual DOM
      │
      ▼
Find Differences (Diffing)
      │
      ▼
Update Only Changed DOM Nodes
```

---

## 2. Reconciliation (Diffing Algorithm)

React's reconciliation algorithm determines what has changed between renders.

For example:

```jsx
Before: <h1>Hello</h1>;

After: <h1>Hello React</h1>;
```

React updates **only the text node**, not the entire `<h1>` element.

Similarly:

```jsx
Before: <ul>
  <li>A</li>
  <li>B</li>
</ul>;

After: <ul>
  <li>A</li>
  <li>C</li>
</ul>;
```

Only the second `<li>` is updated.

---

## 3. React's Diffing Assumptions

React optimizes comparisons using two assumptions:

### Different element types

```jsx
<div />
```

↓

```jsx
<span />
```

React destroys the old tree and creates a new one.

---

### Same element type

```jsx
<button className="red">
```

↓

```jsx
<button className="blue">
```

React updates only the `className` attribute.

---

### Keys in lists

Keys help React identify items efficiently.

```tsx
users.map((user) => <li key={user.id}>{user.name}</li>);
```

Without stable keys, React may unnecessarily re-render or recreate list items.

---

## 4. Render Phase vs Commit Phase

React rendering occurs in two phases.

### Render Phase

- Calls component functions.
- Computes the next Virtual DOM.
- Can be interrupted in concurrent rendering.

### Commit Phase

- Applies the calculated changes to the real DOM.
- Runs layout effects (`useLayoutEffect`) synchronously.
- Schedules passive effects (`useEffect`) after the browser paints.

---

## 5. React 18 Concurrent Rendering

Concurrent rendering lets React prepare updates without blocking the main thread.

Benefits include:

- Interruptible rendering.
- Better responsiveness.
- Prioritized updates.
- Smoother user interactions.

Example:

```tsx
startTransition(() => {
  setSearchQuery(value);
});
```

Urgent updates (typing) are prioritized over expensive UI updates.

---

## 6. Automatic Batching (React 18)

React batches multiple state updates into a single render, even in asynchronous contexts.

```tsx
setCount((c) => c + 1);
setLoading(true);
```

Only **one render** occurs instead of two.

Automatic batching works across:

- Event handlers
- Promises
- `setTimeout`
- Native event listeners

---

## 7. When Does React Re-render?

A component re-renders when:

- State changes.
- Props change.
- A parent re-renders (unless memoized).
- Context values change.

A re-render does **not** necessarily mean the real DOM is updated. If the diff finds no changes, the commit phase performs no DOM mutations.

---

# Example

### Scaffold (Vite + React + TypeScript)

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
  const [count, setCount] = useState(0);

  console.log("Component rendered");

  return (
    <div>
      <h2>Count: {count}</h2>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

**What happens when the button is clicked?**

1. `setCount` schedules a state update.
2. React re-renders `App`.
3. A new Virtual DOM tree is created.
4. React compares it with the previous tree.
5. Only the text inside `<h2>` changes.
6. The `<button>` element is reused because it hasn't changed.

---

# Tooling & Setup

- **Preferred setup:** Vite + React + TypeScript for fast development and native ESM support.
- **Avoid Create React App (CRA)** because it is deprecated.
- For SSR, routing, and React Server Components, use **Next.js**.
- Vite uses **ES Modules (ESM)** during development with fast Hot Module Replacement (HMR), while production builds are optimized with Rollup.

---

# Performance

- Use the **React DevTools Profiler** to identify unnecessary re-renders.
- Memoize components with `React.memo`.
- Memoize expensive calculations with `useMemo`.
- Memoize callback props with `useCallback` when passing them to memoized children.
- Use `React.lazy` and `Suspense` for code splitting and lazy loading.
- Cache server state with libraries like **TanStack Query** to reduce unnecessary network requests and re-renders.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders initial count", () => {
  render(<App />);
  expect(screen.getByText("Count: 0")).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Use **Error Boundaries** to prevent UI crashes from component errors.
- Integrate logging and monitoring tools such as Sentry for production diagnostics.
- Prefer **SSR** (e.g., Next.js) for SEO and faster initial page loads; use **CSR** for highly interactive applications where SEO is less critical.
- Optimize bundle size with tree shaking, dynamic imports, and dependency analysis tools.
- Deploy static assets through a CDN and leverage HTTP caching for faster content delivery.

---

# Pitfalls

- Using unstable or array-index keys in lists, causing unnecessary DOM updates.
- Assuming every component re-render results in a real DOM update—React only commits actual differences.
- Prematurely applying `React.memo`, `useMemo`, or `useCallback` without profiling; these optimizations also have a maintenance and runtime cost.

## Question 4. What is the purpose of React.createElement?

## Short answer

`React.createElement` is the **core function React uses to describe UI elements**. It creates a plain JavaScript object (a React element) that represents what should appear on the screen. JSX is just syntactic sugar that compiles down to `React.createElement`.

---

## Explanation

### 1. What it actually does

When you write JSX:

```jsx
const el = <h1 className="title">Hello</h1>;
```

It gets compiled (via Babel / TypeScript) into:

```js
const el = React.createElement("h1", { className: "title" }, "Hello");
```

So `React.createElement` is responsible for:

- Creating a **React element object**
- Describing the UI structure (not rendering it yet)
- Passing props and children in a structured format

---

### 2. What is a React element?

A React element is a **plain JavaScript object**, not a DOM node:

```js
{
  type: "h1",
  props: {
    className: "title",
    children: "Hello"
  },
  key: null,
  ref: null
}
```

React later uses these objects during:

- **Reconciliation (diffing)**
- **Render phase**
- **Commit phase (DOM updates)**

---

### 3. Why React uses it

React needs a **declarative description of UI**, not imperative DOM manipulation.

Instead of:

```js
document.createElement("h1");
```

React uses:

```js
React.createElement("h1", props, children);
```

This allows React to:

- Build a Virtual DOM tree
- Compare previous vs next UI efficiently
- Enable features like concurrent rendering
- Keep UI predictable and testable

---

### 4. Component usage

It also works with custom components:

```jsx
const App = () => <Button label="Click me" />;
```

Becomes:

```js
React.createElement(Button, { label: "Click me" });
```

So:

- If `type` is a string → DOM element (`div`, `span`)
- If `type` is a function/class → React component

---

### 5. Children handling

Multiple children become nested arguments:

```jsx
<div>
  <h1>Hello</h1>
  <p>World</p>
</div>
```

Transpiles to:

```js
React.createElement(
  "div",
  null,
  React.createElement("h1", null, "Hello"),
  React.createElement("p", null, "World"),
);
```

---

## Example

### Vite setup (React + TypeScript)

```bash
npm create vite@latest react-app -- --template react-ts
cd react-app
npm install
npm run dev
```

### Using `React.createElement` directly (no JSX)

```tsx
import React from "react";

export default function App() {
  return React.createElement(
    "div",
    { className: "container" },
    React.createElement("h1", null, "Hello React"),
    React.createElement(
      "button",
      {
        onClick: () => alert("Clicked!"),
      },
      "Click me",
    ),
  );
}
```

This produces the same UI as JSX.

---

## Tooling & Setup

- JSX is compiled by:
  - **Babel** or **TypeScript compiler**

- JSX transform options:
  - Modern React (17+): `react-jsx` runtime (no need to import React in every file)

- Recommended setup:
  - **Vite + React + TypeScript**

- Avoid CRA (Create React App) because it is deprecated

---

## Performance

- `React.createElement` is **very cheap** (object creation only)
- Real cost comes from:
  - Reconciliation (diffing trees)
  - Component re-renders
  - DOM commits

Optimization strategies:

- `React.memo` to avoid unnecessary re-renders
- `useMemo` / `useCallback` for stable references
- Keep element trees shallow where possible
- Use key properly in lists for efficient diffing
- Use React DevTools Profiler to detect wasted renders

---

## Testing

Since JSX compiles to `React.createElement`, testing focuses on **rendered output**, not element creation:

```bash
npm install -D vitest @testing-library/react jsdom
```

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders heading", () => {
  render(<App />);
  expect(screen.getByText("Hello React")).toBeInTheDocument();
});
```

---

## Ops & Deployment

- `React.createElement` is not used directly in production code in modern apps (JSX hides it)
- Production performance depends on:
  - Bundle size (tree-shaking JSX + unused imports)
  - Minification of compiled output
  - Efficient reconciliation, not element creation

- SSR frameworks (Next.js) serialize React elements on the server and hydrate on client

---

## Pitfalls

- Confusing React elements with DOM nodes (they are not the same)
- Manually using `React.createElement` everywhere instead of JSX (reduces readability)
- Assuming `createElement` causes re-render costs (it doesn’t — reconciliation does)
- Forgetting that props are immutable descriptors, not live DOM attributes

## Question 5. Explain the difference between functional and class components in terms of lifecycle methods

## Short answer

**Class components** use explicit lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.
**Functional components** do not have lifecycle methods directly—instead, they use the **`useEffect` hook (and related hooks)** to achieve the same lifecycle behavior in a unified way.

---

# Explanation

## 1. Class component lifecycle model

Class components are based on **lifecycle methods tied to instance lifecycle phases**.

### Mounting phase (component is created)

- `constructor`
- `render`
- `componentDidMount`

### Updating phase (props/state changes)

- `shouldComponentUpdate`
- `render`
- `componentDidUpdate`

### Unmounting phase (component removed)

- `componentWillUnmount`

### Example

```tsx id="cls1"
import React from "react";

class User extends React.Component {
  state = { name: "" };

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
    return <h1>{this.state.name}</h1>;
  }
}
```

### Key idea

Lifecycle is **segmented and method-based**, tied to the component instance.

---

## 2. Functional component lifecycle model

Functional components don’t have lifecycle methods. Instead, they use:

### `useEffect` = unified lifecycle handler

```tsx id="fn1"
useEffect(() => {
  // runs after render (mount or update)

  return () => {
    // cleanup (unmount or before next effect)
  };
}, [dependencies]);
```

---

### Mapping lifecycle behavior

| Class Lifecycle Method | Functional Equivalent (`useEffect`) |
| ---------------------- | ----------------------------------- |
| `componentDidMount`    | `useEffect(() => {}, [])`           |
| `componentDidUpdate`   | `useEffect(() => {}, [deps])`       |
| `componentWillUnmount` | cleanup function in `useEffect`     |

---

### Example (functional equivalent)

```tsx id="fn2"
import { useEffect, useState } from "react";

export default function User() {
  const [name, setName] = useState("");

  // componentDidMount
  useEffect(() => {
    console.log("Mounted");
  }, []);

  // componentDidUpdate (runs when name changes)
  useEffect(() => {
    console.log("Name updated:", name);
  }, [name]);

  // componentWillUnmount
  useEffect(() => {
    return () => {
      console.log("Unmounted");
    };
  }, []);

  return <input value={name} onChange={(e) => setName(e.target.value)} />;
}
```

---

## 3. React 18 rendering behavior context

### Functional components (modern React)

- Re-render = function re-execution
- Effects run **after commit phase**
- Supports:
  - Concurrent rendering
  - Automatic batching
  - Interruptible renders

### Class components

- Instance-based lifecycle tied to `this`
- Less compatible with modern concurrent features
- No built-in hooks for fine-grained composition

---

## 4. Key architectural differences

### Class components

- Stateful instances (`this.state`)
- Lifecycle methods scattered across class
- Harder to reuse logic (HOCs/render props needed)
- More boilerplate

### Functional components

- Stateless function execution model
- Hooks compose lifecycle + logic together
- Easier reuse via custom hooks
- Cleaner separation of concerns

---

## 5. Execution model difference

### Class

- One instance per mounted component
- Methods called by React runtime

### Function

- Function runs on every render
- React tracks effects and dependencies separately

This is important:

👉 Functional components are **not instantiated**
👉 They are **re-invoked functions**

---

## 6. Example comparison (same behavior)

### Class version

```tsx id="cls2"
class Timer extends React.Component {
  state = { seconds: 0 };

  componentDidMount() {
    this.interval = setInterval(() => {
      this.setState((s) => ({ seconds: s.seconds + 1 }));
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
  }

  render() {
    return <h1>{this.state.seconds}</h1>;
  }
}
```

---

### Functional version

```tsx id="fn3"
import { useEffect, useState } from "react";

export default function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setSeconds((s) => s + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <h1>{seconds}</h1>;
}
```

---

## 7. React 18 concurrency impact

Functional components:

- Work naturally with concurrent rendering
- Effects are **replay-safe and isolated**
- Can be paused/restarted safely

Class components:

- Less compatible with interruptible rendering
- Rely on synchronous lifecycle assumptions

---

## 8. Tooling & setup

### Modern recommended stack

- Vite + React + TypeScript (preferred)
- Next.js for SSR + server components

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

👉 Avoid CRA (Create React App) — deprecated and slower dev experience.

---

## 9. Performance considerations

### Functional components

- Benefit from:
  - `React.memo`
  - `useMemo`
  - `useCallback`
  - Concurrent rendering optimizations

- Easier fine-grained re-render control

### Class components

- Use:
  - `shouldComponentUpdate`
  - `PureComponent`

- More manual optimization effort

---

## 10. Testing differences

### Functional components (preferred today)

```bash id="test1"
npm install -D vitest @testing-library/react jsdom
```

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import Timer from "./Timer";

test("renders timer", () => {
  render(<Timer />);
  expect(screen.getByText(/0/)).toBeInTheDocument();
});
```

---

## 11. Ops & production notes

- Functional components align better with:
  - React Server Components
  - Suspense-based data fetching
  - Modern SSR frameworks (Next.js App Router)

- Class components:
  - Still supported but discouraged for new development
  - Harder to integrate with modern React features

---

## 12. Pitfalls

- Misusing `useEffect` (missing dependencies → stale state bugs)
- Overusing multiple effects instead of grouping related logic
- Trying to mimic class lifecycle 1:1 instead of thinking in effects
- Using class components in new codebases unnecessarily

## Question 6. How do you pass multiple props to a child component?

## Question 7. Can you use props to modify parent state? Why or why not?

## Question 8. How do you render a component conditionally without using if statements?

## Question 9. What are fragments used for in React?

## Question 10. How do you prevent default form submission in React?

## Question 11. How do you bind this in class components?

## Question 12. What is the difference between state updates in class vs functional components?

## Question 13. What is the difference between React.Component and React.PureComponent?

## Question 14. How do you reuse components in React?

## Question 15. How do you handle errors in event handlers?

## Question 16. How can you pass children to a component?

## Question 17. How do you render multiple components inside a parent?

## Question 18. What is the role of React.StrictMode?

## Question 19. Can you return multiple elements from a component? How?

## Question 20. How do you convert class components to functional components?
