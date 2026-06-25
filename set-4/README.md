# Set 4

| #   | Question                                                                                                                                             |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is prop-types and why is it used?](#question-1-what-is-prop-types-and-why-is-it-used)                                                          |
| 2   | [How do you test React components?](#question-2-how-do-you-test-react-components)                                                                    |
| 3   | [Difference between shallow rendering and full rendering in testing](#question-3-difference-between-shallow-rendering-and-full-rendering-in-testing) |
| 4   | [How do you handle async operations in React?](#question-4-how-do-you-handle-async-operations-in-react)                                              |
| 5   | [Explain debouncing and throttling in React components](#question-5-explain-debouncing-and-throttling-in-react-components)                           |
| 6   | [How do you use React.forwardRef?](#question-6-how-do-you-use-reactforwardref)                                                                       |
| 7   | [Explain controlled input with dynamic validation](#question-7-explain-controlled-input-with-dynamic-validation)                                     |
| 8   | [How do you fetch API data in React?](#question-8-how-do-you-fetch-api-data-in-react)                                                                |
| 9   | [Difference between fetch and axios in React](#question-9-difference-between-fetch-and-axios-in-react)                                               |
| 10  | [How do you use setState asynchronously in class components?](#question-10-how-do-you-use-setstate-asynchronously-in-class-components)               |
| 11  | [Explain React Fiber architecture](#question-11-explain-react-fiber-architecture)                                                                    |
| 12  | [How does React handle reconciliation?](#question-12-how-does-react-handle-reconciliation)                                                           |
| 13  | [Explain the difference between reconciliation and diffing](#question-13-explain-the-difference-between-reconciliation-and-diffing)                  |
| 14  | [How do you implement server-side rendering (SSR) in React?](#question-14-how-do-you-implement-server-side-rendering-ssr-in-react)                   |
| 15  | [What is Next.js and how is it related to React?](#question-15-what-is-nextjs-and-how-is-it-related-to-react)                                        |
| 16  | [How do you implement static site generation (SSG)?](#question-16-how-do-you-implement-static-site-generation-ssg)                                   |
| 17  | [Explain hydration in SSR](#question-17-explain-hydration-in-ssr)                                                                                    |
| 18  | [How do you handle global state in large React applications?](#question-18-how-do-you-handle-global-state-in-large-react-applications)               |
| 19  | [Compare Redux, Zustand, Jotai, and Recoil](#question-19-compare-redux-zustand-jotai-and-recoil)                                                     |
| 20  | [Explain middleware in Redux](#question-20-explain-middleware-in-redux)                                                                              |

## Question 1. What is prop-types and why is it used?

## Short answer

`prop-types` is a runtime type-checking library for React props that helps validate the data passed into components and warn developers during development if props don’t match expected types.

---

## Explanation

### What is `prop-types`?

`prop-types` is a separate package used in React (especially in JavaScript codebases) to define and validate the shape and type of props a component receives at runtime.

It was widely used before TypeScript became mainstream in React ecosystems. Even today, it is still useful in non-TypeScript projects or shared component libraries.

---

### Why is it used?

#### 1. Runtime validation (development safety net)

React itself does not enforce prop types at runtime. `prop-types` helps catch issues like:

- Missing required props
- Wrong data types (string vs number)
- Incorrect object shapes

This is especially useful in large apps where components are reused across teams.

---

#### 2. Better maintainability in JS projects

In JavaScript-only React apps, it acts as lightweight “type documentation” inside the component itself.

---

#### 3. Helps during debugging

Warnings appear in the browser console during development, making issues easier to trace early.

---

### React 18 context & modern perspective

- React 18 does **not include runtime prop validation**
- With **TypeScript**, most teams skip `prop-types`
- Still relevant for:
  - Legacy JS codebases
  - Third-party library authors
  - Gradual migration to TypeScript

- Works in both concurrent rendering and legacy rendering modes because it runs at runtime (not compile time)

---

### State management & architecture perspective

- `prop-types` is **not state management**
- It only validates **input contracts between components**
- Useful in component-driven architecture (design systems, micro-frontends)
- Does not replace:
  - TypeScript (compile-time safety)
  - Redux/Zustand/Context (state management)

---

## Example (React + JavaScript)

### Setup (Vite)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm install prop-types
npm run dev
```

---

### Component using prop-types

```jsx
import PropTypes from "prop-types";

function UserCard({ name, age, isAdmin }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>{isAdmin ? "Admin" : "User"}</p>
    </div>
  );
}

UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  isAdmin: PropTypes.bool,
};

export default UserCard;
```

---

## Performance

- `prop-types` runs **only in development mode** in most setups (stripped in production builds)
- No runtime performance cost in production bundles (when configured correctly)
- Still, avoid overusing complex validators in deeply nested structures
- In large apps, prefer TypeScript to eliminate runtime overhead entirely

**Optimization tips:**

- Prefer TypeScript for static validation
- Avoid heavy custom validators in `prop-types`
- Combine with `React.memo` for preventing unnecessary renders
- Use React DevTools Profiler to detect prop-driven re-renders

---

## Testing

With **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react jsdom
```

Example test:

```jsx
import { render } from "@testing-library/react";
import UserCard from "./UserCard";

test("renders user card", () => {
  render(<UserCard name="John" age={25} isAdmin={false} />);
});
```

Note:

- `prop-types` warnings are not typically asserted in tests
- Focus testing on behavior, not type validation
- Type correctness should ideally be enforced by TypeScript

---

## Ops & Deployment

- `prop-types` is removed in production builds (minification + dead code elimination)
- No impact on bundle size in production
- Works seamlessly with:
  - Vite (ESM-based bundler)
  - Webpack
  - Next.js SSR/CSR

**Best practice:**

- Use `prop-types` only as a dev-time guard
- For SSR (Next.js), prefer TypeScript for consistency across server/client boundaries

---

## Pitfalls

- Over-reliance instead of TypeScript (does not scale well)
- Forgetting to mark critical props as `.isRequired`
- Assuming it prevents runtime crashes (it only warns)
- Using complex nested validators that reduce readability

## Question 2. How do you test React components?

# Short answer

React components are typically tested at three levels:

- **Unit tests** – Test an individual component in isolation.
- **Integration tests** – Test how multiple components work together.
- **End-to-end (E2E) tests** – Test complete user flows in a real browser.

Modern React applications commonly use:

- **Vitest** (or Jest) as the test runner
- **React Testing Library (RTL)** for component testing
- **Playwright** or **Cypress** for E2E testing

The recommended approach is to test **behavior from the user's perspective** rather than implementation details.

---

# Explanation

## React testing philosophy

The React team recommends writing tests that resemble how users interact with your application.

Instead of testing:

- Internal state
- Hook implementation
- Private methods
- Component instance

Test:

- What is rendered
- User interactions
- Accessibility
- API responses
- Navigation
- Error states

For example:

❌ Bad test

```tsx
expect(component.state.count).toBe(1);
```

✅ Good test

```tsx
await user.click(button);

expect(screen.getByText("Count: 1")).toBeInTheDocument();
```

---

## Types of React tests

### 1. Unit Testing

Tests a single component independently.

Example:

- Button renders
- Input accepts text
- Modal opens
- Badge displays correct status

Fast and easy to maintain.

---

### 2. Integration Testing

Tests several components working together.

Example:

```
Login Form
      ↓
Submit Button
      ↓
API Call
      ↓
Dashboard
```

This is the most valuable type of testing in React applications.

---

### 3. End-to-End Testing

Runs the application inside a real browser.

Example:

```
Open website
↓

Login
↓

Search product
↓

Add to cart
↓

Checkout
```

Tools:

- Playwright
- Cypress

---

## React 18 considerations

React 18 introduced:

- Concurrent rendering
- Automatic batching
- Suspense improvements

React Testing Library automatically wraps updates in `act()` where appropriate, but asynchronous rendering means you should prefer async queries like `findBy...` or `waitFor` when waiting for UI updates.

Example:

```tsx
await screen.findByText("Users Loaded");
```

instead of

```tsx
screen.getByText("Users Loaded");
```

---

## Component architecture

Well-tested React applications usually separate:

```
UI Components
      ↓
Hooks
      ↓
Services/API
```

Example:

```
UserCard
      ↓
useUsers()
      ↓
fetchUsers()
```

Test independently:

- UI rendering
- Hook behavior
- API mocking

This makes tests easier to write and maintain.

---

# Example (React + TypeScript)

## Create project (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

Install testing tools:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event @testing-library/jest-dom jsdom
```

**Counter.tsx**

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <p>Count: {count}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </>
  );
}
```

**Counter.test.tsx**

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { describe, expect, it } from "vitest";
import Counter from "./Counter";

describe("Counter", () => {
  it("increments count", async () => {
    render(<Counter />);

    const user = userEvent.setup();

    await user.click(screen.getByRole("button", { name: /increment/i }));

    expect(screen.getByText("Count: 1")).toBeInTheDocument();
  });
});
```

Run tests:

```bash
npx vitest
```

---

# Tooling & Setup

**Preferred stack**

- **Vite** for fast development and native ESM support.
- **Vitest** integrates naturally with Vite and offers excellent performance.
- **React Testing Library** for behavior-focused component testing.
- **Playwright** for reliable cross-browser E2E testing.

**Avoid**

- **Create React App (CRA)**, as it is deprecated.

**ESM vs CommonJS**

- Vite and Vitest use **ES Modules (ESM)** by default, enabling faster startup and better tree-shaking.
- CommonJS is primarily used in older Node.js ecosystems and is less common in modern React projects.

---

# Performance

Testing itself doesn't optimize runtime performance, but it helps prevent regressions.

Performance practices:

- Profile with **React DevTools Profiler**
- Test memoized components (`React.memo`)
- Verify expensive calculations using `useMemo`
- Ensure callbacks passed to memoized children use `useCallback` when beneficial
- Test lazy-loaded components

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

Mock slow APIs to keep tests deterministic and fast.

---

# Testing

Recommended testing pyramid:

```
        E2E
         ▲
 Integration
         ▲
     Unit Tests
```

Common commands:

```bash
npm run test
```

```bash
npx vitest
```

Playwright:

```bash
npx playwright test
```

Best practices:

- Prefer `screen.getByRole()` over CSS selectors.
- Use `userEvent` instead of `fireEvent` to better simulate real user interactions.
- Mock network requests with tools like Mock Service Worker (MSW) for integration tests.
- Test accessibility by querying elements the way assistive technologies do.

---

# Ops & Deployment

For production-ready applications:

- Run unit and integration tests in CI/CD pipelines.
- Execute Playwright E2E tests before deployment.
- Use **Error Boundaries** to gracefully handle rendering errors.
- Validate both **SSR** and **CSR** behavior if using frameworks like Next.js.
- Keep bundles small with code splitting and lazy loading.
- Deploy static assets through a CDN and monitor errors using services like Sentry.

---

# Pitfalls

- Testing implementation details (state, hooks, private methods) instead of user-visible behavior.
- Overusing snapshots, which can become brittle and difficult to maintain.
- Forgetting to `await` asynchronous updates, leading to flaky tests.

## Question 3. Difference between shallow rendering and full rendering in testing

# Short answer

**Shallow rendering** renders only the component being tested and does **not** render its child components. It was mainly associated with **Enzyme** and is now largely considered a legacy approach.

**Full rendering** renders the entire component tree, including child components, and is the recommended approach today using **React Testing Library (RTL)** because it better reflects how users interact with your application.

---

# Explanation

## Shallow Rendering

Shallow rendering renders only one level of the component tree.

Example:

```text
<App>
   ├── Header
   ├── Sidebar
   └── Dashboard
```

With shallow rendering:

```text
<App>
```

`Header`, `Sidebar`, and `Dashboard` are **not actually rendered**.

### Advantages

- Fast execution
- Easy to isolate a component
- Less setup for unit tests

### Disadvantages

- Doesn't test component integration
- Can miss real-world rendering issues
- Encourages testing implementation details
- Not recommended for modern React applications

---

## Full Rendering

Full rendering mounts the complete component tree.

Example:

```text
<App>
   ├── Header
   ├── Sidebar
   └── Dashboard
        ├── Card
        └── Chart
```

Everything is rendered, just like it would be in the browser.

### Advantages

- Tests actual user behavior
- Detects integration issues
- Works well with React Context, React Router, and Redux
- Aligns with React Testing Library's philosophy

### Disadvantages

- Slightly slower than shallow rendering
- May require mocking APIs or providers
- More setup for complex applications

---

## Comparison

| Feature                  | Shallow Rendering   | Full Rendering               |
| ------------------------ | ------------------- | ---------------------------- |
| Child components         | ❌ Not rendered     | ✅ Fully rendered            |
| DOM rendering            | Partial             | Complete                     |
| User interaction testing | Limited             | Excellent                    |
| React Context support    | Poor                | Excellent                    |
| Redux support            | Limited             | Excellent                    |
| React Router support     | Limited             | Excellent                    |
| Best for                 | Legacy Enzyme tests | Modern React Testing Library |
| Recommended today        | ❌ No               | ✅ Yes                       |

---

## React 18 considerations

With React 18's features like:

- Concurrent rendering
- Automatic batching
- Suspense
- Transitions

Shallow rendering becomes less representative because it doesn't exercise how components interact within the full tree.

Modern testing emphasizes **observable behavior** rather than component internals. Full rendering with React Testing Library is better suited to React 18 and later.

---

## Component architecture

Consider:

```text
App
 ├── Navbar
 ├── ProductList
 │      ├── ProductCard
 │      └── ProductCard
 └── Footer
```

### Shallow rendering

Tests only:

```text
ProductList
```

### Full rendering

Tests:

```text
ProductList
   ↓
ProductCard
   ↓
Button
   ↓
Image
```

This verifies that child components work together correctly.

---

# Example (React + TypeScript)

## Create project (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
npm run dev
```

### Parent component

```tsx
function Welcome() {
  return <h2>Welcome!</h2>;
}

export default function App() {
  return (
    <>
      <h1>Home</h1>
      <Welcome />
    </>
  );
}
```

### Full rendering test

```tsx
import { render, screen } from "@testing-library/react";
import { describe, it, expect } from "vitest";
import App from "./App";

describe("App", () => {
  it("renders parent and child components", () => {
    render(<App />);

    expect(screen.getByText("Home")).toBeInTheDocument();
    expect(screen.getByText("Welcome!")).toBeInTheDocument();
  });
});
```

Unlike shallow rendering, the child component (`Welcome`) is rendered and verified as part of the test.

---

# Tooling & Setup

**Preferred stack**

- **Vite** for development and native ESM support.
- **Vitest** as the test runner.
- **React Testing Library** for component testing.
- **Playwright** for end-to-end tests.

Avoid **Create React App (CRA)** because it is deprecated.

**ESM vs CommonJS**

- Vite and Vitest are ESM-first, offering faster startup and better tree-shaking.
- CommonJS remains primarily for older Node.js projects.

---

# Performance

Testing strategies don't directly affect application performance, but they influence test quality and maintainability.

When testing performance-sensitive components:

- Use the **React DevTools Profiler** to identify unnecessary renders.
- Verify memoized components (`React.memo`) still behave correctly.
- Test expensive computations using `useMemo`.
- Test callback stability when using `useCallback`.
- Verify lazy-loaded components with `React.lazy` and `Suspense`.

---

# Testing

Recommended approach:

- **Unit tests:** React Testing Library + Vitest
- **Integration tests:** React Testing Library with providers (Context, Router, Redux)
- **E2E tests:** Playwright

Avoid assertions based on:

- Component instances
- Internal state
- Private methods

Instead, test what users can see and do.

---

# Ops & Deployment

- Run unit and integration tests in CI/CD pipelines.
- Execute Playwright E2E tests before releases.
- Use Error Boundaries to handle rendering failures gracefully.
- Test both SSR and CSR behavior when using frameworks like Next.js.
- Use code splitting and lazy loading to optimize bundle size.
- Deploy assets through a CDN and monitor production issues with tools like Sentry.

---

# Pitfalls

- Using shallow rendering to test implementation details instead of observable behavior.
- Assuming a shallow test guarantees child components work correctly.
- Writing brittle tests that depend on component structure rather than user-visible output.

## Question 4. How do you handle async operations in React?

# Short answer

Async operations in React are handled using **`useEffect` for side effects**, combined with **Promises / async-await**, and often managed more robustly using **data-fetching libraries like React Query or SWR**. You must also handle **loading, success, error states, and cleanup (to avoid memory leaks or race conditions)**.

---

# Explanation

## 1. Core pattern: `useEffect` + async/await

React components should remain **pure**, so async work is performed in effects, not during render.

Typical flow:

- Component renders
- `useEffect` triggers async operation
- State updates when data arrives
- Re-render occurs

### React 18 considerations

- Effects run after render
- Strict Mode may run effects twice in development (to detect side effects)
- Concurrent rendering can interrupt or restart renders, so async logic must be resilient

---

## 2. Key concerns in async handling

### a. Loading state

Track when request is in progress.

### b. Error handling

Catch failures and update UI accordingly.

### c. Race conditions

Multiple fast requests may resolve out of order.

### d. Cleanup / cancellation

Avoid setting state after component unmount.

---

## 3. State management trade-offs

### Basic approach (local state)

- Good for simple components
- Scales poorly for complex data flows

### Context / Redux

- Useful when multiple components need shared async data
- Redux middleware (Thunk / Saga) helps centralize async logic

### React Query / SWR (recommended modern approach)

- Handles caching, retries, deduping, background refetch
- Removes most manual loading/error boilerplate
- Ideal for server-state management

---

## 4. Performance considerations

- Avoid triggering unnecessary re-fetches in `useEffect`
- Use memoized dependencies carefully
- Debounce API calls (e.g., search inputs)
- Use abort controllers to cancel stale requests
- Prefer server-state libraries for caching and deduplication

---

# Example (React + TypeScript + Vite)

## Setup

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Example: Async fetch with cleanup

```tsx id="async1"
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function Users() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchUsers() {
      try {
        setLoading(true);

        const res = await fetch("https://jsonplaceholder.typicode.com/users", {
          signal: controller.signal,
        });

        if (!res.ok) throw new Error("Failed to fetch users");

        const data: User[] = await res.json();
        setUsers(data);
      } catch (err: any) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchUsers();

    return () => {
      controller.abort(); // prevents memory leaks / stale updates
    };
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

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

# Tooling & Setup

## Recommended modern stack

### Vite (preferred)

- Fast dev server
- Native ESM
- Minimal config

### React Query (TanStack Query)

Best-in-class async data handling:

```bash id="rq1"
npm install @tanstack/react-query
```

Wrap app:

```tsx id="rq2"
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}
```

---

## Why avoid CRA

- Deprecated
- Slower builds
- Less flexible than Vite / Next.js / Remix

---

# Performance

### Key optimizations

- **AbortController** → prevents stale updates
- **React Query caching** → avoids redundant requests
- **Debouncing input-driven requests**
- **Memoization (`useMemo`, `useCallback`)** to prevent re-fetch triggers
- **Code splitting** for async-heavy routes

### React 18 concurrency impact

- Requests may be started/abandoned during render interruptions
- Effects may re-run in Strict Mode (dev only)
- Always assume async calls can be duplicated → make them idempotent or cancellable

### Profiling

- React DevTools Profiler → detect unnecessary re-renders
- Network tab → check duplicate API calls
- React Query Devtools → inspect caching behavior

---

# Testing

## Unit / Integration (Vitest + RTL)

Mock async requests:

```tsx id="test1"
import { render, screen, waitFor } from "@testing-library/react";
import Users from "./Users";

global.fetch = vi.fn(() =>
  Promise.resolve({
    ok: true,
    json: () => Promise.resolve([{ id: 1, name: "John" }]),
  }),
) as any;

test("renders users", async () => {
  render(<Users />);

  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  await waitFor(() => {
    expect(screen.getByText("John")).toBeInTheDocument();
  });
});
```

---

## E2E (Playwright)

```bash id="pw1"
npx playwright test
```

Example:

- intercept API
- validate UI state transitions

---

# Ops & Deployment

### Production concerns

- Handle API failures gracefully (fallback UI)
- Use retries with exponential backoff (React Query helps)
- Avoid memory leaks from long-running requests
- Use **Error Boundaries** for rendering failures
- Monitor API latency with observability tools (Sentry, Datadog)
- Cache server-state at CDN or edge when possible

### SSR vs CSR

- SSR (Next.js): fetch data on server → faster first paint
- CSR (React SPA): fetch after mount → simpler but slower initial load
- Hybrid (recommended): Next.js App Router with server components

---

# Pitfalls

- Updating state after unmount (memory leaks)
- Ignoring race conditions in fast-changing inputs
- Triggering infinite loops in `useEffect`
- Overusing `useEffect` instead of using React Query/SWR
- Not handling error states in UI

## Question 5. Explain debouncing and throttling in React components

# Short answer

**Debouncing** delays executing a function until a pause in events (e.g., user stops typing), while **throttling** ensures a function executes at most once in a fixed time interval (e.g., every 300ms during scrolling).

In React, both are used to **optimize frequent event handling** like input changes, resize, scroll, and API calls.

---

# Explanation

## 1. Debouncing in React

### Concept

Debouncing waits until the user stops triggering an event before running logic.

Example:

- User types in search box
- API call is made only after user pauses typing (e.g., 500ms)

### Why it matters in React

Without debouncing:

- Every keystroke triggers re-render + API call
- Causes performance issues and unnecessary network requests

### React 18 context

- Each keystroke triggers a render
- Debounce prevents excessive state updates / side effects
- Works well with `useEffect` or controlled inputs

---

## 2. Throttling in React

### Concept

Throttling ensures a function runs at most once per interval.

Example:

- Scroll event fires continuously
- Function executes every 200ms instead of every pixel scroll

### Why it matters in React

Without throttling:

- Scroll/resize handlers cause performance degradation
- Excessive re-renders in concurrent rendering mode

---

## 3. Key difference

| Feature          | Debouncing                     | Throttling                  |
| ---------------- | ------------------------------ | --------------------------- |
| Execution timing | After event stops              | At regular intervals        |
| Best for         | Search inputs, form validation | Scroll, resize, drag events |
| API calls        | Yes (preferred)                | Rare                        |
| Frequency        | Once after delay               | Repeated but limited        |

---

## 4. State management perspective

- Debounce reduces **state updates frequency**
- Throttle limits **render/update rate**
- Both help prevent:
  - unnecessary renders
  - excessive API calls
  - UI jank in React concurrent rendering

---

## 5. Performance impact in React 18

React 18 introduces:

- concurrent rendering
- automatic batching
- interruptible renders

Without debounce/throttle:

- many renders may be queued or interrupted
- expensive calculations run repeatedly

With them:

- fewer renders
- smoother UI transitions
- reduced CPU usage

---

# Example (React + TypeScript)

## Setup (Vite)

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## 1. Debounce example (search input)

```tsx id="debounce1"
import { useEffect, useState } from "react";

function useDebounce(value: string, delay: number) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

export default function Search() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 500);

  useEffect(() => {
    if (debouncedQuery) {
      console.log("API call:", debouncedQuery);
      // fetch(`/api/search?q=${debouncedQuery}`)
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      placeholder="Search..."
    />
  );
}
```

---

## 2. Throttle example (scroll event)

```tsx id="throttle1"
import { useEffect, useState } from "react";

function throttle(fn: Function, delay: number) {
  let lastCall = 0;

  return (...args: any[]) => {
    const now = Date.now();

    if (now - lastCall >= delay) {
      lastCall = now;
      fn(...args);
    }
  };
}

export default function ScrollTracker() {
  const [scrollY, setScrollY] = useState(0);

  useEffect(() => {
    const handleScroll = throttle(() => {
      setScrollY(window.scrollY);
    }, 200);

    window.addEventListener("scroll", handleScroll);

    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  }, []);

  return <div>Scroll position: {scrollY}px</div>;
}
```

---

# Tooling & Setup

## Recommended approach

Instead of writing custom implementations everywhere:

### Use utility libraries

```bash id="lib1"
npm install lodash
```

- `lodash.debounce`
- `lodash.throttle`

Or modern alternatives:

- `use-debounce`
- `ahooks`

### Why Vite?

- Fast refresh
- ESM-based modules
- Better DX than CRA (deprecated)

---

## ESM vs CommonJS note

- Vite uses **ESM**
- Lodash imports should be modular:

  ```ts
  import debounce from "lodash/debounce";
  ```

This avoids full bundle imports.

---

# Performance

## Why debouncing/throttling matters

### Without optimization:

- input → 10 keystrokes → 10 renders + 10 API calls
- scroll → 100 events/sec → 100 state updates

### With optimization:

- debounce → 1 API call after typing stops
- throttle → 5–10 updates/sec max

---

## React optimization techniques

- Combine with `useMemo` for expensive computations
- Use `useCallback` for stable handlers
- Use `React.memo` for preventing child re-renders
- Avoid inline function creation in JSX
- Use `requestAnimationFrame` for UI-heavy scroll animations

---

## Profiling

Use:

- React DevTools Profiler
- Chrome Performance tab

Look for:

- excessive re-renders
- repeated API calls
- long tasks (>50ms)

---

# Testing

## Debounce testing (Vitest + RTL)

```tsx id="test1"
import { render, screen, fireEvent, waitFor } from "@testing-library/react";
import Search from "./Search";

test("debounces input updates", async () => {
  render(<Search />);

  const input = screen.getByPlaceholderText("Search...");

  fireEvent.change(input, { target: { value: "react" } });
  fireEvent.change(input, { target: { value: "react js" } });

  await waitFor(() => {
    // verify effect runs only after delay
    expect(console.log).toHaveBeenCalledWith("API call:", "react js");
  });
});
```

## E2E (Playwright)

- simulate typing fast
- assert only one network request fired

---

# Ops & Deployment

- Debounce search inputs to reduce backend load
- Throttle scroll analytics events (e.g., tracking)
- Avoid memory leaks (clear timers on unmount)
- Use server-side caching alongside client debounce
- Monitor API call frequency in production logs

---

# Pitfalls

- Forgetting to clean up timers in `useEffect`
- Using debounce where throttle is needed (and vice versa)
- Creating new debounced functions on every render
- Blocking urgent UI updates due to excessive delay
- Not considering React Strict Mode double-invocation in dev

## Question 6. How do you use React.forwardRef?

# Short answer

`React.forwardRef` is used to **pass a ref from a parent component to a child component’s DOM node or instance**, enabling direct DOM access (or imperative APIs) through functional components.

---

# Explanation

## 1. Why `forwardRef` exists

In React, `ref` is not passed as a normal prop.

So this does NOT work:

```tsx
function Input(props) {
  return <input ref={props.ref} />; // ❌ ref is not in props
}
```

Because:

- `ref` is reserved by React
- Functional components don’t receive `ref` by default

### Solution: `forwardRef`

It allows a component to **accept a ref argument explicitly** and forward it to a child DOM element or component.

---

## 2. React 18 perspective

In modern React:

- Functional components are primary
- `forwardRef` is essential for reusable UI libraries (design systems)
- Often combined with:
  - `useImperativeHandle` (to expose custom methods)
  - `memo` (to avoid unnecessary re-renders)

Common use cases:

- Input focus management
- Modal open/close control
- Integrating with third-party DOM libraries
- Form libraries (React Hook Form, etc.)

---

## 3. State & architecture trade-offs

### Without forwardRef

- Parent cannot directly control child DOM
- Forces prop drilling or state lifting

### With forwardRef

- Enables **imperative control**
- Useful for **UI component abstractions**
- Should not be overused (breaks declarative flow if misused)

---

# Example (React + TypeScript)

## Setup (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Basic forwardRef example

### Input component

```tsx
import React, { forwardRef } from "react";

type InputProps = {
  placeholder?: string;
};

const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ placeholder }, ref) => {
    return <input ref={ref} placeholder={placeholder} />;
  },
);

export default Input;
```

---

### Parent component

```tsx
import { useRef } from "react";
import Input from "./Input";

export default function App() {
  const inputRef = useRef<HTMLInputElement>(null);

  const focusInput = () => {
    inputRef.current?.focus();
  };

  return (
    <>
      <Input ref={inputRef} placeholder="Enter name" />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}
```

---

## 4. Advanced pattern: `useImperativeHandle`

Used when you want to expose **custom methods instead of raw DOM**

```tsx
import React, { forwardRef, useImperativeHandle, useRef } from "react";

type InputHandle = {
  focus: () => void;
  clear: () => void;
};

const Input = forwardRef<InputHandle>((_, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    clear: () => {
      if (inputRef.current) inputRef.current.value = "";
    },
  }));

  return <input ref={inputRef} />;
});

export default Input;
```

### Parent usage

```tsx
const ref = useRef<InputHandle>(null);

ref.current?.focus();
ref.current?.clear();
```

---

# Tooling & Setup

## Recommended stack

- **Vite** (fast ESM-based dev server)
- **TypeScript** (strongly recommended for refs)
- **React 18+**

Why Vite:

- Fast HMR
- Minimal config
- Modern ES module support

---

## ESM vs CommonJS note

- `forwardRef` is ESM-friendly and tree-shakeable
- Works seamlessly in Vite / Next.js
- Avoid mixing with legacy CommonJS patterns for component libraries

---

# Performance

## Impact of forwardRef

- No runtime performance cost by itself
- Slight abstraction overhead only in development readability

## Optimization strategies

- Combine with `React.memo` for preventing re-renders
- Avoid recreating ref callbacks unnecessarily
- Use `useCallback` for handlers inside forwarded components
- Keep imperative APIs minimal

## Profiling

Use:

- React DevTools Profiler → check unnecessary re-renders
- Highlight updates feature → visualize rerenders

---

# Testing

## Vitest + React Testing Library

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("focuses input using ref", async () => {
  render(<App />);

  const button = screen.getByText("Focus Input");
  await userEvent.click(button);

  const input = screen.getByPlaceholderText("Enter name");
  expect(document.activeElement).toBe(input);
});
```

---

## E2E (Playwright)

```bash
npx playwright test
```

Test:

- click button
- verify input receives focus

---

# Ops & Deployment

- Use forwardRef in **design systems / reusable UI libraries**
- Avoid exposing internal DOM unnecessarily in large apps
- Combine with Error Boundaries for robustness
- Ensure SSR compatibility (Next.js handles refs carefully)
- Keep public imperative APIs stable (breaking changes affect consumers)

---

# Pitfalls

- ❌ Forgetting `forwardRef` breaks ref passing completely
- ❌ Overusing refs instead of declarative state
- ❌ Exposing too much DOM complexity via ref
- ❌ Not typing refs properly in TypeScript
- ❌ Creating unnecessary imperative APIs instead of React patterns

## Question 7. Explain controlled input with dynamic validation

# Short answer

A **controlled input with dynamic validation** in React is an input field whose value is managed by React state, and whose validation logic runs **in real time (on change, blur, or submit)** to provide immediate feedback such as errors, warnings, or formatting rules.

---

# Explanation

## 1. What is a controlled input?

A **controlled input** is an input element whose value is driven by React state:

- React state is the **single source of truth**
- Every keystroke updates state via `onChange`
- UI always reflects state

```tsx id="controlled1"
const [value, setValue] = useState("");
<input value={value} onChange={(e) => setValue(e.target.value)} />;
```

### Why this matters

- Enables validation
- Enables formatting (masking, trimming)
- Enables syncing with external systems (Redux, forms, URL params)

---

## 2. What is dynamic validation?

Dynamic validation means:

- Validation runs **while user types or interacts**
- Not only on form submit

Common triggers:

- `onChange` → real-time validation
- `onBlur` → validate when user leaves field
- `onSubmit` → final validation

---

## 3. React 18 considerations

With React 18:

- Updates are automatically batched → validation state updates are efficient
- Concurrent rendering means UI may be interrupted → validation logic should be **pure and deterministic**
- Avoid expensive validation inside render; use memoization if needed

---

## 4. State management trade-offs

### Local state (recommended for simple forms)

- Fast, simple
- Best for single component forms

### Form libraries (recommended for complex apps)

- React Hook Form (uncontrolled + performant)
- Formik (controlled-heavy approach)
- Zod/Yup for schema validation

### When controlled inputs become expensive

- Large forms (100+ fields)
- High-frequency validation logic
- Complex async validation

---

## 5. Performance considerations

- Avoid validating entire form on every keystroke
- Debounce expensive validations (e.g., API checks)
- Memoize validation functions (`useCallback`)
- Split validation per field instead of global re-validation
- Use `useTransition` for non-urgent validation updates (React 18)

---

# Example (React + TypeScript)

## Setup (Vite)

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## Controlled input with dynamic validation

```tsx id="dynamic1"
import { useState } from "react";

type Errors = {
  email?: string;
  password?: string;
};

export default function SignupForm() {
  const [form, setForm] = useState({
    email: "",
    password: "",
  });

  const [errors, setErrors] = useState<Errors>({});

  const validate = (name: string, value: string) => {
    switch (name) {
      case "email":
        if (!value.includes("@")) return "Invalid email format";
        return "";

      case "password":
        if (value.length < 6) return "Password must be at least 6 characters";
        return "";

      default:
        return "";
    }
  };

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;

    setForm((prev) => ({
      ...prev,
      [name]: value,
    }));

    // dynamic validation per field
    setErrors((prev) => ({
      ...prev,
      [name]: validate(name, value),
    }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();

    const newErrors: Errors = {
      email: validate("email", form.email),
      password: validate("password", form.password),
    };

    setErrors(newErrors);

    const hasErrors = Object.values(newErrors).some(Boolean);

    if (!hasErrors) {
      console.log("Form submitted:", form);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="email"
          value={form.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <p style={{ color: "red" }}>{errors.email}</p>}
      </div>

      <div>
        <input
          name="password"
          type="password"
          value={form.password}
          onChange={handleChange}
          placeholder="Password"
        />
        {errors.password && <p style={{ color: "red" }}>{errors.password}</p>}
      </div>

      <button type="submit">Submit</button>
    </form>
  );
}
```

---

## 6. Advanced pattern: debounced validation

Useful for expensive checks (e.g., username availability API):

```tsx id="debounce1"
import { useEffect, useState } from "react";

function useDebounce(value: string, delay = 400) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const id = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(id);
  }, [value, delay]);

  return debounced;
}
```

---

# Tooling & Setup

## Recommended stack

### Vite (preferred)

- Fast refresh
- ESM-first
- Minimal config

### Form libraries (production-grade apps)

- **React Hook Form** → best performance (uncontrolled model)
- **Zod** → schema-based validation
- **Yup** → alternative schema validation

Example combo:

```bash id="tool1"
npm install react-hook-form zod @hookform/resolvers
```

---

# Performance

## Optimization strategies

- Validate only changed field (not full form)
- Use debouncing for async validation
- Avoid unnecessary re-renders (split components per field)
- Memoize validation logic with `useCallback`
- Use `React.memo` for input components
- Consider `useTransition` for non-urgent validation updates

### React 18 behavior

- Automatic batching reduces validation re-renders
- Concurrent rendering may interrupt updates → keep validation pure

---

# Testing

## Vitest + React Testing Library

```tsx id="test1"
import { render, screen, fireEvent } from "@testing-library/react";
import SignupForm from "./SignupForm";

test("shows validation error for invalid email", () => {
  render(<SignupForm />);

  const input = screen.getByPlaceholderText("Email");

  fireEvent.change(input, {
    target: { value: "invalidemail" },
  });

  expect(screen.getByText("Invalid email format")).toBeInTheDocument();
});
```

---

## E2E (Playwright)

- Type into input
- Assert real-time error appears
- Submit invalid form → block submission

---

# Ops & Deployment

- Validate both client-side and server-side (never trust UI only)
- Ensure consistent validation rules across frontend/backend
- Log validation failures for analytics (optional)
- Use schema validation shared between client and server (Zod)
- Handle accessibility (ARIA error messages)

---

# Pitfalls

- ❌ Validating entire form on every keystroke (performance issue)
- ❌ Mutating state directly instead of using functional updates
- ❌ Mixing controlled and uncontrolled inputs
- ❌ Duplicating validation logic across components
- ❌ Not handling async validation race conditions (stale responses)

## Question 8. How do you fetch API data in React?

# Short answer

In React, API data is typically fetched using **`useEffect` + `fetch/axios` for simple cases**, or **React Query (TanStack Query)** for production-grade apps. The key is to manage **loading, error, and caching states** properly while avoiding memory leaks and race conditions.

---

# Explanation

## 1. Basic approach: `useEffect + fetch`

The most common beginner-to-intermediate approach:

### Flow

- Component mounts
- `useEffect` triggers API call
- State updates with data
- UI re-renders

### React 18 considerations

- Effects run after render
- Strict Mode may run effects twice in dev
- Async calls must be cancellable or guarded to avoid stale updates

---

## 2. State management responsibilities

When fetching data manually, you must handle:

- **Loading state** → UI feedback during request
- **Error state** → failure handling
- **Data state** → API response storage
- **Cleanup** → prevent state update after unmount
- **Race conditions** → ensure latest request wins

---

## 3. Architecture perspective

### Small apps

- `useEffect + fetch`
- local state is sufficient

### Medium apps

- custom hooks (`useUsers`, `useProducts`)
- shared state via Context

### Large apps (recommended)

- **React Query (TanStack Query)** or **SWR**
- caching, retries, deduping, background refetching

---

## 4. Performance considerations

Without optimization:

- repeated API calls on re-renders
- unnecessary network usage
- UI flickering

Optimizations:

- AbortController for cancellation
- memoized dependencies in `useEffect`
- caching via React Query
- debouncing for input-driven fetches
- pagination / infinite queries

---

## 5. React 18 concurrency impact

- Requests can be started and abandoned mid-render
- Strict Mode double-invokes effects in development
- Race conditions become more visible
- Always assume requests may resolve out of order

---

# Example (React + TypeScript)

## Setup (Vite)

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

---

## 1. Manual fetch with useEffect

```tsx id="fetch1"
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function Users() {
  const [data, setData] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    async function fetchUsers() {
      try {
        setLoading(true);

        const res = await fetch("https://jsonplaceholder.typicode.com/users", {
          signal: controller.signal,
        });

        if (!res.ok) throw new Error("Failed to fetch users");

        const json: User[] = await res.json();
        setData(json);
      } catch (err: any) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchUsers();

    return () => {
      controller.abort(); // prevents memory leaks
    };
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {data.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## 2. Custom hook approach (clean architecture)

```tsx id="hook1"
import { useEffect, useState } from "react";

export function useUsers() {
  const [data, setData] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const controller = new AbortController();

    fetch("https://jsonplaceholder.typicode.com/users", {
      signal: controller.signal,
    })
      .then((res) => res.json())
      .then(setData)
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, []);

  return { data, loading };
}
```

Usage:

```tsx id="hook2"
const { data, loading } = useUsers();
```

---

## 3. Production-grade approach: React Query

```bash id="rq1"
npm install @tanstack/react-query
```

### Setup provider

```tsx id="rq2"
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Users />
    </QueryClientProvider>
  );
}
```

### Fetching data

```tsx id="rq3"
import { useQuery } from "@tanstack/react-query";

function fetchUsers() {
  return fetch("https://jsonplaceholder.typicode.com/users").then((res) =>
    res.json(),
  );
}

export default function Users() {
  const { data, isLoading, error } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error occurred</p>;

  return (
    <ul>
      {data.map((u: any) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

---

# Tooling & Setup

## Recommended stack

### Vite

- Fast build tool
- Native ESM
- Best DX for React SPA

### Fetch libraries

- Native `fetch` (built-in)
- `axios` (interceptors, older but popular)
- React Query (best for server state)

---

## ESM vs CommonJS

- Vite uses **ESM**
- React Query and modern libraries are ESM-first
- Tree-shaking reduces bundle size automatically

---

# Performance

## Key optimizations

- Use **React Query caching** instead of refetching
- Use **AbortController** to cancel stale requests
- Avoid fetching inside render
- Use pagination or infinite queries for large datasets
- Debounce input-based API calls
- Memoize query keys and functions

---

## React 18 behavior

- Effects may re-run in Strict Mode (dev only)
- Concurrent rendering may interrupt updates
- Always assume requests can overlap or race

---

# Testing

## Vitest + React Testing Library

```tsx id="test1"
import { render, screen, waitFor } from "@testing-library/react";
import Users from "./Users";

global.fetch = vi.fn(() =>
  Promise.resolve({
    ok: true,
    json: () => Promise.resolve([{ id: 1, name: "John" }]),
  }),
) as any;

test("renders fetched users", async () => {
  render(<Users />);

  await waitFor(() => {
    expect(screen.getByText("John")).toBeInTheDocument();
  });
});
```

---

## E2E (Playwright)

- intercept API calls
- verify loading → success states
- simulate slow network conditions

---

# Ops & Deployment

- Cache API responses where possible (CDN, edge caching)
- Monitor API latency and failure rates
- Use retries with exponential backoff (React Query handles this)
- Implement graceful error UI states
- Secure API keys (never expose secrets in frontend)
- Use SSR (Next.js) for faster initial data loading if needed

---

# Pitfalls

- ❌ Fetching inside render instead of `useEffect`
- ❌ Ignoring cleanup → memory leaks
- ❌ Not handling race conditions in fast-changing inputs
- ❌ Missing loading/error states
- ❌ Overfetching without caching strategy

## Question 9. Difference between fetch and axios in React

# Short answer

**`fetch`** is a built-in browser API for making HTTP requests, while **`axios`** is a third-party library that provides a more feature-rich and developer-friendly abstraction over HTTP requests.

In React, both are used to fetch API data, but **axios simplifies common patterns like JSON parsing, error handling, and request configuration**.

---

# Explanation

## 1. Core differences

### fetch

- Native browser API (no install needed)
- Returns a **Promise of Response**
- Requires manual JSON parsing (`res.json()`)
- Does not throw errors for HTTP errors (like 404/500)

### axios

- External library (`npm install axios`)
- Automatically parses JSON
- Throws errors for HTTP status codes outside 2xx
- Provides interceptors, timeout, cancellation, and defaults

---

## 2. React 18 context

In React:

- Both are used inside `useEffect`, event handlers, or data libraries
- React itself does not prefer either
- Modern apps often use **React Query + fetch/axios underneath**

Key point:
👉 React is **transport-agnostic** — it only cares about state updates, not how data is fetched.

---

## 3. Architecture perspective

### Using fetch

- Lightweight
- Good for small apps
- More boilerplate (manual error handling, headers, JSON parsing)

### Using axios

- Better for large-scale apps
- Centralized API configuration
- Interceptors for auth tokens, logging, retries

---

## 4. State management implications

With both:

- You still manage:
  - loading state
  - error state
  - caching (unless using React Query/SWR)
  - cancellation

But axios reduces boilerplate in:

- error normalization
- response transformation
- request middleware (interceptors)

---

## 5. Performance considerations

Neither `fetch` nor `axios` is inherently faster.

But:

- `fetch` = minimal overhead
- `axios` = slightly heavier bundle but better DX

Optimization considerations:

- Use AbortController (fetch) or cancel tokens (axios legacy)
- Avoid duplicate requests via React Query caching
- Debounce API calls for inputs
- Use pagination/infinite queries for large datasets

---

## 6. React 18 concurrency impact

- Multiple renders may trigger multiple requests
- Always ensure requests are:
  - cancellable
  - idempotent
  - deduplicated (React Query helps here)

---

# Example (React + TypeScript)

## Setup (Vite)

```bash id="setup1"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install axios
npm run dev
```

---

## 1. Fetch example

```tsx id="fetch1"
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function UsersFetch() {
  const [data, setData] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    async function getUsers() {
      try {
        const res = await fetch("https://jsonplaceholder.typicode.com/users", {
          signal: controller.signal,
        });

        if (!res.ok) throw new Error("Fetch failed");

        const json = await res.json();
        setData(json);
      } catch (err: any) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    getUsers();

    return () => controller.abort();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {data.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

---

## 2. Axios example

```tsx id="axios1"
import { useEffect, useState } from "react";
import axios from "axios";

type User = {
  id: number;
  name: string;
};

export default function UsersAxios() {
  const [data, setData] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    async function getUsers() {
      try {
        const response = await axios.get<User[]>(
          "https://jsonplaceholder.typicode.com/users",
          {
            signal: controller.signal,
          },
        );

        setData(response.data);
      } catch (err: any) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    getUsers();

    return () => controller.abort();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {data.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

---

## 3. Key improvement with Axios: centralized API layer

```tsx id="axios2"
import axios from "axios";

export const api = axios.create({
  baseURL: "https://jsonplaceholder.typicode.com",
  timeout: 5000,
});

api.interceptors.request.use((config) => {
  // attach token
  config.headers.Authorization = "Bearer token";
  return config;
});
```

Then:

```tsx id="axios3"
api.get("/users").then((res) => res.data);
```

---

# Tooling & Setup

## Recommended stack

### Vite (preferred)

- Fast ESM-based dev server
- Minimal configuration

### fetch (default choice for modern apps)

- Native
- No dependency

### axios (when needed)

Use when you need:

- interceptors
- request cancellation abstraction
- centralized API config
- better error handling consistency

---

## ESM vs CommonJS

- Both `fetch` and `axios` work in ESM-based Vite setups
- Axios is ESM-compatible in modern versions
- Tree-shaking works better with `fetch`

---

# Performance

## fetch advantages

- Smaller bundle size
- No extra dependency
- Native browser optimization

## axios advantages

- Reduced boilerplate improves maintainability
- Interceptors reduce duplicated logic
- Better structure in large apps

## Optimization strategies

- Use AbortController (fetch) or signal-based cancellation (axios)
- Combine with React Query for caching/deduping
- Debounce requests for input-driven APIs
- Avoid unnecessary re-fetching via dependency control in `useEffect`

---

# Testing

## Vitest + RTL

Mocking fetch:

```tsx id="test1"
global.fetch = vi.fn(() =>
  Promise.resolve({
    ok: true,
    json: () => Promise.resolve([{ id: 1, name: "John" }]),
  }),
) as any;
```

Mocking axios:

```tsx id="test2"
import axios from "axios";

vi.mock("axios");

(axios.get as any).mockResolvedValue({
  data: [{ id: 1, name: "John" }],
});
```

---

## E2E (Playwright)

- intercept network requests
- simulate API failures
- verify loading and error states

---

# Ops & Deployment

- Prefer **fetch + React Query** in modern production apps
- Use axios when API complexity grows
- Centralize API logic in a service layer
- Monitor API errors with tools like Sentry
- Add retries/backoff (React Query preferred)
- Ensure secrets are never exposed in frontend calls

---

# Pitfalls

- ❌ Using axios without central configuration (leads to duplication)
- ❌ Forgetting fetch `.json()` parsing
- ❌ Not handling HTTP error codes in fetch
- ❌ Missing cancellation logic → memory leaks
- ❌ Overusing axios when fetch is sufficient

## Question 10. How do you use setState asynchronously in class components?

# Short answer

In React class components, `setState` is **asynchronous and batched**, meaning state updates are scheduled and not applied immediately. To safely work with the updated state, you should use:

- The **functional form of `setState`** when relying on previous state
- The **callback form of `setState`** when you need to run code after state is updated

---

# Explanation

## 1. Why `setState` is asynchronous

React batches multiple `setState` calls for performance optimization.

This means:

- State updates are **not applied immediately**
- React schedules a re-render
- Multiple updates may be combined (especially in React 18 automatic batching)

### React 18 impact

Even in class components:

- Updates inside event handlers are batched automatically
- Updates inside async callbacks (timeouts, promises) may also be batched
- This makes state consistency more important than ever

---

## 2. The problem with direct state usage

❌ Incorrect approach:

```js id="wrong1"
this.setState({ count: this.state.count + 1 });
this.setState({ count: this.state.count + 1 });
```

Expected: +2
Actual: may still be +1 because `this.state.count` is stale in both calls

---

## 3. Solution 1: Functional `setState`

This is the **recommended approach when state depends on previous state**.

```js id="correct1"
this.setState((prevState) => ({
  count: prevState.count + 1,
}));
```

### Why it works

- React passes the **latest state snapshot**
- Ensures correctness in concurrent and batched updates

---

## 4. Solution 2: Callback after state update

If you need to run logic after state has been applied:

```js id="correct2"
this.setState({ count: this.state.count + 1 }, () => {
  console.log("Updated state:", this.state.count);
});
```

### Use cases

- Logging updated state
- Triggering API calls after state change
- DOM-related logic

---

## 5. Solution 3: Async workflows (promises / timeouts)

`setState` itself does not return a promise, so you cannot `await` it directly.

Instead:

```js id="async1"
handleClick = () => {
  this.setState(
    (prev) => ({ count: prev.count + 1 }),
    () => {
      this.doSomethingAfterUpdate();
    },
  );
};
```

For async flows:

```js id="async2"
async handleSave() {
  this.setState({ loading: true });

  await api.saveData();

  this.setState({ loading: false });
}
```

---

## 6. React 18 concurrency considerations

Even in class components:

- Updates can be interrupted and resumed
- Multiple updates may be merged
- Order is not guaranteed unless using functional updates

Best practices:

- Always use functional `setState` when relying on previous state
- Avoid reading `this.state` immediately after calling `setState`
- Treat state updates as **eventually consistent**

---

## 7. State management perspective

Class component state:

- Local and encapsulated
- Suitable for UI-specific state
- Not ideal for complex shared state (Redux/Zustand preferred)

Modern React:

- Functional components + hooks (`useState`, `useReducer`) are preferred
- Class components still supported but mostly legacy

---

# Example (Class Component)

## Setup (Vite + React)

```bash id="setup1"
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

---

## Safe asynchronous `setState`

```jsx id="class1"
import React from "react";

class Counter extends React.Component {
  state = {
    count: 0,
    loading: false,
  };

  incrementTwice = () => {
    this.setState(
      (prev) => ({ count: prev.count + 1 }),
      () => {
        this.setState(
          (prev) => ({ count: prev.count + 1 }),
          () => {
            console.log("Final count:", this.state.count);
          },
        );
      },
    );
  };

  fetchData = async () => {
    this.setState({ loading: true });

    try {
      await new Promise((res) => setTimeout(res, 1000));

      this.setState({ loading: false });
    } catch (err) {
      this.setState({ loading: false });
    }
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>

        <button onClick={this.incrementTwice}>Increment Twice</button>

        <button onClick={this.fetchData}>Fetch Data</button>

        {this.state.loading && <p>Loading...</p>}
      </div>
    );
  }
}

export default Counter;
```

---

# Tooling & Setup

## Recommended modern approach

Even though class components are valid, modern React prefers:

- Functional components
- `useState` instead of `this.setState`
- `useEffect` instead of lifecycle methods

### Why Vite

- Fast refresh
- ESM support
- Lightweight setup
- Preferred over CRA (deprecated)

---

## ESM vs CommonJS

- Class components work in both systems
- Modern bundlers (Vite, Next.js) use ESM
- Tree-shaking and optimization are better with functional components

---

# Performance

## Why React batches `setState`

- Reduces re-renders
- Improves rendering performance
- Enables concurrent rendering optimizations

## Best practices

- Use functional `setState` for correctness
- Avoid multiple sequential `setState` calls without callbacks
- Keep state minimal (avoid derived state)
- Avoid unnecessary state duplication

## Profiling tools

- React DevTools Profiler → detect unnecessary renders
- Highlight updates → visualize state-driven re-renders

---

# Testing

## Vitest + React Testing Library

```jsx id="test1"
import { render, fireEvent, screen } from "@testing-library/react";
import Counter from "./Counter";

test("increments correctly", () => {
  render(<Counter />);

  fireEvent.click(screen.getByText("Increment Twice"));

  expect(screen.getByText(/Count:/)).toBeInTheDocument();
});
```

## Key testing idea

- Do NOT test timing of `setState`
- Test final rendered output instead

---

# Ops & Deployment

- Class components still work in production React apps
- Ensure consistent state updates in async flows
- Avoid mixing class and functional state logic in the same feature
- Prefer migrating to functional components for new development
- Monitor unexpected state races in async workflows

---

# Pitfalls

- ❌ Using `this.state` directly inside `setState`
- ❌ Assuming `setState` is synchronous
- ❌ Chaining multiple updates without functional form
- ❌ Trying to `await this.setState()` (not supported)
- ❌ Overusing class components in modern React apps

## Question 11. Explain React Fiber architecture

## Question 12. How does React handle reconciliation?

## Question 13. Explain the difference between reconciliation and diffing

## Question 14. How do you implement server-side rendering (SSR) in React?

## Question 15. What is Next.js and how is it related to React?

## Question 16. How do you implement static site generation (SSG)?

## Question 17. Explain hydration in SSR

## Question 18. How do you handle global state in large React applications?

## Question 19. Compare Redux, Zustand, Jotai, and Recoil

## Question 20. Explain middleware in Redux
