# Set 3

| #   | Question                                                                                                                                            |
| --- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is useMemo and how is it used?](#question-1-what-is-usememo-and-how-is-it-used)                                                               |
| 2   | [What is useCallback and why is it useful?](#question-2-what-is-usecallback-and-why-is-it-useful)                                                   |
| 3   | [Explain useReducer hook with an example](#question-3-explain-usereducer-hook-with-an-example)                                                      |
| 4   | [Difference between useState and useReducer](#question-4-difference-between-usestate-and-usereducer)                                                |
| 5   | [What is context API? How is it used?](#question-5-what-is-context-api-how-is-it-used)                                                              |
| 6   | [How do you handle side effects in React?](#question-6-how-do-you-handle-side-effects-in-react)                                                     |
| 7   | [Explain higher-order components (HOC)](#question-7-explain-higher-order-components-hoc)                                                            |
| 8   | [What is React Portal? Give a use case](#question-8-what-is-react-portal-give-a-use-case)                                                           |
| 9   | [How do you handle errors in React? Explain error boundaries](#question-9-how-do-you-handle-errors-in-react-explain-error-boundaries)               |
| 10  | [Difference between controlled and uncontrolled forms](#question-10-difference-between-controlled-and-uncontrolled-forms)                           |
| 11  | [How do you optimize React performance?](#question-11-how-do-you-optimize-react-performance)                                                        |
| 12  | [Explain React lazy loading and suspense](#question-12-explain-react-lazy-loading-and-suspense)                                                     |
| 13  | [What is code splitting in React?](#question-13-what-is-code-splitting-in-react)                                                                    |
| 14  | [Difference between client-side and server-side rendering in React](#question-14-difference-between-client-side-and-server-side-rendering-in-react) |
| 15  | [What is React Router? Explain its basic usage](#question-15-what-is-react-router-explain-its-basic-usage)                                          |
| 16  | [Difference between BrowserRouter and HashRouter](#question-16-difference-between-browserrouter-and-hashrouter)                                     |
| 17  | [Explain route parameters and query parameters in React Router](#question-17-explain-route-parameters-and-query-parameters-in-react-router)         |
| 18  | [How do you redirect programmatically in React Router?](#question-18-how-do-you-redirect-programmatically-in-react-router)                          |
| 19  | [Explain the difference between React.memo and PureComponent](#question-19-explain-the-difference-between-reactmemo-and-purecomponent)              |
| 20  | [How do you prevent unnecessary re-renders in React?](#question-20-how-do-you-prevent-unnecessary-re-renders-in-react)                              |

## Question 1. What is useMemo and how is it used?

## Short answer

`useMemo` is a React Hook that memoizes the result of an expensive computation so it is only recalculated when its dependencies change, improving performance by avoiding unnecessary recalculations on re-renders.

---

## Explanation

### What `useMemo` does

`useMemo` caches a **computed value**, not a function. React will re-run the computation only when dependencies in the dependency array change.

```ts
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

### Rendering behavior (React 18+)

- React re-renders a component whenever state/props/context changes.
- Without `useMemo`, expensive computations run **on every render**.
- With `useMemo`, React skips recomputation unless dependencies change.
- Works with React 18 concurrent rendering, but memoization is not a guarantee—it’s an optimization hint.

### When it helps

- Expensive calculations (filtering large lists, complex transformations)
- Derived state from props/state
- Preventing referential changes that trigger child re-renders (especially with `React.memo`)

### Trade-offs

- Memory overhead (cached value stored)
- Dependency correctness is critical (stale values bug risk)
- Not useful for cheap computations (often worse than no memoization)

---

## Example (React + TypeScript)

### Setup (Vite recommended)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### Component using `useMemo`

```tsx
import React, { useMemo, useState } from "react";

type Item = {
  id: number;
  name: string;
};

const items: Item[] = Array.from({ length: 10000 }, (_, i) => ({
  id: i,
  name: `Item ${i}`,
}));

export default function FilterList() {
  const [query, setQuery] = useState("");

  const filteredItems = useMemo(() => {
    console.log("Filtering expensive list...");
    return items.filter((item) =>
      item.name.toLowerCase().includes(query.toLowerCase()),
    );
  }, [query]);

  return (
    <div>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />

      <ul>
        {filteredItems.slice(0, 20).map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Tooling & Setup

- Prefer **Vite + React + TypeScript** (fast HMR, ESM-first, minimal config)
- Avoid CRA (deprecated, slower build system)
- In modern apps:
  - Vite uses native ES Modules in dev
  - Production bundling via Rollup

- Alternative:
  - Next.js (SSR + App Router) if you need server rendering or full-stack features

---

## Performance

### When to use

- Large array filtering, sorting, grouping
- Derived computations used in render
- Stabilizing values passed to memoized children

### When NOT to use

- Simple arithmetic or string formatting
- Every small computation (adds overhead)

### Optimization stack

- `useMemo` → memoize values
- `useCallback` → memoize functions
- `React.memo` → memoize components
- React DevTools Profiler → detect re-renders
- Code splitting: `React.lazy` + `Suspense`

---

## Testing

Using **Vitest + React Testing Library**

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import FilterList from "./FilterList";

test("renders input", () => {
  render(<FilterList />);
  expect(screen.getByPlaceholderText("Search...")).toBeInTheDocument();
});
```

Tip: `useMemo` is usually tested indirectly by verifying performance-sensitive behavior, not implementation.

---

## Ops & Deployment

- Use **SSR (Next.js)** if SEO or initial render performance matters
- For CSR apps (Vite):
  - Deploy to CDN (Vercel, Netlify, Cloudflare Pages)

- Use error boundaries for runtime safety
- Monitor bundle size (`vite-plugin-visualizer`)
- Logging: Sentry or OpenTelemetry

---

## Pitfalls

- Incorrect dependency array → stale or buggy values
- Overusing `useMemo` → performance degradation instead of improvement
- Confusing `useMemo` with `useCallback` (value vs function memoization)

## Question 2. What is useCallback and why is it useful?

## Short answer

`useCallback` is a React Hook that memoizes a **function reference**, returning the same function instance across renders until one of its dependencies changes. It is useful for preventing unnecessary re-renders of memoized child components and avoiding repeated setup/cleanup in Hooks that depend on callback references.

---

## Explanation

### What `useCallback` does

Normally, every time a component re-renders, JavaScript creates new function objects.

```tsx
const handleClick = () => {
  console.log("Clicked");
};
```

Even though the logic is identical, `handleClick` is a **new function reference** on every render.

`useCallback` caches that function:

```tsx
const handleClick = useCallback(() => {
  console.log("Clicked");
}, []);
```

React returns the **same function instance** until one of the dependencies changes.

---

### Syntax

```tsx
const memoizedCallback = useCallback(() => {
  // callback logic
}, [dependencies]);
```

---

### Rendering behavior (React 18+)

React 18 automatically batches state updates, but it **does not automatically memoize functions**.

Without `useCallback`:

- Parent re-renders
- New callback created
- Memoized child (`React.memo`) sees a changed prop
- Child re-renders unnecessarily

With `useCallback`:

- Parent re-renders
- Callback reference remains stable
- `React.memo` child can skip rendering if other props are unchanged

---

### Common use cases

#### 1. Prevent unnecessary child re-renders

Works especially well with `React.memo`.

#### 2. Stable dependencies in Hooks

Useful when passing callbacks to:

- `useEffect`
- `useMemo`
- Custom hooks

to avoid unnecessary effect executions.

#### 3. Event handlers passed deep into component trees

Keeps callback identity stable when many memoized components receive the same handler.

---

### `useMemo` vs `useCallback`

| Hook          | Memoizes       | Returns  |
| ------------- | -------------- | -------- |
| `useMemo`     | Computed value | Value    |
| `useCallback` | Function       | Function |

Internally:

```tsx
useCallback(fn, deps);
```

is conceptually equivalent to:

```tsx
useMemo(() => fn, deps);
```

---

## Example (React + TypeScript)

### Setup (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### Parent and memoized child

```tsx
import React, { memo, useCallback, useState } from "react";

type ButtonProps = {
  onClick: () => void;
};

const Button = memo(({ onClick }: ButtonProps) => {
  console.log("Button rendered");

  return <button onClick={onClick}>Increment</button>;
});

export default function App() {
  const [count, setCount] = useState(0);
  const [theme, setTheme] = useState("light");

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return (
    <>
      <h2>Count: {count}</h2>

      <Button onClick={increment} />

      <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
        Toggle Theme
      </button>
    </>
  );
}
```

### What happens?

Without `useCallback`:

- Toggling the theme creates a new `increment` function.
- `Button` receives a new function prop.
- `React.memo` cannot skip rendering.

With `useCallback`:

- `increment` keeps the same reference.
- `Button` skips re-rendering when only the theme changes.

---

## Tooling & Setup

- Prefer **Vite + React + TypeScript** for new projects.
- Avoid **Create React App (CRA)** because it is deprecated.
- Vite provides:
  - Fast HMR
  - Native ESM during development
  - Rollup for optimized production bundles

- Use **Next.js** when you need SSR, Server Components, or full-stack routing.

---

## Performance

Use `useCallback` only when it provides measurable benefit.

Good candidates:

- Functions passed to `React.memo` components
- Dependencies of `useEffect`
- Dependencies of custom hooks
- Expensive child component trees

Avoid using it for:

- Every event handler
- Small components
- Functions never passed as props

Profile first with the **React DevTools Profiler** to confirm it reduces unnecessary renders.

Other optimizations include:

- `React.memo` for component memoization
- `useMemo` for expensive computed values
- `React.lazy` and `Suspense` for code splitting
- Data caching with libraries such as TanStack Query or SWR

---

## Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders increment button", () => {
  render(<App />);
  expect(screen.getByText("Increment")).toBeInTheDocument();
});
```

Rather than testing `useCallback` directly, test that memoized components avoid unnecessary renders or that behavior remains correct after state updates.

---

## Ops & Deployment

- Use React DevTools Profiler to verify callback memoization is actually reducing renders.
- Add logging only in development to detect unnecessary re-renders.
- Wrap UI sections with Error Boundaries for resilience.
- Consider SSR (Next.js) for improved SEO and initial load performance.
- Optimize bundles with dynamic imports and lazy loading.
- Deploy optimized builds to a CDN (e.g., Vercel, Netlify, Cloudflare Pages).

---

## Pitfalls

- **Overusing `useCallback`** can add unnecessary complexity and memory overhead.
- **Missing dependencies** can lead to stale closures and bugs; rely on the `eslint-plugin-react-hooks` exhaustive-deps rule.
- **Expecting performance gains everywhere**—`useCallback` is most effective when paired with `React.memo` or when callback identity matters.

## Question 3. Explain useReducer hook with an example

## Short answer

`useReducer` is a React Hook used to manage **complex state logic** by centralizing state updates in a **reducer function**. It is an alternative to `useState` when state has multiple related values, complex transitions, or when the next state depends on the previous state. It follows the same principles as Redux but is built into React and scoped to a component.

---

## Explanation

### What is `useReducer`?

Instead of updating state directly, you **dispatch actions** that describe _what happened_. A reducer function receives the current state and the action, then returns the next state.

```tsx
const [state, dispatch] = useReducer(reducer, initialState);
```

Where:

- **state** → current state
- **dispatch(action)** → sends an action to the reducer
- **reducer(state, action)** → returns the new state

---

### Syntax

```tsx
const [state, dispatch] = useReducer(reducer, initialState);

function reducer(state, action) {
  switch (action.type) {
    case "ACTION":
      return { ...state };
    default:
      return state;
  }
}
```

---

### Rendering behavior (React 18+)

- Calling `dispatch()` schedules a state update and triggers a re-render if the state changes.
- React 18 automatically batches multiple `dispatch` calls made within the same event.
- The reducer **must be pure**:
  - No API calls
  - No mutations
  - No side effects

- Reducers should always return a **new state object** rather than mutating the existing one.

---

### When to use `useReducer`

Use `useReducer` when:

- State has multiple related fields.
- There are many possible state transitions.
- State update logic becomes difficult to manage with multiple `useState` calls.
- Building forms, wizards, shopping carts, authentication flows, or complex UI state.

Prefer `useState` for simple state like toggles, counters, or input values.

---

## Example (React + TypeScript)

### Setup (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### Counter using `useReducer`

```tsx
import { useReducer } from "react";

type State = {
  count: number;
};

type Action = { type: "increment" } | { type: "decrement" } | { type: "reset" };

const initialState: State = {
  count: 0,
};

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };

    case "decrement":
      return { count: state.count - 1 };

    case "reset":
      return initialState;

    default:
      return state;
  }
}

export default function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h2>Count: {state.count}</h2>

      <button onClick={() => dispatch({ type: "increment" })}>+</button>

      <button onClick={() => dispatch({ type: "decrement" })}>-</button>

      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </div>
  );
}
```

### Flow

1. User clicks **+**.
2. `dispatch({ type: "increment" })` is called.
3. React invokes `reducer(state, action)`.
4. Reducer returns `{ count: state.count + 1 }`.
5. React updates the state and re-renders the component.

---

## Tooling & Setup

- **Recommended:** Vite + React + TypeScript for fast development and type safety.
- **Avoid:** Create React App (CRA), as it is deprecated.
- **Bundler:** Vite uses native **ES Modules (ESM)** in development and Rollup for optimized production builds.
- **Alternative:** Use Next.js when SSR, Server Components, or full-stack routing is required.

---

## Performance

- Keep reducers **pure and lightweight**; expensive calculations belong in `useMemo`.
- Split large reducers into smaller reducers or custom hooks to improve maintainability.
- Combine `useReducer` with **Context API** for application-wide state without introducing Redux for smaller apps.
- Use `React.memo`, `useCallback`, and `useMemo` where appropriate to reduce unnecessary renders.
- Profile state updates with the **React DevTools Profiler**.
- Use `React.lazy` and `Suspense` for code splitting, and libraries like TanStack Query for caching server state.

---

## Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example reducer unit test:

```tsx
import { expect, test } from "vitest";

test("increments count", () => {
  const state = { count: 0 };

  expect(reducer(state, { type: "increment" })).toEqual({ count: 1 });
});
```

Reducer functions are easy to unit test because they are pure functions.

---

## Ops & Deployment

- Keep reducers free of side effects; perform API calls in event handlers or `useEffect`.
- Use Error Boundaries to isolate rendering failures.
- For SSR (e.g., Next.js), ensure reducer initial state is deterministic to avoid hydration mismatches.
- Optimize bundle size by code-splitting large feature modules.
- Deploy optimized builds to a CDN (e.g., Vercel, Netlify, Cloudflare Pages).

---

## Pitfalls

- **Mutating state** inside the reducer instead of returning a new object.
- **Putting side effects** (API calls, timers, logging) inside the reducer.
- **Using `useReducer` for simple state**, where `useState` is simpler and more readable.

## Question 4. Difference between useState and useReducer

## Short answer

`useState` is a simple state management hook for **independent or straightforward state updates**, while `useReducer` is used for **complex state logic with multiple transitions**, where state changes are better managed through dispatched actions and a reducer function.

---

## Explanation

### Core conceptual difference

#### `useState`

- Direct state updates
- Best for simple values (boolean, string, number, small objects)
- You set state explicitly

```ts
setCount(count + 1);
```

#### `useReducer`

- Action-driven state updates
- Centralized state transition logic
- You dispatch actions instead of directly setting state

```ts
dispatch({ type: "increment" });
```

---

### Mental model

| Hook         | Mental Model                                         |
| ------------ | ---------------------------------------------------- |
| `useState`   | “Set this value directly”                            |
| `useReducer` | “Tell me what happened, I’ll compute the next state” |

---

### When React 18 updates state

Both hooks:

- Trigger re-render when state changes
- Use automatic batching (React 18+)
- Work with concurrent rendering

But:

- `useState` → updates are inline and local
- `useReducer` → updates go through a **pure reducer function**

---

## Key differences

### 1. Complexity of state logic

- `useState` → simple updates
- `useReducer` → complex transitions, multiple actions

Example:

- Counter → `useState`
- Form with validation + multiple fields → `useReducer`

---

### 2. State structure

| useState                | useReducer                        |
| ----------------------- | --------------------------------- |
| Independent values      | Structured state machine          |
| Multiple useState calls | Single reducer managing all state |

---

### 3. Update logic location

- `useState`: logic scattered in event handlers
- `useReducer`: logic centralized in reducer function

---

### 4. Scalability

- `useState`: can become messy as logic grows
- `useReducer`: scales better for complex flows

---

### 5. Testability

- `useState`: harder to test logic (embedded in component)
- `useReducer`: reducer is a pure function → easy unit testing

---

## Example (React + TypeScript)

### Setup (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

### Side-by-side comparison

#### 1. useState version

```tsx id="ustate_ex"
import { useState } from "react";

export default function CounterState() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>

      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

---

#### 2. useReducer version

```tsx id="ureducer_ex"
import { useReducer } from "react";

type State = { count: number };

type Action = { type: "increment" } | { type: "decrement" } | { type: "reset" };

const initialState: State = { count: 0 };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return initialState;
    default:
      return state;
  }
}

export default function CounterReducer() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      <h2>Count: {state.count}</h2>

      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "reset" })}>Reset</button>
    </div>
  );
}
```

---

## Tooling & Setup

- Use **Vite + React + TypeScript** (preferred modern setup)
- Avoid CRA (deprecated, slower, less flexible)
- Use **Next.js** when:
  - SSR is required
  - SEO matters
  - Server Components or API routes are needed

---

## Performance considerations

### `useState`

- Faster for simple updates
- Less abstraction overhead

### `useReducer`

- Better for complex state transitions
- Avoids scattered logic, improving maintainability

### Optimization strategies

- Use `useReducer` to avoid prop drilling of multiple setters
- Combine with `React.memo` for preventing re-renders
- Use `useMemo` for derived state
- Use `useCallback` for stable dispatch handlers
- Profile with React DevTools Profiler before optimizing

---

## Testing

### `useState`

- Test behavior through UI interactions

### `useReducer`

- Test reducer directly (pure function)

```ts id="test_reducer"
import { expect, test } from "vitest";

test("increment action", () => {
  const state = { count: 0 };

  const newState = reducer(state, { type: "increment" });

  expect(newState.count).toBe(1);
});
```

---

## Ops & Deployment

- Keep `useState` for local UI state (toggles, inputs)
- Use `useReducer` for:
  - form state
  - multi-step workflows
  - complex UI state machines

- Avoid mixing both unnecessarily in large components
- Ensure predictable state transitions for SSR hydration (Next.js)

---

## Pitfalls

- Using `useReducer` for trivial state (adds unnecessary complexity)
- Using `useState` for complex multi-action workflows (becomes unmaintainable)
- Duplicating logic across multiple `setState` calls instead of centralizing it

## Question 5. What is context API? How is it used?

## Short answer

The **Context API** in React is a built-in feature for sharing data **globally across a component tree** without prop drilling. It is commonly used for app-wide state like theme, authentication, language, or user settings.

---

## Explanation

### What problem Context solves

Without Context:

```txt
App → Layout → Header → Navbar → Button
           (props passed through many levels)
```

This is called **prop drilling**, where intermediate components receive props they don’t actually use.

With Context:

```txt
App → Provider → Any nested component can directly access state
```

---

### Core pieces of Context API

1. **createContext** → creates a context object
2. **Provider** → supplies the value
3. **useContext** → consumes the value

---

### How it works internally

- React stores context value at the nearest Provider above the component.
- When the Provider value changes:
  - All consuming components re-render
  - React re-evaluates context dependencies during render phase (React 18 concurrent rendering supports interruptible updates)

⚠️ Important: Context is **not a state manager like Redux**—it is a **dependency injection mechanism**.

---

### When to use Context API

Good use cases:

- Theme (dark/light mode)
- Authentication state (user info, tokens)
- Localization (i18n)
- Global UI state (modal visibility, sidebar state)

Avoid:

- High-frequency updates (mouse position, animations)
- Large complex state trees (better: Redux, Zustand, TanStack Query)

---

## Example (React + TypeScript)

### Setup (Vite recommended)

```bash id="ctx_setup"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

### 1. Create Context

```tsx id="ctx_1"
import { createContext, useContext, useState, ReactNode } from "react";

type AuthContextType = {
  user: string | null;
  login: (name: string) => void;
  logout: () => void;
};

const AuthContext = createContext<AuthContextType | undefined>(undefined);
```

---

### 2. Provider Component

```tsx id="ctx_2"
export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<string | null>(null);

  const login = (name: string) => setUser(name);
  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}
```

---

### 3. Custom Hook (best practice)

```tsx id="ctx_3"
export function useAuth() {
  const context = useContext(AuthContext);

  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }

  return context;
}
```

---

### 4. Consuming Context

```tsx id="ctx_4"
import { useAuth } from "./AuthContext";

export default function Navbar() {
  const { user, login, logout } = useAuth();

  return (
    <div>
      {user ? (
        <>
          <p>Welcome, {user}</p>
          <button onClick={logout}>Logout</button>
        </>
      ) : (
        <button onClick={() => login("John")}>Login</button>
      )}
    </div>
  );
}
```

---

### 5. Wrap App

```tsx id="ctx_5"
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { AuthProvider } from "./AuthContext";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <AuthProvider>
      <App />
    </AuthProvider>
  </React.StrictMode>,
);
```

---

## Tooling & Setup

- Preferred stack:
  - **Vite + React + TypeScript**

- Avoid CRA (deprecated)
- For large apps:
  - Next.js App Router if SSR or server-side auth is needed

Context works in both:

- CSR (Vite)
- SSR (Next.js) — but initial hydration must match server state

---

## Performance

### Key performance insight

Context updates cause **all consumers to re-render**, even if only part of the value changed.

### Optimization strategies

- **Split contexts**
  - e.g. AuthContext, ThemeContext separately

- Memoize provider value:

```tsx id="ctx_perf"
const value = useMemo(() => ({ user, login, logout }), [user]);
```

- Use `React.memo` for consumers
- Avoid putting frequently changing state in Context
- Combine with:
  - `useReducer` for structured updates
  - Zustand or Redux for large-scale apps

### React 18 behavior

- Context updates are **batched automatically**
- Concurrent rendering may interrupt renders but ensures consistency

---

## Testing

Using **Vitest + React Testing Library**

```bash id="ctx_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx id="ctx_test_ex"
import { render, screen } from "@testing-library/react";
import { AuthProvider } from "./AuthContext";
import Navbar from "./Navbar";

test("renders login button", () => {
  render(
    <AuthProvider>
      <Navbar />
    </AuthProvider>,
  );

  expect(screen.getByText("Login")).toBeInTheDocument();
});
```

---

## Ops & Deployment

- Avoid storing sensitive tokens directly in Context (use secure storage or server sessions)
- Use SSR frameworks (Next.js) for authenticated initial render
- Keep Context value minimal to reduce rerenders
- Add Error Boundaries around provider consumers for resilience
- Monitor unnecessary re-renders using React DevTools Profiler
- Deploy via CDN (Vercel/Netlify/Cloudflare Pages)

---

## Pitfalls

- ❌ Using Context for everything (leads to performance issues)
- ❌ Not memoizing provider value → unnecessary re-renders
- ❌ Large monolithic context objects (hard to maintain + slow updates)

## Question 6. How do you handle side effects in React?

## Question 7. Explain higher-order components (HOC)

## Question 8. What is React Portal? Give a use case

## Question 9. How do you handle errors in React? Explain error boundaries

## Question 10. Difference between controlled and uncontrolled forms

## Question 11. How do you optimize React performance?

## Question 12. Explain React lazy loading and suspense

## Question 13. What is code splitting in React?

## Question 14. Difference between client-side and server-side rendering in React

## Question 15. What is React Router? Explain its basic usage

## Question 16. Difference between BrowserRouter and HashRouter

## Question 17. Explain route parameters and query parameters in React Router

## Question 18. How do you redirect programmatically in React Router?

## Question 19. Explain the difference between React.memo and PureComponent

## Question 20. How do you prevent unnecessary re-renders in React?
