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

## Short answer

Side effects in React are handled primarily using the **`useEffect` hook** (and related hooks like `useLayoutEffect` or external libraries like React Query). Side effects include anything that interacts with the outside world, such as API calls, subscriptions, timers, DOM manipulation, or logging.

---

## Explanation

### What are side effects?

A **side effect** is any operation that happens outside the pure rendering process.

Examples:

- Fetching data from APIs
- Subscribing/unsubscribing to events
- Setting timers (`setTimeout`, `setInterval`)
- Manual DOM manipulation
- Logging, analytics tracking

React rendering must remain **pure**, meaning:

> Given the same props and state, rendering should always return the same UI.

So side effects are separated from rendering using lifecycle-aware hooks.

---

### Primary tool: `useEffect`

```tsx id="fx1"
useEffect(() => {
  // side effect here
  return () => {
    // cleanup here
  };
}, [dependencies]);
```

---

### How React executes effects (React 18+)

- Render phase: React calculates UI (pure, no side effects)
- Commit phase: DOM updates happen
- Post-commit: `useEffect` runs asynchronously after paint
- Cleanup runs before re-running effect or unmounting

React 18 concurrent rendering may:

- Pause or restart renders
- Re-run effects in Strict Mode (development only) to detect unsafe side effects

---

## Common side effect patterns

### 1. API calls (data fetching)

```tsx id="fx2"
import { useEffect, useState } from "react";

type User = { id: number; name: string };

export default function Users() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    let isMounted = true;

    async function fetchUsers() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();

      if (isMounted) {
        setUsers(data);
      }
    }

    fetchUsers();

    return () => {
      isMounted = false;
    };
  }, []);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

---

### 2. Event listeners

```tsx id="fx3"
import { useEffect } from "react";

export default function WindowResize() {
  useEffect(() => {
    const handleResize = () => {
      console.log(window.innerWidth);
    };

    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return <div>Resize the window</div>;
}
```

---

### 3. Timers

```tsx id="fx4"
import { useEffect, useState } from "react";

export default function Timer() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);

    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;
}
```

---

### 4. DOM manipulation (rare cases)

```tsx id="fx5"
import { useEffect, useRef } from "react";

export default function FocusInput() {
  const ref = useRef<HTMLInputElement>(null);

  useEffect(() => {
    ref.current?.focus();
  }, []);

  return <input ref={ref} />;
}
```

---

## Tooling & Setup

- Use **Vite + React + TypeScript** for modern React apps
- Prefer **Next.js** if:
  - You need SSR/SEO
  - You want server-side data fetching

- Avoid CRA (deprecated)

Modern ecosystem alternatives for side effects:

- **TanStack Query (React Query)** → API state + caching
- **SWR** → lightweight data fetching
- **Redux Toolkit Query (RTK Query)** → centralized async state

---

## Performance considerations

### Key rules

- Avoid unnecessary effects (bad dependency arrays = extra runs)
- Don’t use `useEffect` for derived state
- Prefer server-side or memoized computations when possible

### Optimization strategies

- Correct dependency arrays (use ESLint plugin: `react-hooks/exhaustive-deps`)
- Use `useMemo`/`useCallback` to stabilize dependencies
- Debounce/throttle expensive effects (e.g., search input)
- Cancel async requests using AbortController

```tsx id="fx_perf"
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/data", { signal: controller.signal })
    .then((res) => res.json())
    .then((data) => console.log(data));

  return () => controller.abort();
}, []);
```

- Use React DevTools Profiler to detect unnecessary re-runs

---

## Testing

Using **Vitest + React Testing Library**

```bash id="fx_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx id="fx_test_ex"
import { render, waitFor, screen } from "@testing-library/react";
import Users from "./Users";

