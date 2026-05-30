# Set 21

| #   | Question                                                                                                                                                                |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you explain React to someone who knows only HTML, CSS, and JavaScript?](#question-1-how-do-you-explain-react-to-someone-who-knows-only-html-css-and-javascript) |
| 2   | [What is the difference between React elements and React components?](#question-2-what-is-the-difference-between-react-elements-and-react-components)                   |
| 3   | [What are the rules of hooks in React?](#question-3-what-are-the-rules-of-hooks-in-react)                                                                               |
| 4   | [How do you handle events with parameters in functional components?](#question-4-how-do-you-handle-events-with-parameters-in-functional-components)                     |
| 5   | [How do you render text, numbers, and expressions in JSX?](#question-5-how-do-you-render-text-numbers-and-expressions-in-jsx)                                           |
| 6   | [How do you pass multiple children to a component?](#question-6-how-do-you-pass-multiple-children-to-a-component)                                                       |
| 7   | [How do you implement conditional rendering with switch statements?](#question-7-how-do-you-implement-conditional-rendering-with-switch-statements)                     |
| 8   | [How do you implement lists with unique keys?](#question-8-how-do-you-implement-lists-with-unique-keys)                                                                 |
| 9   | [How do you prevent default behavior in React forms?](#question-9-how-do-you-prevent-default-behavior-in-react-forms)                                                   |
| 10  | [How do you handle focus programmatically in functional components?](#question-10-how-do-you-handle-focus-programmatically-in-functional-components)                    |
| 11  | [How do you combine multiple refs in React?](#question-11-how-do-you-combine-multiple-refs-in-react)                                                                    |
| 12  | [How do you render components based on a condition from props?](#question-12-how-do-you-render-components-based-on-a-condition-from-props)                              |
| 13  | [How do you implement a simple tooltip in React?](#question-13-how-do-you-implement-a-simple-tooltip-in-react)                                                          |
| 14  | [How do you handle onChange events for controlled components?](#question-14-how-do-you-handle-onchange-events-for-controlled-components)                                |
| 15  | [How do you implement a simple dropdown menu?](#question-15-how-do-you-implement-a-simple-dropdown-menu)                                                                |
| 16  | [How do you update state objects without mutating the previous state?](#question-16-how-do-you-update-state-objects-without-mutating-the-previous-state)                |
| 17  | [How do you implement a simple star rating component?](#question-17-how-do-you-implement-a-simple-star-rating-component)                                                |
| 18  | [How do you create a reusable alert/notification component?](#question-18-how-do-you-create-a-reusable-alertnotification-component)                                     |
| 19  | [How do you implement a basic accordion component?](#question-19-how-do-you-implement-a-basic-accordion-component)                                                      |
| 20  | [How do you handle multiple checkboxes in forms?](#question-20-how-do-you-handle-multiple-checkboxes-in-forms)                                                          |

## Question 1. How do you explain React to someone who knows only HTML, CSS, and JavaScript?

# Short answer

React is a **JavaScript library for building user interfaces (UIs)**. Instead of manually updating HTML whenever data changes, you describe what the UI should look like for a given state, and React efficiently updates only the parts of the page that need to change.

Think of it as **HTML + JavaScript working together in a structured, component-based way**, with React handling DOM updates for you.

---

# Explanation

If someone already knows **HTML, CSS, and JavaScript**, here's the easiest progression:

### Step 1: Traditional HTML + JavaScript

Suppose you have a counter.

```html
<h1 id="count">0</h1>
<button onclick="increment()">+</button>

<script>
  let count = 0;

  function increment() {
    count++;
    document.getElementById("count").textContent = count;
  }
</script>
```

Here you must:

- Store the data (`count`)
- Find the DOM element
- Manually update the DOM

As the application grows, keeping HTML and JavaScript synchronized becomes increasingly difficult.

---

### Step 2: React's Approach

In React, you don't tell the browser **how** to update the page.

Instead, you tell React:

> "If the count is 5, the UI should look like this."

Whenever the state changes, React automatically updates the DOM.

This is called **declarative programming**.

Instead of:

```
Find this element
Update this text
Hide this button
Show this div
```

You simply write:

```jsx
return <h1>{count}</h1>;
```

React figures out the DOM changes for you.

---

### Step 3: Components

React applications are built from **components**.

A component is simply a reusable JavaScript function that returns UI.

Example:

```
App
 ├── Header
 ├── Sidebar
 ├── ProductList
 │      ├── ProductCard
 │      ├── ProductCard
 │      └── ProductCard
 └── Footer
```

Benefits:

- Reusable
- Easy to maintain
- Independent
- Easier testing

---

### Step 4: State

State is data that changes over time.

Examples:

- Counter value
- Logged-in user
- Shopping cart
- Search text
- Theme (Dark/Light)

Instead of manually updating HTML:

```
State changes
        ↓
React re-renders
        ↓
DOM updates automatically
```

---

### Step 5: Virtual DOM

Changing the real DOM is relatively expensive.

React introduces the **Virtual DOM**, a lightweight in-memory representation of the UI.

The update flow is:

```
State changes
       ↓
New Virtual DOM created
       ↓
Compare with previous Virtual DOM (diffing)
       ↓
Only necessary changes applied to the real DOM
```

This minimizes unnecessary DOM operations and improves rendering performance.

---

### Step 6: Rendering Behavior (React 18)

React 18 introduced features that make rendering more efficient:

- **Automatic batching** groups multiple state updates into a single render, reducing unnecessary work.
- **Concurrent rendering** allows React to prioritize urgent updates (such as typing) over less important rendering work, improving responsiveness.
- React still follows the same declarative model—you describe the UI, and React schedules updates efficiently.

---

### Step 7: Why companies use React

React makes it easier to build large applications because it provides:

- Component-based architecture
- Predictable state updates
- Efficient rendering
- Reusable UI
- Strong ecosystem
- Easy integration with APIs
- Good developer experience

It's used for dashboards, e-commerce sites, social networks, SaaS applications, and more.

---

# Example

**Modern setup (Vite + React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`src/App.tsx`**

```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <main>
      <h1>Count: {count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </main>
  );
}
```

Notice there is:

- No `document.getElementById()`
- No `innerHTML`
- No manual DOM manipulation

Updating the state triggers React to re-render the component and update only the necessary DOM nodes.

---

# Tooling & Setup

- **Avoid Create React App (CRA):** It is deprecated for new projects.
- **Prefer Vite** for client-rendered applications because it offers fast startup, native ESM during development, and quick hot module replacement (HMR).
- Use **Next.js** when you need server-side rendering (SSR), static site generation (SSG), or React Server Components.
- **Remix** is a strong choice for data-driven routing and web standards.
- **ES Modules (ESM)** are the standard module format used by modern React tooling. CommonJS is mainly encountered in older Node.js projects.
- Vite uses an ESM-based development server with fast dependency pre-bundling for an excellent developer experience.

---

# Performance

For production applications:

- Use the **React DevTools Profiler** to identify unnecessary renders.
- Memoize expensive calculations with `useMemo`.
- Memoize callback props passed to memoized children with `useCallback`.
- Use `React.memo` for components that receive stable props.
- Split bundles with `React.lazy` and `Suspense`.
- Cache server data with libraries such as React Query or framework-provided data fetching to reduce redundant network requests.
- Keep component state as local as practical to avoid unnecessary re-renders.

---

# Testing

A common modern testing stack is:

- **Vitest** for unit testing.
- **React Testing Library** for component behavior.
- **Playwright** for end-to-end testing.

Install unit testing dependencies:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example command:

```bash
npm run test
```

Focus tests on user-visible behavior rather than implementation details.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Add structured logging and monitoring (e.g., Sentry or OpenTelemetry) for production diagnostics.
- Choose **CSR** for highly interactive apps, and **SSR/SSG** (e.g., Next.js) when SEO or faster initial page loads are important.
- Monitor bundle size with tools such as bundle analyzers and apply code splitting where appropriate.
- Deploy static Vite builds to a CDN, or deploy SSR applications to edge/server platforms depending on your framework.

---

# Pitfalls

- **Don't manipulate the DOM directly** with APIs like `document.getElementById()` unless integrating with third-party libraries.
- **Don't mutate state directly**; always use state setters and immutable update patterns.
- **Don't overuse global state**—keep state local until it genuinely needs to be shared.

## Question 2. What is the difference between React elements and React components?

# Short answer

A **React element** is a **plain JavaScript object** that describes what should appear on the screen. It is **immutable** and created using JSX or `React.createElement()`.

A **React component** is a **function (or class, though functional components are the modern standard)** that returns React elements. Components encapsulate UI, logic, and state, making them reusable building blocks.

In simple terms:

- **Element = What to render**
- **Component = Function that creates elements**

---

# Explanation

Think of building a house:

- **Blueprint** → React Component
- **Finished room** → React Element

The component contains the instructions for building the UI, while the element is the description of the UI produced by those instructions.

## React Element

A React element is an object created by JSX.

Example:

```tsx
const element = <h1>Hello, React!</h1>;
```

JSX is compiled into:

```tsx
const element = React.createElement("h1", null, "Hello, React!");
```

Conceptually, the resulting object looks like:

```ts
{
  type: "h1",
  props: {
    children: "Hello, React!"
  }
}
```

Characteristics:

- Immutable
- Lightweight JavaScript object
- Describes UI
- Not an actual DOM node
- Used by React during reconciliation to determine what should be rendered

---

## React Component

A component is a JavaScript/TypeScript function that returns React elements.

```tsx
function Welcome() {
  return <h1>Hello!</h1>;
}
```

Using the component:

```tsx
<Welcome />
```

Execution flow:

```text
<App />
      ↓
React calls App()
      ↓
App returns React elements
      ↓
React compares them with the previous render
      ↓
Updates the DOM
```

Components can:

- Receive props
- Manage state
- Use Hooks
- Handle events
- Fetch data
- Compose other components

Elements cannot do these things—they are simply descriptions of UI.

---

## Key Differences

| Feature           | React Element        | React Component                                         |
| ----------------- | -------------------- | ------------------------------------------------------- |
| Definition        | Object describing UI | Function (or class) returning elements                  |
| Mutable           | No (immutable)       | Function logic can produce different elements over time |
| Contains logic    | No                   | Yes                                                     |
| Can use Hooks     | No                   | Yes (functional components)                             |
| Receives props    | Stored as data       | Accepts props as parameters                             |
| Reusable          | Not by itself        | Yes                                                     |
| Rendered by React | Yes                  | Invoked by React to produce elements                    |

---

## Rendering Behavior (React 18)

In React 18:

- When state or props change, React re-executes affected **components** to produce a new tree of **elements**.
- React compares the new element tree with the previous one (reconciliation) and updates only the necessary DOM nodes.
- **Automatic batching** groups multiple state updates into a single render, reducing unnecessary work.
- **Concurrent rendering** lets React prioritize urgent updates (like typing) while deferring less urgent work, improving responsiveness.

The distinction is important: **components execute**, while **elements are compared**.

---

# Example

**Modern setup (Vite + React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`src/App.tsx`**

```tsx
type WelcomeProps = {
  name: string;
};

function Welcome({ name }: WelcomeProps) {
  return <h2>Hello, {name}!</h2>;
}

export default function App() {
  const heading = <h1>React Elements vs Components</h1>; // React element

  return (
    <main>
      {heading}
      <Welcome name="Alice" /> {/* React component */}
      <Welcome name="Bob" />
    </main>
  );
}
```

Here:

- `heading` is a **React element**.
- `Welcome` is a **React component**.
- Each `<Welcome />` invocation creates a new tree of React elements during rendering.

---

# Tooling & Setup

- **Avoid Create React App (CRA):** It is deprecated.
- **Prefer Vite** for client-side React applications because of its fast dev server, native ESM support, and efficient Hot Module Replacement (HMR).
- Choose **Next.js** if you need SSR, SSG, or React Server Components.
- **Remix** is another production-ready option for route-based data loading.
- Modern React tooling is ESM-first; CommonJS is mainly encountered in older Node.js environments.

---

# Performance

- Use the **React DevTools Profiler** to identify unnecessary component re-renders.
- Use `React.memo` for components with stable props.
- Use `useMemo` for expensive computations.
- Use `useCallback` when passing callbacks to memoized child components.
- Split large bundles with `React.lazy` and `Suspense`.
- Use data-fetching caches (e.g., TanStack Query) to avoid redundant network requests.
- Keep state localized to reduce the number of components that re-render.

---

# Testing

Use:

- **Vitest** for unit testing.
- **React Testing Library** for testing component behavior.
- **Playwright** for end-to-end testing.

Install testing tools:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test command:

```bash
npm run test
```

Test rendered output and user interactions rather than implementation details.

---

# Ops & Deployment

- Use **Error Boundaries** to catch rendering errors in component trees.
- Add production logging and monitoring (e.g., Sentry, OpenTelemetry).
- Choose **CSR** for highly interactive apps and **SSR/SSG** (Next.js) for SEO and faster initial loads.
- Monitor bundle size with build analyzers and apply code splitting where appropriate.
- Deploy Vite builds as static assets behind a CDN, or deploy SSR apps to server/edge platforms.

---

# Pitfalls

- **Don't confuse JSX tags with elements.** `<Button />` is JSX that React uses to invoke the `Button` component and produce elements.
- **Don't mutate React elements.** They are immutable descriptions of the UI.
- **Avoid defining components inside other components** unless necessary, as it creates a new component function on every render and can affect performance and state preservation.

## Question 3. What are the rules of hooks in React?

# Short answer

The **Rules of Hooks** ensure React can correctly preserve state and effects between renders. The two primary rules are:

1. **Only call Hooks at the top level** (never inside loops, conditions, or nested functions).
2. **Only call Hooks from React function components or custom Hooks** (not from regular JavaScript functions or class components).

Following these rules allows React to associate each Hook call with the correct component state across renders.

---

# Explanation

Hooks (such as `useState`, `useEffect`, and `useMemo`) rely on the **order in which they are called**. React internally tracks Hook state by their call order during each render.

If the order changes between renders, React cannot determine which state belongs to which Hook, leading to bugs.

## Rule 1: Only Call Hooks at the Top Level

✅ Correct:

```tsx
import { useEffect, useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]);

  return <button onClick={() => setCount((c) => c + 1)}>{count}</button>;
}
```

❌ Incorrect (inside a condition):

```tsx
if (loggedIn) {
  useEffect(() => {
    // ❌ Wrong
  }, []);
}
```

❌ Incorrect (inside a loop):

```tsx
items.forEach(() => {
  useState(0); // ❌ Wrong
});
```

❌ Incorrect (inside a nested function):

```tsx
function handleClick() {
  useState(0); // ❌ Wrong
}
```

Instead, call the Hook unconditionally and place conditional logic **inside** the Hook:

```tsx
useEffect(() => {
  if (loggedIn) {
    // Safe
  }
}, [loggedIn]);
```

---

## Rule 2: Only Call Hooks from React Components or Custom Hooks

✅ React component:

```tsx
function Profile() {
  const [user, setUser] = useState(null);

  return <div>{user?.name}</div>;
}
```

✅ Custom Hook:

```tsx
function useWindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  return width;
}
```

❌ Regular JavaScript function:

```tsx
function calculatePrice() {
  useState(0); // ❌ Invalid
}
```

Custom Hooks are simply functions whose names start with `use` and that can call other Hooks to encapsulate reusable stateful logic.

---

## Why These Rules Exist

React stores Hook state in the order Hooks are called.

Correct order:

```text
Render 1
1. useState
2. useEffect
3. useMemo

Render 2
1. useState
2. useEffect
3. useMemo
```

Incorrect order:

```text
Render 1
1. useState
2. useEffect
3. useMemo

Render 2
1. useState
2. useMemo   ❌
```

Because the second render skipped `useEffect`, React would associate the wrong state with subsequent Hooks.

---

## Rendering Behavior (React 18)

React 18's **automatic batching** and **concurrent rendering** improve update scheduling and responsiveness, but they **do not change the Rules of Hooks**.

- Components may render more than once before committing updates.
- React still expects Hooks to be called in the same order on every render.
- In development, **Strict Mode** intentionally re-renders components to help detect side effects, making adherence to the Rules of Hooks even more important.

---

# Example

**Modern setup (Vite + React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`src/App.tsx`**

```tsx
import { useEffect, useState } from "react";

function useDocumentTitle(title: string) {
  useEffect(() => {
    document.title = title;
  }, [title]);
}

export default function App() {
  const [count, setCount] = useState(0);

  useDocumentTitle(`Count: ${count}`);

  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}
```

This example follows both rules:

- Hooks are called at the top level.
- `useDocumentTitle` is a valid custom Hook that can use other Hooks internally.

---

# Tooling & Setup

- **Avoid Create React App (CRA):** It is deprecated.
- **Prefer Vite** for client-side React development due to its fast startup, native ESM support, and HMR.
- Use **Next.js** when you need SSR, SSG, or React Server Components.
- Modern React projects are ESM-first; CommonJS is mainly found in older Node.js ecosystems.
- Enable the **ESLint Hooks plugin** (`eslint-plugin-react-hooks`). It automatically detects violations of the Rules of Hooks and missing effect dependencies.

---

# Performance

- Use the **React DevTools Profiler** to identify unnecessary renders.
- Memoize expensive calculations with `useMemo`.
- Memoize callback props with `useCallback` when passing them to memoized children.
- Use `React.memo` for components with stable props.
- Split large bundles with `React.lazy` and `Suspense`.
- Cache server data with libraries such as TanStack Query instead of storing all fetched data in component state.

---

# Testing

Use:

- **Vitest** + **React Testing Library** for unit and integration tests.
- **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

When testing custom Hooks, verify their observable behavior through components or dedicated Hook testing utilities rather than relying on implementation details.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures (they do not catch errors inside event handlers or async callbacks).
- Enable **Strict Mode** during development to surface side-effect issues early.
- Monitor production errors with tools like Sentry or OpenTelemetry.
- Keep bundles small using code splitting and lazy loading.
- Deploy Vite apps as static assets behind a CDN, or use Next.js for SSR/edge deployments.

---

# Pitfalls

- **Never call Hooks inside `if`, `for`, `while`, callbacks, or nested functions.**
- **Don't call Hooks from regular JavaScript utility functions**—extract reusable stateful logic into custom Hooks instead.
- **Use the `eslint-plugin-react-hooks` plugin** to catch Hook rule violations and dependency issues automatically.

## Question 4. How do you handle events with parameters in functional components?

## Question 5. How do you render text, numbers, and expressions in JSX?

## Question 6. How do you pass multiple children to a component?

## Question 7. How do you implement conditional rendering with switch statements?

## Question 8. How do you implement lists with unique keys?

## Question 9. How do you prevent default behavior in React forms?

## Question 10. How do you handle focus programmatically in functional components?

## Question 11. How do you combine multiple refs in React?

## Question 12. How do you render components based on a condition from props?

## Question 13. How do you implement a simple tooltip in React?

## Question 14. How do you handle onChange events for controlled components?

## Question 15. How do you implement a simple dropdown menu?

## Question 16. How do you update state objects without mutating the previous state?

## Question 17. How do you implement a simple star rating component?

## Question 18. How do you create a reusable alert/notification component?

## Question 19. How do you implement a basic accordion component?

## Question 20. How do you handle multiple checkboxes in forms?
