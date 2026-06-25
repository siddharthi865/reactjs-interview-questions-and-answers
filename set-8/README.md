# Set 8

| S.No. | Question                                                                                                                                                                                    |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between `useState` lazy initialization vs direct initialization?](#question-1-what-is-the-difference-between-usestate-lazy-initialization-vs-direct-initialization) |
| 2.    | [How do you implement theme switching using context API?](#question-2-how-do-you-implement-theme-switching-using-context-api)                                                               |
| 3.    | [Explain the concept of Render Props in React](#question-3-explain-the-concept-of-render-props-in-react)                                                                                    |
| 4.    | [Difference between render props and higher-order components](#question-4-difference-between-render-props-and-higher-order-components)                                                      |
| 5.    | [How do you implement infinite scroll using IntersectionObserver in React?](#question-5-how-do-you-implement-infinite-scroll-using-intersectionobserver-in-react)                           |
| 6.    | [How do you handle memoization of components with dynamic props?](#question-6-how-do-you-handle-memoization-of-components-with-dynamic-props)                                               |
| 7.    | [How do you prevent stale closures in hooks?](#question-7-how-do-you-prevent-stale-closures-in-hooks)                                                                                       |
| 8.    | [Explain the difference between `componentDidMount` and `useEffect(() => {}, [])`](#question-8-explain-the-difference-between-componentdidmount-and-useeffect---)                           |
| 9.    | [How do you handle forms with dynamic fields in React?](#question-9-how-do-you-handle-forms-with-dynamic-fields-in-react)                                                                   |
| 10.   | [How do you manage state across multiple components using context?](#question-10-how-do-you-manage-state-across-multiple-components-using-context)                                          |
| 11.   | [How do you implement dark mode in React?](#question-11-how-do-you-implement-dark-mode-in-react)                                                                                            |
| 12.   | [How do you debounce input changes in React?](#question-12-how-do-you-debounce-input-changes-in-react)                                                                                      |
| 13.   | [How do you implement image lazy loading with `React.lazy` and `Suspense`?](#question-13-how-do-you-implement-image-lazy-loading-with-reactlazy-and-suspense)                               |
| 14.   | [How do you handle error messages in forms in React?](#question-14-how-do-you-handle-error-messages-in-forms-in-react)                                                                      |
| 15.   | [How do you implement client-side caching in React?](#question-15-how-do-you-implement-client-side-caching-in-react)                                                                        |
| 16.   | [How do you implement optimistic UI updates in React?](#question-16-how-do-you-implement-optimistic-ui-updates-in-react)                                                                    |
| 17.   | [How do you persist state across page refreshes?](#question-17-how-do-you-persist-state-across-page-refreshes)                                                                              |
| 18.   | [How do you integrate React with third-party libraries like Chart.js or D3.js?](#question-18-how-do-you-integrate-react-with-third-party-libraries-like-chartjs-or-d3js)                    |
| 19.   | [How do you implement a search/filter component in React?](#question-19-how-do-you-implement-a-searchfilter-component-in-react)                                                             |
| 20.   | [How do you implement multi-step forms in React?](#question-20-how-do-you-implement-multi-step-forms-in-react)                                                                              |

## Question 1. What is the difference between `useState` lazy initialization vs direct initialization?

## Short answer

Direct initialization computes the initial state on every render (though React only uses it on the first render), while lazy initialization computes the initial state only once on mount using a function passed to `useState`.

---

## Explanation

### 1. Direct initialization

```js
const [state, setState] = useState(expensiveComputation());
```

- `expensiveComputation()` runs **on every render**.
- React only uses the returned value for the **initial render**, but the function is still executed repeatedly.
- This can cause unnecessary performance overhead.

#### Key behavior:

- Execution happens during render phase.
- Wasteful if computation is heavy.

---

### 2. Lazy initialization

```js
const [state, setState] = useState(() => expensiveComputation());
```

- The function is executed **only once (on initial mount)**.
- React ignores it on subsequent renders.
- Ideal for expensive computations or reading from localStorage, IndexedDB, etc.

#### Key behavior:

- Function is passed, not executed immediately.
- React invokes it only during initial state setup.

---

### React rendering behavior (important for interviews)

- React 18 with concurrent rendering may render components multiple times before committing.
- Direct initialization can re-run expensive logic unnecessarily during these render attempts.
- Lazy initialization ensures computation is **isolated to initial mount phase only**, improving performance stability.

---

### State management trade-offs

- Use **direct initialization** when:
  - Value is cheap (constants, simple calculations)
  - Readability matters more than micro-optimization

- Use **lazy initialization** when:
  - Computation is expensive (sorting large arrays, parsing JSON)
  - Reading from browser APIs (`localStorage`, `sessionStorage`)
  - Avoiding unnecessary work during re-renders

---

## Example (React + TypeScript)

### Direct vs Lazy Initialization

```tsx
import React, { useState } from "react";

function expensiveComputation() {
  console.log("Expensive computation running...");
  return Array.from({ length: 1000000 }, (_, i) => i).reduce(
    (a, b) => a + b,
    0,
  );
}

export default function Counter() {
  // ❌ Runs on every render (bad for expensive logic)
  const [directValue] = useState(expensiveComputation());

  // ✅ Runs only once on mount
  const [lazyValue] = useState(() => expensiveComputation());

  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount((c) => c + 1)}>Re-render</button>

      <p>Direct init value: {directValue}</p>
      <p>Lazy init value: {lazyValue}</p>
    </div>
  );
}
```

---

## Tooling & Setup

Use **Vite (recommended)** instead of CRA:

```bash
npm create vite@latest react-lazy-state -- --template react-ts
cd react-lazy-state
npm install
npm run dev
```

- Vite uses **ESM-based dev server** (fast HMR)
- Bundler: **Rollup for production builds**
- Modern React setups rely on ESM instead of CommonJS for tree-shaking efficiency

---

## Performance

- Prefer lazy initialization when:
  - CPU-heavy computations exist
  - Data hydration from storage/API sync is needed

### Optimization techniques:

- Avoid unnecessary re-computation in render
- Combine with:
  - `React.memo` for component memoization
  - `useMemo` for derived state (not initial state)
  - `useCallback` for stable handlers

- Use React DevTools Profiler to detect re-render cost

---

## Testing

Using **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders lazy initialized value", () => {
  render(<Counter />);
  expect(screen.getByText(/Direct init value/)).toBeInTheDocument();
});
```

- Mock expensive functions using `vi.fn()`
- Ensure initialization only happens once (spy on function calls)

---

## Ops & Deployment

- Use lazy init to reduce **client-side startup cost**
- Important in SSR frameworks (Next.js):
  - Avoid running browser-only logic during SSR

- Combine with:
  - Code splitting (`React.lazy`)
  - CDN caching for static assets
  - Edge rendering (Next.js / Vercel)

---

## Pitfalls

- ❌ Calling expensive functions directly in `useState`
- ❌ Assuming lazy init runs on every render (it does NOT)
- ❌ Using lazy init for derived state (better with `useMemo`)
- ❌ Over-optimizing trivial computations

## Question 2. How do you implement theme switching using context API?

## Short answer

Implement theme switching by creating a **Theme Context** that stores the current theme (`light`/`dark`) and a function to toggle or set it. Wrap your app with a `ThemeProvider`, expose a custom `useTheme` hook, and consume the context anywhere in the component tree. For production apps, persist the theme (e.g., `localStorage`), respect the user's system preference, and apply the theme at the root element.

---

# Explanation

The **Context API** is ideal for application-wide state such as themes because it avoids prop drilling and provides a single source of truth.

### Architecture

```
App
 └── ThemeProvider
      ├── Navbar
      ├── Sidebar
      ├── Dashboard
      └── Settings
```

The `ThemeProvider` manages:

- Current theme (`light` or `dark`)
- Toggle function
- Persistence (optional)
- Synchronization with the DOM (`data-theme` or CSS class)

### How it works

1. Create a `ThemeContext`.
2. Store the theme using `useState`.
3. Expose `theme` and `toggleTheme`.
4. Wrap the application with `ThemeProvider`.
5. Consume the context using a custom `useTheme()` hook.

### React 18 considerations

- Theme changes are automatically **batched** with other state updates.
- Updating the context re-renders all consuming components.
- To reduce unnecessary re-renders:
  - Memoize the context value with `useMemo`.
  - Split contexts if your provider manages unrelated state.
  - Avoid storing frequently changing values in the same context.

---

# Example (React + TypeScript)

## Create a Vite project

```bash
npm create vite@latest theme-demo -- --template react-ts
cd theme-demo
npm install
npm run dev
```

### `ThemeContext.tsx`

```tsx
import {
  createContext,
  useContext,
  useEffect,
  useMemo,
  useState,
  ReactNode,
} from "react";

type Theme = "light" | "dark";

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>(() => {
    return (localStorage.getItem("theme") as Theme) || "light";
  });

  useEffect(() => {
    document.documentElement.dataset.theme = theme;
    localStorage.setItem("theme", theme);
  }, [theme]);

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  const value = useMemo(
    () => ({
      theme,
      toggleTheme,
    }),
    [theme],
  );

  return (
    <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);

  if (!context) {
    throw new Error("useTheme must be used inside ThemeProvider");
  }

  return context;
}
```

### `App.tsx`

```tsx
import { ThemeProvider, useTheme } from "./ThemeContext";

function Home() {
  const { theme, toggleTheme } = useTheme();

  return (
    <div>
      <h2>Current Theme: {theme}</h2>

      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <Home />
    </ThemeProvider>
  );
}
```

### CSS

```css
:root[data-theme="light"] {
  --bg: white;
  --text: black;
}

:root[data-theme="dark"] {
  --bg: #1e1e1e;
  --text: white;
}

body {
  background: var(--bg);
  color: var(--text);
  transition:
    background 0.2s ease,
    color 0.2s ease;
}
```

---

# Tooling & Setup

**Recommended stack:** Vite + React + TypeScript

Why Vite?

- Extremely fast development server with HMR
- Native ES Modules (ESM) during development
- Rollup for optimized production builds
- Better startup performance than the deprecated Create React App

Framework considerations:

- **Next.js:** Use this approach in Client Components. For SSR, initialize the theme from a cookie or inline script to avoid hydration mismatches.
- **Remix:** Load the initial theme on the server and pass it through the root route.

---

# Performance

Theme changes trigger re-renders for all context consumers.

Optimize by:

- Memoizing the provider value with `useMemo`.
- Using `React.memo` for components that receive stable props.
- Splitting contexts if theme state grows (e.g., separate theme and user contexts).
- Using CSS variables so changing the theme mostly updates styles rather than requiring additional React state.
- Code-splitting large routes with `React.lazy` and `Suspense`.
- Using the React DevTools Profiler to identify unnecessary re-renders after toggling the theme.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("toggles theme", () => {
  render(<App />);

  fireEvent.click(screen.getByRole("button"));

  expect(screen.getByText(/Current Theme: dark/i)).toBeInTheDocument();
});
```

For end-to-end testing, use Playwright to verify that the selected theme persists across page reloads.

---

# Ops & Deployment

- Persist the theme in `localStorage` or cookies.
- Respect the user's system preference using `window.matchMedia("(prefers-color-scheme: dark)")` for the initial theme.
- For SSR applications, initialize the theme before hydration to prevent a flash of the wrong theme (FOUC).
- Use an Error Boundary for unrelated runtime errors; theme context errors are best handled by throwing in the custom hook when used outside the provider.
- Minimize bundle size by keeping the provider focused and tree-shaking unused code.
- Deploy static assets through a CDN; theme switching itself requires no server infrastructure.

---

# Pitfalls

- Don't create the context value inline without `useMemo`, or consumers may re-render unnecessarily.
- Don't store unrelated global state (authentication, notifications, settings) in the same context as the theme.
- Don't rely solely on `localStorage`; consider the user's system preference and SSR requirements to avoid hydration issues.

## Question 3. Explain the concept of Render Props in React

## Short answer

**Render Props** is a React pattern where a component shares logic by accepting a **function as a prop**. Instead of deciding what to render itself, the component calls the function and passes data to it, allowing the parent to control the UI while reusing the underlying behavior.

---

# Explanation

Before Hooks (`useState`, `useEffect`), Render Props were a popular way to **share stateful logic** between components without duplicating code.

Instead of this:

```tsx
<MyComponent />
```

You pass a function:

```tsx
<MyComponent render={(data) => <Child data={data} />} />
```

or more commonly:

```tsx
<MyComponent>{(data) => <Child data={data} />}</MyComponent>
```

The component owns the logic, while the caller owns the UI.

### How it works

```text
Parent
   │
   │ render function
   ▼
RenderPropComponent
   │
   ├── manages state
   ├── handles events
   └── calls render(state)
            │
            ▼
      Parent decides UI
```

### Example use cases

- Mouse position tracking
- Authentication status
- Data fetching (before Hooks)
- Form state management
- Animation libraries

---

### Render Props vs Higher-Order Components (HOCs)

| Render Props                  | HOC                               |
| ----------------------------- | --------------------------------- |
| Uses a function prop          | Wraps a component                 |
| More explicit                 | Can lead to wrapper nesting       |
| Easier to pass dynamic values | Less flexible                     |
| Avoids prop collisions        | Prop name collisions are possible |

---

### Render Props vs Hooks

Modern React generally prefers **custom Hooks** because they:

- Require less nesting
- Produce cleaner JSX
- Are easier to compose
- Work naturally with function components

Render Props are still encountered in legacy codebases and some libraries.

---

### React 18 considerations

- Render Props are fully compatible with concurrent rendering.
- Automatic batching works the same as with any functional component.
- A new render function is created on every parent render unless memoized, which may trigger unnecessary child renders.

---

# Example (React + TypeScript)

## Create a Vite project

```bash
npm create vite@latest render-props-demo -- --template react-ts
cd render-props-demo
npm install
npm run dev
```

### `MouseTracker.tsx`

```tsx
import { ReactNode, useState } from "react";

type Props = {
  children: (position: { x: number; y: number }) => ReactNode;
};

export default function MouseTracker({ children }: Props) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  return (
    <div
      style={{ height: 200, border: "1px solid gray" }}
      onMouseMove={(e) =>
        setPosition({
          x: e.clientX,
          y: e.clientY,
        })
      }
    >
      {children(position)}
    </div>
  );
}
```

### `App.tsx`

```tsx
import MouseTracker from "./MouseTracker";

export default function App() {
  return (
    <MouseTracker>
      {({ x, y }) => (
        <h2>
          Mouse Position: {x}, {y}
        </h2>
      )}
    </MouseTracker>
  );
}
```

Here:

- `MouseTracker` owns the state.
- `App` controls how that state is displayed.
- The same `MouseTracker` can render any UI by changing the render function.

---

# Tooling & Setup

**Recommended stack:** Vite + React + TypeScript

Why Vite?

- Fast development server with Hot Module Replacement (HMR)
- Native ES Modules (ESM) in development
- Rollup for optimized production builds
- Avoid **Create React App (CRA)**, as it is deprecated

Framework notes:

- **Next.js:** Render Props work in Client Components. Server Components cannot accept functions as props across the server/client boundary.
- **Remix:** Works identically in client-rendered components.

---

# Performance

Render Props can introduce unnecessary re-renders because the render function is recreated on each parent render.

Optimization techniques:

- Memoize render functions with `useCallback` when passing them to memoized children.
- Use `React.memo` for components receiving stable props.
- Prefer custom Hooks for reusable logic in new codebases.
- Use `React.lazy` and dynamic imports for code splitting.
- Profile with the React DevTools Profiler to identify render bottlenecks.
- Use caching libraries like React Query or SWR for server state instead of implementing custom data-fetching Render Props.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders mouse position", () => {
  render(<App />);
  expect(screen.getByText(/Mouse Position/i)).toBeInTheDocument();
});
```

For integration tests, simulate mouse movement with `fireEvent.mouseMove`. For end-to-end testing, use Playwright to verify interactive behavior.

---

# Ops & Deployment

- Render Props add no special deployment requirements.
- Use Error Boundaries to isolate runtime errors in the rendered UI.
- Avoid deeply nested Render Props ("wrapper hell") to keep bundles and component trees maintainable.
- For SSR, ensure any browser-specific APIs (e.g., `window`, `document`) are accessed inside effects or event handlers.
- Deploy optimized bundles via a CDN and monitor performance with browser profiling tools.

---

# Pitfalls

- Creating a new render function every render can reduce memoization effectiveness.
- Excessive nesting of Render Props makes JSX difficult to read ("callback hell").
- Prefer custom Hooks for new React applications unless you need to support class components or maintain legacy APIs.

## Question 4. Difference between render props and higher-order components

## Short answer

**Render Props** pass logic via a function prop to control rendering, while **Higher-Order Components (HOCs)** wrap a component and inject props into it. Render Props give UI control to the consumer; HOCs return a new enhanced component.

---

# Explanation

Both patterns solve the same problem: **reusing stateful logic across components before Hooks existed**.

## 1. Render Props

A component exposes state/logic by accepting a function that returns UI.

```tsx id="rp1"
<DataProvider>{(data) => <Child data={data} />}</DataProvider>
```

### Key idea:

- “I will give you data, you decide how to render it.”

### Flow:

```
DataProvider (logic owner)
   ↓
calls function prop
   ↓
Parent defines UI
```

---

## 2. Higher-Order Components (HOC)

A function takes a component and returns a new component with extra props.

```tsx id="hoc1"
const withData = (Component) => {
  return function EnhancedComponent(props) {
    return <Component {...props} data="hello" />;
  };
};
```

Usage:

```tsx id="hoc2"
const Enhanced = withData(MyComponent);
```

### Key idea:

- “I will wrap your component and inject logic into it.”

### Flow:

```
MyComponent
   ↓
wrapped by HOC
   ↓
EnhancedComponent (with injected props)
```

---

# Key Differences (Interview Core)

| Feature           | Render Props                            | HOC                               |
| ----------------- | --------------------------------------- | --------------------------------- |
| Pattern type      | Component composition via function prop | Function returning component      |
| Control of UI     | Consumer controls rendering             | HOC controls structure            |
| Prop injection    | Via function arguments                  | Via props injection               |
| Readability       | More explicit but can be verbose        | Cleaner JSX but abstract wrapping |
| Nesting issue     | Less wrapper nesting                    | Can cause “wrapper hell”          |
| Debugging         | Easier to trace                         | Harder due to multiple wrappers   |
| Naming collisions | Rare                                    | Possible (prop conflicts)         |
| Hooks replacement | Largely replaced                        | Largely replaced                  |

---

# React 18 behavior considerations

Both patterns:

- Re-render when parent re-renders unless memoized
- Are fully compatible with concurrent rendering
- Benefit from:
  - `React.memo` (HOCs especially)
  - `useCallback` (Render Props to stabilize function identity)

- Can cause unnecessary renders if not optimized:
  - HOC recreates wrapper component
  - Render Props create new function each render

---

# Example (React + TypeScript)

## 1. Render Props example

```tsx id="rp3"
import { useState } from "react";

type Props = {
  children: (count: number, inc: () => void) => React.ReactNode;
};

export function CounterRenderProps({ children }: Props) {
  const [count, setCount] = useState(0);

  return children(count, () => setCount((c) => c + 1));
}

// usage
export default function App() {
  return (
    <CounterRenderProps>
      {(count, inc) => (
        <div>
          <p>{count}</p>
          <button onClick={inc}>Increment</button>
        </div>
      )}
    </CounterRenderProps>
  );
}
```

---

## 2. HOC example

```tsx id="hoc3"
import React, { useState } from "react";

type InjectedProps = {
  count: number;
  increment: () => void;
};

function withCounter<P extends object>(
  Component: React.ComponentType<P & InjectedProps>,
) {
  return function WrappedComponent(props: P) {
    const [count, setCount] = useState(0);

    const increment = () => setCount((c) => c + 1);

    return <Component {...props} count={count} increment={increment} />;
  };
}

// base component
type Props = {
  title: string;
} & InjectedProps;

function Counter({ title, count, increment }: Props) {
  return (
    <div>
      <h3>{title}</h3>
      <p>{count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}

export default withCounter(Counter);
```

---

# Tooling & Setup

Use modern tooling:

```bash id="setup1"
npm create vite@latest react-patterns -- --template react-ts
cd react-patterns
npm install
npm run dev
```

### Why Vite over CRA

- Faster dev server (native ESM)
- Better HMR performance
- Modern build pipeline (Rollup-based)
- CRA is deprecated

### When to choose frameworks:

- **Next.js**: Prefer Hooks; HOCs used in legacy auth wrappers
- **Remix**: Prefer loaders/actions instead of both patterns

---

# Performance

## Render Props issues

- Function recreation causes child re-renders
- Inline functions break memoization

### Fix:

```tsx
const render = useCallback((count) => <div>{count}</div>, []);
```

## HOC issues

- Wrapper components add React tree depth
- Can break `displayName` debugging if not set

### Optimization strategies

- `React.memo` for wrapped components
- Stable prop injection
- Avoid unnecessary re-wrapping inside render

---

# Testing

Use **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react jsdom
```

### Render Props test

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders counter UI", () => {
  render(<App />);
  expect(screen.getByText(/Increment/i)).toBeInTheDocument();
});
```

### HOC test

- Test both:
  - Base component separately
  - Wrapped component behavior

---

# Ops & Deployment

- Both patterns are **client-side abstractions only**
- No special deployment concerns
- Prefer Hooks in new systems for:
  - Better tree-shaking
  - Less wrapper overhead

- Avoid deep HOC chains (debugging complexity)
- Ensure error boundaries wrap enhanced components

---

# Pitfalls

- ❌ HOC nesting (“wrapper hell”)
- ❌ Render Props inline functions causing re-renders
- ❌ Forgetting to forward refs in HOCs (`forwardRef`)
- ❌ Losing component display name in HOCs (bad debugging)

## Question 5. How do you implement infinite scroll using IntersectionObserver in React?

## Short answer

Infinite scroll with `IntersectionObserver` in React is implemented by placing a **sentinel (trigger) element at the bottom of a list** and observing it. When it becomes visible in the viewport, you fetch the next page of data and append it to the list.

---

# Explanation

## Core idea

Instead of listening to scroll events (which is expensive and prone to performance issues), you use the browser’s **IntersectionObserver API** to efficiently detect when the user reaches the bottom of a list.

### Why IntersectionObserver?

- Runs off the main scroll event loop (more performant)
- Batch-friendly with React 18 automatic batching
- Avoids manual throttling/debouncing
- Works well with virtualization and pagination

---

## Architecture

```text
List items
   ↓
Rendered UI
   ↓
Sentinel div (bottom trigger)
   ↓
IntersectionObserver watches sentinel
   ↓
When visible → fetch next page → append data
```

---

## React 18 behavior notes

- State updates triggered by observer callbacks are **batched automatically**
- Multiple triggers may occur in concurrent rendering → must guard against duplicate fetches
- Strict Mode in dev may run effects twice → requires cleanup logic

---

# Example (React + TypeScript)

## Setup

```bash
npm create vite@latest infinite-scroll -- --template react-ts
cd infinite-scroll
npm install
npm run dev
```

---

## Infinite Scroll Component

```tsx
import { useEffect, useRef, useState, useCallback } from "react";

type Item = {
  id: number;
  title: string;
};

const fetchItems = async (page: number): Promise<Item[]> => {
  // Simulated API
  await new Promise((res) => setTimeout(res, 800));

  return Array.from({ length: 10 }, (_, i) => ({
    id: page * 10 + i,
    title: `Item ${page * 10 + i}`,
  }));
};

export default function InfiniteScroll() {
  const [items, setItems] = useState<Item[]>([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  const observerRef = useRef<IntersectionObserver | null>(null);
  const sentinelRef = useRef<HTMLDivElement | null>(null);

  const loadMore = useCallback(async () => {
    if (loading || !hasMore) return;

    setLoading(true);

    const newItems = await fetchItems(page);

    setItems((prev) => [...prev, ...newItems]);
    setPage((prev) => prev + 1);

    if (newItems.length === 0) {
      setHasMore(false);
    }

    setLoading(false);
  }, [page, loading, hasMore]);

  useEffect(() => {
    loadMore(); // initial load
  }, []);

  useEffect(() => {
    if (!sentinelRef.current) return;

    observerRef.current = new IntersectionObserver(
      (entries) => {
        const target = entries[0];

        if (target.isIntersecting) {
          loadMore();
        }
      },
      {
        root: null, // viewport
        rootMargin: "200px", // prefetch before reaching bottom
        threshold: 0,
      },
    );

    observerRef.current.observe(sentinelRef.current);

    return () => {
      observerRef.current?.disconnect();
    };
  }, [loadMore]);

  return (
    <div style={{ padding: 20 }}>
      <h2>Infinite Scroll</h2>

      {items.map((item) => (
        <div
          key={item.id}
          style={{ padding: 10, borderBottom: "1px solid #ddd" }}
        >
          {item.title}
        </div>
      ))}

      {loading && <p>Loading...</p>}
      {!hasMore && <p>No more items</p>}

      {/* Sentinel element */}
      <div ref={sentinelRef} style={{ height: 20 }} />
    </div>
  );
}
```

---

# Tooling & Setup

### Recommended stack

- **Vite + React + TypeScript**
- Optional:
  - React Query (TanStack Query) for pagination caching
  - Axios or fetch wrapper for API layer

### Why not CRA?

- Slower dev server
- Deprecated ecosystem
- Poor ESM optimization

---

# Performance

## Key optimizations

### 1. Use `rootMargin` prefetching

```js
rootMargin: "200px";
```

Fetches data before user hits bottom → smoother UX

---

### 2. Prevent duplicate calls

Guard against concurrent triggers:

```tsx
if (loading || !hasMore) return;
```

---

### 3. Memoize callback

```tsx
const loadMore = useCallback(...)
```

Prevents observer recreation on every render.

---

### 4. Disconnect observer properly

Avoid memory leaks:

```tsx
return () => observer.disconnect();
```

---

### 5. Consider virtualization for large lists

Use:

- `react-window`
- `react-virtualized`

---

### 6. React Profiler usage

Track:

- Re-renders on scroll
- Expensive DOM updates
- Network-trigger frequency

---

# Testing

## Vitest + React Testing Library

```bash
npm install -D vitest @testing-library/react jsdom
```

### Example test

```tsx
import { render, screen } from "@testing-library/react";
import InfiniteScroll from "./InfiniteScroll";

test("renders initial items", () => {
  render(<InfiniteScroll />);
  expect(screen.getByText(/Infinite Scroll/i)).toBeInTheDocument();
});
```

### Advanced testing

Mock IntersectionObserver:

```tsx
class MockObserver {
  observe = vi.fn();
  disconnect = vi.fn();
  unobserve = vi.fn();
}
```

---

# Ops & Deployment

- Use CDN-backed APIs for fast pagination
- Combine with caching layer (React Query recommended)
- Handle API rate limits (debounce or throttle fallback)
- Add error boundaries for failed fetches
- SSR considerations:
  - Infinite scroll is client-only (disable on server render)

- Use logging for:
  - page index
  - fetch failures
  - observer triggers

---

# Pitfalls

- ❌ Not disconnecting IntersectionObserver → memory leaks
- ❌ Missing dependency array in `useEffect` → duplicate observers
- ❌ Triggering multiple fetches due to rapid intersection events
- ❌ Not handling “no more data” state
- ❌ Using scroll events instead of IntersectionObserver (less performant)

## Question 6. How do you handle memoization of components with dynamic props?

## Short answer

Use `React.memo` to memoize components and ensure that **dynamic props have stable references**. For objects, arrays, and functions, use `useMemo` and `useCallback` in the parent to prevent unnecessary re-renders. If needed, provide a custom comparison function to `React.memo` for fine-grained control.

---

# Explanation

`React.memo` prevents a functional component from re-rendering when its **props have not changed**. By default, React performs a **shallow comparison** using `Object.is`.

Primitive props (`string`, `number`, `boolean`) work well because they are compared by value. However, **objects, arrays, and functions are compared by reference**, so creating a new object or function on every render causes `React.memo` to see the prop as changed.

### Example of the problem

```tsx
const user = { name: "Alice" }; // New object every render
<Profile user={user} />;
```

Even though `user.name` hasn't changed, `Profile` re-renders because `user` is a new object reference.

### Solutions

1. **Memoize objects and arrays with `useMemo`.**
2. **Memoize callbacks with `useCallback`.**
3. **Wrap child components with `React.memo`.**
4. **Use a custom comparison function** only when shallow comparison isn't sufficient and profiling shows it's beneficial.

### React 18 considerations

- **Automatic batching** reduces the number of renders after multiple state updates, but it does not prevent re-renders caused by changing prop references.
- Concurrent rendering may invoke render functions more than once before committing, so memoization should optimize expensive rendering—not be relied on for correctness.
- Avoid overusing memoization; it has its own comparison cost.

---

# Example (React + TypeScript)

## Create a Vite project

```bash
npm create vite@latest memo-demo -- --template react-ts
cd memo-demo
npm install
npm run dev
```

### `UserCard.tsx`

```tsx
import React from "react";

type User = {
  id: number;
  name: string;
};

type Props = {
  user: User;
  onSelect: (id: number) => void;
};

function UserCard({ user, onSelect }: Props) {
  console.log("Rendering:", user.name);

  return (
    <div>
      <p>{user.name}</p>
      <button onClick={() => onSelect(user.id)}>Select</button>
    </div>
  );
}

export default React.memo(UserCard);
```

### `App.tsx`

```tsx
import { useCallback, useMemo, useState } from "react";
import UserCard from "./UserCard";

export default function App() {
  const [count, setCount] = useState(0);

  const user = useMemo(
    () => ({
      id: 1,
      name: "Alice",
    }),
    [],
  );

  const handleSelect = useCallback((id: number) => {
    console.log("Selected:", id);
  }, []);

  return (
    <>
      <button onClick={() => setCount((c) => c + 1)}>Counter: {count}</button>

      <UserCard user={user} onSelect={handleSelect} />
    </>
  );
}
```

Clicking the counter updates the parent state, but `UserCard` does not re-render because both `user` and `handleSelect` keep stable references.

### Custom comparison example

If only a subset of props determines rendering:

```tsx
export default React.memo(UserCard, (prev, next) => {
  return prev.user.id === next.user.id && prev.user.name === next.user.name;
});
```

Use custom comparators sparingly—they run on every parent render and can become more expensive than simply rendering the component.

---

# Tooling & Setup

**Recommended stack:** Vite + React + TypeScript

```bash
npm create vite@latest memo-demo -- --template react-ts
npm install
npm run dev
```

- **Vite** uses native **ES Modules (ESM)** during development for fast startup and HMR.
- Production builds use **Rollup** for tree-shaking and optimized bundles.
- Avoid **Create React App (CRA)**, as it is deprecated.
- For SSR, frameworks like Next.js or Remix support the same memoization techniques in Client Components.

---

# Performance

Memoization should be **driven by profiling**, not applied everywhere.

Recommended optimizations:

- Wrap expensive components with `React.memo`.
- Memoize derived objects and arrays using `useMemo`.
- Memoize callbacks passed to memoized children with `useCallback`.
- Use `React.lazy` and `Suspense` for code splitting.
- Use the React DevTools **Profiler** to verify that memoization reduces rendering work.
- For large collections, combine memoization with virtualization libraries such as `react-window`.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders user name", () => {
  render(<App />);
  expect(screen.getByText("Alice")).toBeInTheDocument();
});
```

To verify memoization, spy on the child component or inspect render counts with React DevTools Profiler. Functional correctness tests should take priority over implementation details.

---

# Ops & Deployment

- Use logging or the React Profiler during development to identify unnecessary renders.
- Wrap application sections with Error Boundaries for resilience.
- Memoization affects runtime performance but not bundle size.
- Optimize bundle size with tree-shaking, route-level code splitting, and lazy loading.
- Serve production bundles via a CDN and enable long-term caching for static assets.

---

# Pitfalls

- Don't create new object, array, or function props inline when passing them to memoized components.
- Don't overuse `React.memo`, `useMemo`, or `useCallback`; each adds maintenance and comparison overhead.
- Don't write expensive custom comparison functions without confirming the performance benefit through profiling.

## Question 7. How do you prevent stale closures in hooks?

## Short answer

A **stale closure** occurs when a Hook callback (`useEffect`, `useCallback`, event handler, `setInterval`, etc.) captures old state or props from a previous render. Prevent stale closures by:

- Including all dependencies in Hook dependency arrays.
- Using **functional state updates** when the next state depends on the previous state.
- Storing mutable values in `useRef` when they need to be read without causing re-renders.
- Memoizing callbacks with `useCallback` and the correct dependencies.
- Avoiding disabling the `react-hooks/exhaustive-deps` ESLint rule unless you fully understand the consequences.

---

# Explanation

React components re-run on every render. Each render creates **new variables and new closures**.

Consider this example:

```tsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      console.log(count);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <button onClick={() => setCount((c) => c + 1)}>+</button>;
}
```

The interval always logs `0` because the callback captured the initial value of `count`. This is a **stale closure**.

### Common causes

- `setInterval` / `setTimeout`
- Event listeners
- Async functions
- Promises
- Missing dependencies in `useEffect`
- Memoized callbacks with incorrect dependency arrays

### React 18 considerations

- Concurrent rendering can make stale closure bugs more noticeable because renders may be interrupted or restarted.
- Automatic batching reduces unnecessary renders but **does not fix stale closures**.
- In development, Strict Mode intentionally mounts, unmounts, and re-runs effects to help detect side-effect bugs, making proper cleanup and dependency management even more important.

---

# Example (React + TypeScript)

## Create a Vite project

```bash
npm create vite@latest stale-closure-demo -- --template react-ts
cd stale-closure-demo
npm install
npm run dev
```

### ❌ Incorrect

```tsx
import { useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount(count + 1); // Uses stale count
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <h2>{count}</h2>;
}
```

The interval always uses the initial `count`.

### ✅ Correct (Functional Update)

```tsx
import { useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((prev) => prev + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <h2>{count}</h2>;
}
```

Functional updates always receive the latest state.

### ✅ Using `useRef` for the latest value

```tsx
import { useEffect, useRef, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);
  const countRef = useRef(count);

  useEffect(() => {
    countRef.current = count;
  }, [count]);

  useEffect(() => {
    const id = setInterval(() => {
      console.log(countRef.current);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <button onClick={() => setCount((c) => c + 1)}>{count}</button>;
}
```

`useRef` provides access to the latest value without recreating the interval.

---

# Tooling & Setup

**Recommended stack:** Vite + React + TypeScript

```bash
npm create vite@latest stale-closure-demo -- --template react-ts
npm install
npm run dev
```

- Vite provides a fast development server with native **ES Modules (ESM)** and HMR.
- Production builds use **Rollup** for optimized bundles.
- Avoid **Create React App (CRA)** because it is deprecated.
- Next.js and Remix use the same Hook patterns in Client Components.

---

# Performance

- Include all dependencies in `useEffect`, `useMemo`, and `useCallback`.
- Use `useCallback` to stabilize callback references when passing them to memoized children.
- Prefer functional state updates instead of adding frequently changing state to dependency arrays when appropriate.
- Use `useRef` for mutable values that shouldn't trigger re-renders.
- Profile with the React DevTools Profiler to verify that dependency changes aren't causing unnecessary renders.
- Use `React.lazy` and code splitting for large applications; memoization and stale closure prevention solve different performance problems.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders counter", () => {
  render(<App />);
  expect(screen.getByRole("button")).toBeInTheDocument();
});
```

For interval-based logic, use fake timers (`vi.useFakeTimers()`) to verify that state updates correctly over time.

---

# Ops & Deployment

- Enable the `eslint-plugin-react-hooks` rules, especially `react-hooks/exhaustive-deps`, to catch missing dependencies early.
- Use Error Boundaries to isolate runtime failures; they don't catch stale closure bugs but improve application resilience.
- Monitor production behavior with logging and error reporting tools to identify issues caused by asynchronous callbacks.
- Bundle optimization (tree-shaking, lazy loading, CDN delivery) is independent of stale closure handling.

---

# Pitfalls

- ❌ Omitting dependencies from `useEffect` or `useCallback` to "avoid re-renders."
- ❌ Using `setState(value + 1)` inside timers or async callbacks instead of functional updates.
- ❌ Disabling the `react-hooks/exhaustive-deps` ESLint rule without understanding why it's reporting an issue.

## Question 8. Explain the difference between `componentDidMount` and `useEffect(() => {}, [])`

## Short answer

`componentDidMount` is a **class component lifecycle method** that runs once after the component is mounted. `useEffect(() => {}, [])` is the **functional component equivalent** that runs after the initial render when given an empty dependency array.

Although they are often compared, **they are not exactly identical**, especially in React 18's Strict Mode and concurrent rendering.

---

# Explanation

## `componentDidMount` (Class Components)

- Runs **once** after the component is mounted.
- Used for:
  - API calls
  - Subscriptions
  - Timers
  - DOM manipulation

- Cannot be used in functional components.

```tsx
class User extends React.Component {
  componentDidMount() {
    console.log("Mounted");
  }

  render() {
    return <h1>User</h1>;
  }
}
```

---

## `useEffect(() => {}, [])` (Function Components)

```tsx
useEffect(() => {
  console.log("Mounted");
}, []);
```

- Runs after the initial render.
- Empty dependency array (`[]`) means React won't re-run it due to state or prop changes.
- Can return a cleanup function for unmounting.

```tsx
useEffect(() => {
  console.log("Mounted");

  return () => {
    console.log("Unmounted");
  };
}, []);
```

Unlike `componentDidMount`, `useEffect` combines the responsibilities of:

- `componentDidMount`
- `componentDidUpdate` (when dependencies change)
- `componentWillUnmount` (via cleanup)

---

# Key Differences (Interview Core)

| Feature                            | `componentDidMount`             | `useEffect(() => {}, [])`                                      |
| ---------------------------------- | ------------------------------- | -------------------------------------------------------------- |
| Component type                     | Class                           | Function                                                       |
| Runs after mount                   | ✅                              | ✅                                                             |
| Runs once in production            | ✅                              | ✅                                                             |
| Cleanup support                    | ❌ (use `componentWillUnmount`) | ✅ Return cleanup function                                     |
| Handles updates                    | ❌                              | ✅ (with dependencies)                                         |
| React 18 Strict Mode (development) | Runs once                       | Effect is intentionally mounted, cleaned up, and mounted again |
| Concurrent rendering support       | Legacy lifecycle                | Designed for modern React                                      |

---

# React 18 & Strict Mode

A common interview topic is that **`useEffect(() => {}, [])` may appear to run twice in development**.

```tsx
useEffect(() => {
  console.log("Mounted");
}, []);
```

In **React 18 Strict Mode (development only)**, React intentionally performs:

1. Mount
2. Run effect
3. Cleanup
4. Mount again
5. Run effect again

This helps detect side effects that aren't properly cleaned up.

**Production behavior:**

- The effect runs only once after mount.

`componentDidMount` in class components does **not** undergo this intentional effect re-execution.

---

# Example (React + TypeScript)

## Create a Vite project

```bash
npm create vite@latest lifecycle-demo -- --template react-ts
cd lifecycle-demo
npm install
npm run dev
```

### Functional Component

```tsx
import { useEffect } from "react";

export default function App() {
  useEffect(() => {
    console.log("Component mounted");

    return () => {
      console.log("Component unmounted");
    };
  }, []);

  return <h1>Hello React</h1>;
}
```

### Equivalent Class Component

```tsx
import React from "react";

class App extends React.Component {
  componentDidMount() {
    console.log("Component mounted");
  }

  componentWillUnmount() {
    console.log("Component unmounted");
  }

  render() {
    return <h1>Hello React</h1>;
  }
}

export default App;
```

---

# Tooling & Setup

**Recommended stack:** Vite + React + TypeScript

```bash
npm create vite@latest lifecycle-demo -- --template react-ts
npm install
npm run dev
```

Why Vite?

- Fast HMR
- Native ESM in development
- Rollup for optimized production builds
- **Avoid Create React App (CRA)**, as it is deprecated.

Framework notes:

- **Next.js:** `useEffect` only runs on the client after hydration. It never runs during server rendering.
- **Remix:** Same client-side behavior for `useEffect`.

---

# Performance

- Use `useEffect` only for **side effects**, not for deriving state that can be computed during rendering.
- Always clean up:
  - Timers
  - Event listeners
  - WebSocket subscriptions
  - `IntersectionObserver`

- Memoize callbacks with `useCallback` if they're dependencies of effects.
- Use the React DevTools Profiler to identify unnecessary renders.
- Combine with `React.lazy` and route-level code splitting for better performance.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render } from "@testing-library/react";
import App from "./App";

test("renders component", () => {
  render(<App />);
});
```

For effects involving timers or asynchronous work, use fake timers (`vi.useFakeTimers()`) or `waitFor` from React Testing Library.

---

# Ops & Deployment

- Keep effects idempotent so they behave correctly under React 18 Strict Mode.
- Use Error Boundaries to catch rendering errors (they don't catch errors inside asynchronous effects).
- Minimize bundle size with tree-shaking and lazy loading.
- Serve optimized assets through a CDN.
- In SSR applications, remember that `useEffect` runs only in the browser after hydration.

---

# Pitfalls

- ❌ Assuming `useEffect(() => {}, [])` is **exactly** the same as `componentDidMount`; it isn't, especially in React 18 development mode.
- ❌ Performing data derivation in `useEffect` instead of computing it during render.
- ❌ Forgetting cleanup for subscriptions, timers, or event listeners, leading to memory leaks.

## Question 9. How do you handle forms with dynamic fields in React?

Handling **dynamic forms** in React means managing fields that can be **added, removed, or modified at runtime**, such as multiple addresses, phone numbers, or education records.

---

## Approach 1: Using `useState` (Most Common)

Store form data as an object or array.

```jsx
import { useState } from "react";

function DynamicForm() {
  const [users, setUsers] = useState([{ name: "", email: "" }]);

  const handleChange = (index, event) => {
    const values = [...users];
    values[index][event.target.name] = event.target.value;
    setUsers(values);
  };

  const addUser = () => {
    setUsers([...users, { name: "", email: "" }]);
  };

  const removeUser = (index) => {
    const values = [...users];
    values.splice(index, 1);
    setUsers(values);
  };

  return (
    <div>
      {users.map((user, index) => (
        <div key={index}>
          <input
            name="name"
            value={user.name}
            onChange={(e) => handleChange(index, e)}
            placeholder="Name"
          />

          <input
            name="email"
            value={user.email}
            onChange={(e) => handleChange(index, e)}
            placeholder="Email"
          />

          <button onClick={() => removeUser(index)}>Remove</button>
        </div>
      ))}

      <button onClick={addUser}>Add User</button>
    </div>
  );
}

export default DynamicForm;
```

---

## Data Structure

Instead of separate state variables:

```jsx
const [name, setName] = useState("");
const [email, setEmail] = useState("");
```

Use an array:

```jsx
[
  {
    name: "John",
    email: "john@test.com",
  },
  {
    name: "Alice",
    email: "alice@test.com",
  },
];
```

This allows unlimited dynamic fields.

---

## Updating a Specific Field

```jsx
const handleChange = (index, e) => {
  const values = [...users];
  values[index][e.target.name] = e.target.value;
  setUsers(values);
};
```

Steps:

1. Copy state.
2. Find the correct object.
3. Update the property.
4. Save new state.

---

## Adding New Fields

```jsx
setUsers([...users, { name: "", email: "" }]);
```

This appends another blank form section.

---

## Removing Fields

```jsx
const removeUser = (index) => {
  setUsers(users.filter((_, i) => i !== index));
};
```

Using `filter` is often preferred over `splice` because it avoids mutating the copied array.

---

## Dynamic Validation

You can validate each object individually.

```jsx
users.map((user) => ({
  nameError: user.name === "",
  emailError: !user.email.includes("@"),
}));
```

Example:

```jsx
{
  !user.name && <p>Name is required</p>;
}
```

---

## Using `useReducer` for Complex Forms

When the form becomes large:

```jsx
const reducer = (state, action) => {
  switch (action.type) {
    case "UPDATE":
      return state.map((item, index) =>
        index === action.index
          ? { ...item, [action.field]: action.value }
          : item,
      );

    case "ADD":
      return [...state, { name: "", email: "" }];

    case "REMOVE":
      return state.filter((_, i) => i !== action.index);

    default:
      return state;
  }
};
```

`useReducer` centralizes state updates and scales better than multiple `useState` calls.

---

## Using Form Libraries

For large applications, libraries simplify handling dynamic fields:

- **React Hook Form** (`useFieldArray`) — Excellent performance and minimal re-renders.
- **Formik** — Good for forms with built-in validation.
- **Final Form** — Another option for complex form state management.

For example, `useFieldArray` in React Hook Form is specifically designed for dynamically adding and removing groups of fields.

---

## Best Practices

- Use an **array of objects** for repeatable form sections.
- Keep inputs **controlled** by binding `value` and `onChange`.
- Prefer **stable IDs** over array indexes as React `key`s when items can be reordered or deleted.
- Avoid mutating state directly; create new arrays/objects using the spread operator or methods like `map` and `filter`.
- Validate each dynamic section independently.
- Consider `useReducer` or a form library for complex forms with nested data.

---

## Interview Answer (Short)

> Dynamic forms are typically implemented by storing form sections in an array of objects using `useState` or `useReducer`. Each object represents one group of fields. Inputs are rendered by mapping over the array, and users can add or remove sections by updating the array immutably. For larger forms, libraries like React Hook Form with `useFieldArray` provide efficient handling of dynamic fields and validation.

## Question 10. How do you manage state across multiple components using context?

The **Context API** allows you to share state across multiple components **without manually passing props through every level** (prop drilling). A context consists of:

1. **Context** – created using `createContext()`
2. **Provider** – supplies the state to descendant components
3. **Consumer** – accesses the state using `useContext()` or `Context.Consumer`

---

## Step 1: Create a Context

```jsx
// ThemeContext.js
import { createContext } from "react";

export const ThemeContext = createContext();
```

---

## Step 2: Create a Provider

The provider stores the shared state.

```jsx
// ThemeProvider.js
import { useState } from "react";
import { ThemeContext } from "./ThemeContext";

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export default ThemeProvider;
```

---

## Step 3: Wrap the Application

```jsx
import ThemeProvider from "./ThemeProvider";

function App() {
  return (
    <ThemeProvider>
      <Navbar />
      <Dashboard />
      <Footer />
    </ThemeProvider>
  );
}
```

Every component inside `ThemeProvider` can access the shared state.

---

## Step 4: Consume the Context

```jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

function Navbar() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div className={theme}>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}
```

Any component within the provider tree can read and update the shared state.

---

## Sharing More Than One Value

```jsx
const value = {
  user,
  theme,
  language,
  cart,
  login,
  logout,
  toggleTheme,
};
```

These values become available to all descendant components.

---

## Using Multiple Contexts

Instead of one large context, separate unrelated concerns.

```jsx
<AuthProvider>
  <ThemeProvider>
    <CartProvider>
      <App />
    </CartProvider>
  </ThemeProvider>
</AuthProvider>
```

This improves maintainability and reduces unnecessary re-renders.

---

## Optimizing Performance

Since any change to a context value causes all consuming components to re-render, consider these optimizations:

- Memoize the context value:

```jsx
const value = useMemo(() => ({ theme, toggleTheme }), [theme]);
```

- Memoize callback functions:

```jsx
const toggleTheme = useCallback(() => {
  setTheme((prev) => (prev === "light" ? "dark" : "light"));
}, []);
```

- Split large contexts into smaller ones (e.g., `ThemeContext`, `AuthContext`, `CartContext`) so updates affect only relevant consumers.

---

## When to Use Context

Use Context for data that many components need, such as:

- Theme (light/dark mode)
- Authenticated user
- Language/localization
- User preferences
- Shopping cart
- Global UI state (e.g., notifications)

Avoid using Context for state that is only needed by a parent and its immediate children—passing props is often simpler.

---

## Context vs Props

| Props                                  | Context                                  |
| -------------------------------------- | ---------------------------------------- |
| Pass data from parent to child         | Share data across many components        |
| Explicit and easy to trace             | Avoids prop drilling                     |
| Best for local/component-specific data | Best for app-wide or shared state        |
| No extra setup                         | Requires creating a context and provider |

---

## Interview Answer (Short)

> To manage state across multiple components, I use React's Context API. I create a context with `createContext`, store the shared state inside a provider using `useState` or `useReducer`, and wrap the required part of the application with the provider. Child components access the shared state using `useContext`, eliminating prop drilling. For better performance, I memoize the context value with `useMemo`, memoize callbacks with `useCallback`, and split unrelated state into separate contexts to minimize unnecessary re-renders.`

## Question 11. How do you implement dark mode in React?

## Question 12. How do you debounce input changes in React?

## Question 13. How do you implement image lazy loading with `React.lazy` and `Suspense`?

## Question 14. How do you handle error messages in forms in React?

## Question 15. How do you implement client-side caching in React?

## Question 16. How do you implement optimistic UI updates in React?

## Question 17. How do you persist state across page refreshes?

## Question 18. How do you integrate React with third-party libraries like Chart.js or D3.js?

## Question 19. How do you implement a search/filter component in React?

## Question 20. How do you implement multi-step forms in React?