test("renders users", async () => {
  render(<Users />);

  await waitFor(() =>
    expect(screen.getByText(/Leanne Graham/i)).toBeInTheDocument(),
  );
});
```

For side effects:

- Mock APIs using **MSW (Mock Service Worker)**
- Avoid real network calls in unit tests

---

## Ops & Deployment

- Handle async failures with error boundaries or local error state
- Add retry logic for API effects (or use React Query)
- Avoid memory leaks (always clean up subscriptions/timers)
- In SSR (Next.js):
  - Move data fetching to server components or `getServerSideProps`
  - Avoid client-only effects for initial critical data

- Monitor real-world issues with Sentry or OpenTelemetry
- Deploy via CDN (Vercel, Netlify, Cloudflare Pages)

---

## Pitfalls

- ❌ Missing dependency arrays → stale or incorrect data
- ❌ Using `useEffect` for derived state (unnecessary re-renders)
- ❌ Not cleaning up subscriptions → memory leaks
- ❌ Triggering infinite loops (setting state inside effect without conditions)

## Question 7. Explain higher-order components (HOC)

## Short answer

A **Higher-Order Component (HOC)** is a function in React that takes a component as input and returns a new enhanced component with additional props or behavior. It is a pattern used for **code reuse, logic abstraction, and cross-cutting concerns** like authentication, logging, or data fetching.

---

## Explanation

### What is an HOC?

An HOC is essentially a function:

```ts
const EnhancedComponent = withSomething(BaseComponent);
```

It follows this signature:

```ts
(Component) => Component;
```

More precisely:

```ts
function withFeature(WrappedComponent) {
  return function EnhancedComponent(props) {
    return <WrappedComponent {...props} extraProp="value" />;
  };
}
```

---

### Why HOCs exist

Before Hooks, HOCs were the primary way to:

- Share logic across components
- Inject props
- Wrap behavior (authentication, theming, permissions)

Even today, they are still used in:

- Legacy React codebases
- Libraries (React Redux `connect`, React Router patterns, etc.)

---

### How HOCs work internally

- A HOC creates a **new component wrapper**
- It composes behavior around the original component
- It does not modify the original component (pure composition)

React 18 still treats HOCs as normal components:

- They re-render when props/context/state change
- They participate in reconciliation like any other component

---

### Common use cases

- Authentication guards
- Logging / analytics
- Feature flags
- Injecting data (older Redux patterns)
- Theming wrappers
- Permission-based rendering

---

## Example (React + TypeScript)

### Setup (Vite)

```bash id="hoc_setup"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

### 1. Basic HOC: Authentication guard

```tsx id="hoc1"
import React from "react";

type WithAuthProps = {
  isAuthenticated: boolean;
};

function withAuth<P extends object>(WrappedComponent: React.ComponentType<P>) {
  return function AuthComponent(props: P & WithAuthProps) {
    const { isAuthenticated, ...rest } = props;

    if (!isAuthenticated) {
      return <h2>Please log in to continue</h2>;
    }

    return <WrappedComponent {...(rest as P)} />;
  };
}
```

---

### 2. Base component

```tsx id="hoc2"
type DashboardProps = {
  username: string;
};

function Dashboard({ username }: DashboardProps) {
  return <h1>Welcome, {username}</h1>;
}
```

---

### 3. Enhanced component

```tsx id="hoc3"
const ProtectedDashboard = withAuth(Dashboard);
```

---

### 4. Usage

```tsx id="hoc4"
export default function App() {
  return (
    <>
      <ProtectedDashboard isAuthenticated={true} username="John" />

      <ProtectedDashboard isAuthenticated={false} username="John" />
    </>
  );
}
```

---

## Tooling & Setup

- Preferred modern stack: **Vite + React + TypeScript**

- Avoid CRA (deprecated)

- HOCs are often replaced by Hooks in modern React:
  - `useAuth()` instead of `withAuth`
  - `useQuery()` instead of `withDataFetching`

- In modern frameworks:
  - **Next.js** prefers Server Components + hooks over HOCs
  - HOCs are still used in legacy integrations

---

## Performance considerations

### Potential issues

- Extra component layer → slightly deeper React tree
- Can break memoization if not handled properly
- Re-renders propagate through wrapper components

### Optimization strategies

- Use `React.memo` inside or after HOC wrapping
- Forward refs using `React.forwardRef`
- Avoid recreating HOCs inside render functions

```tsx id="hoc_perf"
const MemoizedComponent = React.memo(WrappedComponent);
```

- Combine with `useCallback`/`useMemo` where needed
- Use React DevTools Profiler to detect wrapper-induced re-renders

---

## Testing

Using **Vitest + React Testing Library**

```bash id="hoc_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx id="hoc_test_ex"
import { render, screen } from "@testing-library/react";
import { describe, test, expect } from "vitest";

test("shows auth message when not logged in", () => {
  const Protected = withAuth(() => <div>Secret</div>);

  render(<Protected isAuthenticated={false} />);

  expect(screen.getByText(/Please log in/i)).toBeInTheDocument();
});
```

---

## Ops & Deployment

- HOCs increase abstraction layers → harder debugging if overused
- Prefer hooks in new codebases for readability and composition
- Ensure consistent props typing with TypeScript generics
- Use Error Boundaries separately (don’t mix with HOCs unless needed)
- In SSR (Next.js), ensure HOCs are compatible with server rendering
- Avoid deep HOC nesting (can degrade maintainability and debugging)

---

## Pitfalls

- ❌ Overusing HOCs instead of hooks (leads to wrapper hell)
- ❌ Losing prop types if generics are not handled correctly
- ❌ Creating HOCs inside render (causes remounting)
- ❌ Ignoring `displayName` → harder debugging in React DevTools

## Question 8. What is React Portal? Give a use case

## Short answer

A **React Portal** allows you to render a component’s UI **outside the parent DOM hierarchy**, while still keeping it logically inside the React component tree. It is commonly used for UI elements like **modals, tooltips, dropdowns, and overlays**.

---

## Explanation

### What problem does Portal solve?

Normally, React renders components inside their parent DOM node:

```txt
<App>
  <div id="root">
    Parent → Child → Grandchild
  </div>
</App>
```

This causes issues for UI layers like:

- Modals being clipped by `overflow: hidden`
- Tooltips being affected by `z-index`
- Dropdowns breaking layout stacking contexts

---

### What is a Portal?

A Portal lets you render a component into a **different DOM node**, typically outside `#root`.

Example structure:

```txt
<body>
  <div id="root"></div>
  <div id="portal-root"></div>
</body>
```

React renders UI into `portal-root` instead of `root`.

---

### How it works internally

React Portal:

- Keeps component in the **same React tree**
- But renders DOM output in a **different DOM container**
- Event bubbling still works through React tree (not DOM hierarchy)

So:

> Logical structure ≠ DOM structure

React 18 treats portals like normal components during reconciliation, but DOM placement differs.

---

### Syntax

```tsx
ReactDOM.createPortal(child, container);
```

- `child` → React element
- `container` → DOM node outside root

---

## Example (React + TypeScript)

### Setup (Vite)

```bash id="portal_setup"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

### 1. Add portal root in HTML

```html id="portal_html"
<!-- index.html -->
<body>
  <div id="root"></div>
  <div id="modal-root"></div>
</body>
```

---

### 2. Modal using Portal

```tsx id="portal1"
import React from "react";
import ReactDOM from "react-dom";

type ModalProps = {
  open: boolean;
  onClose: () => void;
  children: React.ReactNode;
};

export default function Modal({ open, onClose, children }: ModalProps) {
  if (!open) return null;

  const modalRoot = document.getElementById("modal-root");

  if (!modalRoot) return null;

  return ReactDOM.createPortal(
    <div
      style={{
        position: "fixed",
        top: 0,
        left: 0,
        width: "100%",
        height: "100%",
        background: "rgba(0,0,0,0.5)",
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
      }}
      onClick={onClose}
    >
      <div
        style={{ background: "white", padding: 20 }}
        onClick={(e) => e.stopPropagation()}
      >
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>,
    modalRoot,
  );
}
```

---

### 3. Usage

```tsx id="portal2"
import { useState } from "react";
import Modal from "./Modal";

export default function App() {
  const [open, setOpen] = useState(false);

  return (
    <div>
      <h1>Portal Example</h1>

      <button onClick={() => setOpen(true)}>Open Modal</button>

      <Modal open={open} onClose={() => setOpen(false)}>
        <h2>Hello from Portal</h2>
      </Modal>
    </div>
  );
}
```

---

## Tooling & Setup

- Use **Vite + React + TypeScript**
- Ensure `index.html` includes a separate portal root
- In Next.js:
  - Portals must be client-only (`use client`)
  - Avoid SSR rendering into portal containers directly

Modern alternatives:

- Radix UI (uses portals internally)
- Headless UI
- React Aria

---

## Performance

- Portals do **not reduce rendering cost**
- They only change DOM placement
- Useful for avoiding layout recalculations caused by parent containers

### Optimization tips

- Avoid re-creating portal containers dynamically
- Use `React.memo` for modal content
- Lazy load modals using `React.lazy`:

```tsx
const Modal = React.lazy(() => import("./Modal"));
```

- Keep portal UI minimal to reduce re-renders
- Use React DevTools Profiler to check overlay re-renders

---

## Testing

Using **Vitest + React Testing Library**

```bash id="portal_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx id="portal_test_ex"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("opens modal", () => {
  render(<App />);

  screen.getByText("Open Modal").click();

  expect(screen.getByText("Hello from Portal")).toBeInTheDocument();
});
```

If needed, mock `document.body` or portal root in tests.

---

## Ops & Deployment

- Ensure portal root exists in production HTML
- Avoid multiple conflicting portal roots
- Be careful with z-index stacking contexts in CSS
- Handle accessibility:
  - Focus trapping inside modals
  - Escape key handling

- Consider SSR hydration behavior in Next.js
- Use CDNs for static assets; portals do not affect deployment strategy

---

## Pitfalls

- ❌ Forgetting to add portal container in HTML
- ❌ Breaking accessibility (focus not trapped in modal)
- ❌ Event handling confusion (DOM hierarchy vs React tree)
- ❌ Overusing portals unnecessarily (adds complexity)

## Question 9. How do you handle errors in React? Explain error boundaries

## Short answer

In React, errors are handled using **Error Boundaries**, which are special components that catch JavaScript errors in their child component tree during rendering, lifecycle methods, and constructors, and display a fallback UI instead of crashing the entire app.

---

## Explanation

### What problem do Error Boundaries solve?

Without error boundaries:

- A single runtime error in a component → **entire React app crashes**
- UI becomes blank or broken

With error boundaries:

- Only the **faulty part of the UI is replaced**
- Rest of the application continues working

---

### What is an Error Boundary?

An Error Boundary is a class component that implements:

- `static getDerivedStateFromError()`
- `componentDidCatch()`

React 18 still requires **class components** for error boundaries (no functional equivalent yet for full boundary behavior).

---

### How React handles errors internally

- Errors during render phase are propagated up the component tree
- React looks for the nearest error boundary
- That boundary catches the error and renders fallback UI
- Error boundaries **do NOT catch**:
  - Event handlers
  - Async code (setTimeout, promises)
  - Server-side rendering errors (handled separately)

---

## Error Boundary lifecycle flow

```txt id="eb1"
Render → Error thrown → Boundary catches → Fallback UI rendered
```

---

## Example (React + TypeScript)

### Setup (Vite recommended)

```bash id="eb_setup"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

### 1. Error Boundary class component

```tsx id="eb2"
import React, { ReactNode } from "react";

type Props = {
  children: ReactNode;
};

type State = {
  hasError: boolean;
};

export class ErrorBoundary extends React.Component<Props, State> {
  constructor(props: Props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(): State {
    return { hasError: true };
  }

  componentDidCatch(error: Error, info: React.ErrorInfo) {
    console.error("Error caught by boundary:", error, info);
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong.</h2>;
    }

    return this.props.children;
  }
}
```

---

### 2. Faulty component

```tsx id="eb3"
export function BuggyComponent() {
  throw new Error("Crash inside component!");

  return <div>This will never render</div>;
}
```

---

### 3. Usage

```tsx id="eb4"
import { ErrorBoundary } from "./ErrorBoundary";
import { BuggyComponent } from "./BuggyComponent";

export default function App() {
  return (
    <ErrorBoundary>
      <h1>React App</h1>
      <BuggyComponent />
    </ErrorBoundary>
  );
}
```

---

### Result

- `BuggyComponent` crashes
- Error Boundary catches it
- App still renders fallback UI instead of breaking completely

---

## Tooling & Setup

- Use **Vite + React + TypeScript** for modern apps
- Avoid CRA (deprecated)
- In Next.js:
  - Use `error.tsx` (App Router) for route-level boundaries
  - Server Components have their own error handling model

Modern alternatives:

- `react-error-boundary` library (functional wrapper around class boundaries)
- Sentry for production error tracking

---

## Performance considerations

- Error boundaries are **cheap** and do not affect render performance unless triggered
- Place boundaries strategically:
  - At page level (route boundaries)
  - At widget/component level (isolate failures)

### Best practices

- Don’t wrap entire app in a single boundary (too coarse)
- Use multiple boundaries for isolation
- Combine with lazy loading:

```tsx id="eb_perf"
const LazyComponent = React.lazy(() => import("./Component"));
```

- Use React DevTools to trace error source components

---

## Testing

Using **Vitest + React Testing Library**

```bash id="eb_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx id="eb_test_ex"
import { render, screen } from "@testing-library/react";
import { ErrorBoundary } from "./ErrorBoundary";

function Broken() {
  throw new Error("Test error");
}

test("renders fallback UI on error", () => {
  render(
    <ErrorBoundary>
      <Broken />
    </ErrorBoundary>,
  );

  expect(screen.getByText(/Something went wrong/i)).toBeInTheDocument();
});
```

---

## Ops & Deployment

- Use production error tracking:
  - Sentry
  - LogRocket

- Log errors in `componentDidCatch`
- Pair with fallback UI for better UX
- Avoid exposing sensitive error messages to users
- In SSR (Next.js):
  - Use `error.tsx` route boundaries
  - Handle server/client errors separately

- Monitor error rates in production dashboards

---

## Pitfalls

- ❌ Error boundaries do NOT catch async errors (fetch, promises)
- ❌ Cannot be used in functional components alone (requires class or library wrapper)
- ❌ Overusing a single boundary reduces granularity
- ❌ Forgetting fallback UI leads to poor UX

## Question 10. Difference between controlled and uncontrolled forms

## Short answer

A **controlled form** is where form data is managed by React state, while an **uncontrolled form** is where form data is handled by the DOM itself using refs. Controlled forms give React full control over input values; uncontrolled forms rely on the browser DOM for state management.

---

## Explanation

### Controlled Components

In a **controlled form**, every input value is tied to React state:

- Source of truth → React state
- Updates happen via `onChange`
- React re-renders on every input change

#### How it works (React 18 behavior)

- User types → event fires
- `setState` updates value
- React re-renders component
- Input value is synced from state

This creates a **single source of truth**.

---

### Uncontrolled Components

In an **uncontrolled form**, the DOM manages the state:

- Source of truth → DOM
- You access values using `ref`
- React does NOT re-render on each keystroke

You typically read values only on submit.

---

### Key differences

| Feature         | Controlled      | Uncontrolled              |
| --------------- | --------------- | ------------------------- |
| Source of truth | React state     | DOM                       |
| Re-rendering    | On every change | Minimal                   |
| Validation      | Real-time       | On submit or manual       |
| Complexity      | More verbose    | Simpler                   |
| Performance     | More re-renders | Fewer re-renders          |
| Use case        | Dynamic forms   | Simple forms, file inputs |

---

## When to use what

### Use Controlled Forms when:

- You need real-time validation
- You depend on input values dynamically (e.g., search, filters)
- You want predictable state flow
- You are building complex forms (multi-step, conditional logic)

### Use Uncontrolled Forms when:

- Form is simple (login, contact form)
- You want better performance for large forms
- Working with file inputs (often easier uncontrolled)
- Integrating with non-React libraries

---

## Example (React + TypeScript)

### Setup (Vite)

```bash id="form_setup"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## 1. Controlled Form Example

```tsx id="controlled_form"
import { useState } from "react";

export default function ControlledForm() {
  const [email, setEmail] = useState("");

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log("Submitted:", email);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />

      <button type="submit">Submit</button>
    </form>
  );
}
```

### Flow:

- Input changes → state updates → re-render → UI syncs

---

## 2. Uncontrolled Form Example

```tsx id="uncontrolled_form"
import { useRef } from "react";

export default function UncontrolledForm() {
  const emailRef = useRef<HTMLInputElement>(null);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log("Submitted:", emailRef.current?.value);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" ref={emailRef} placeholder="Enter email" />

      <button type="submit">Submit</button>
    </form>
  );
}
```

### Flow:

- Input changes → DOM updates only
- React reads value only on submit

---

## Tooling & Setup

- Use **Vite + React + TypeScript** for modern form handling
- Avoid CRA (deprecated)
- For large-scale forms:
  - React Hook Form (uncontrolled + optimized)
  - Formik (more controlled-based, heavier)

---

## Performance considerations

### Controlled forms

- Cause re-render on every keystroke
- Can be expensive for large forms
- Optimize using:
  - `useMemo` for derived validation
  - `React.memo` for input components
  - Debouncing input handlers

### Uncontrolled forms

- Minimal re-renders → better performance
- Less React overhead
- Harder to implement dynamic validation

---

## Best practice in modern React

A hybrid approach is often ideal:

- Controlled for critical fields (validation, UI logic)
- Uncontrolled for simple or large inputs

Or better:

- Use **React Hook Form**
  - Uses uncontrolled inputs internally
  - Optimized for performance
  - Minimal re-renders

---

## Testing

Using **Vitest + React Testing Library**

```bash id="form_test"
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx id="form_test_ex"
import { render, screen, fireEvent } from "@testing-library/react";
import ControlledForm from "./ControlledForm";

test("updates input value", () => {
  render(<ControlledForm />);

  const input = screen.getByPlaceholderText("Enter email");

  fireEvent.change(input, { target: { value: "test@mail.com" } });

  expect((input as HTMLInputElement).value).toBe("test@mail.com");
});
```

---

## Ops & Deployment

- Validate on both client and server (never trust client-only validation)
- Sanitize inputs to prevent XSS
- Use schema validation (Zod, Yup)
- Handle form submission errors gracefully
- Consider server actions (Next.js App Router) for secure submissions
- Avoid storing sensitive form state unnecessarily in global state

---

## Pitfalls

- ❌ Using controlled inputs without need → performance overhead
- ❌ Mixing controlled + uncontrolled in same input
- ❌ Forgetting `value` prop in controlled inputs (causes React warnings)
- ❌ Direct DOM manipulation in controlled forms

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
