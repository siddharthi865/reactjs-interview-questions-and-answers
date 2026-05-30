# Set 14

| S.No. | Question                                                                                                                                               |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you use `useLayoutEffect` to measure DOM elements?](#question-1-how-do-you-use-uselayouteffect-to-measure-dom-elements)                        |
| 2.    | [How do you implement infinite scroll with pagination?](#question-2-how-do-you-implement-infinite-scroll-with-pagination)                              |
| 3.    | [How do you handle conditional rendering based on API response?](#question-3-how-do-you-handle-conditional-rendering-based-on-api-response)            |
| 4.    | [How do you implement a toast notification system?](#question-4-how-do-you-implement-a-toast-notification-system)                                      |
| 5.    | [How do you implement drag-and-drop lists in React?](#question-5-how-do-you-implement-drag-and-drop-lists-in-react)                                    |
| 6.    | [How do you implement collapsible panels or accordions?](#question-6-how-do-you-implement-collapsible-panels-or-accordions)                            |
| 7.    | [How do you manage state for multiple modals?](#question-7-how-do-you-manage-state-for-multiple-modals)                                                |
| 8.    | [How do you implement sorting functionality in a table?](#question-8-how-do-you-implement-sorting-functionality-in-a-table)                            |
| 9.    | [How do you implement a multi-select dropdown in React?](#question-9-how-do-you-implement-a-multi-select-dropdown-in-react)                            |
| 10.   | [How do you handle errors globally using an ErrorBoundary component?](#question-10-how-do-you-handle-errors-globally-using-an-errorboundary-component) |
| 11.   | [How do you implement server-side rendering with data fetching?](#question-11-how-do-you-implement-server-side-rendering-with-data-fetching)           |
| 12.   | [How do you implement static generation with Next.js?](#question-12-how-do-you-implement-static-generation-with-nextjs)                                |
| 13.   | [How do you implement hydration in SSR applications?](#question-13-how-do-you-implement-hydration-in-ssr-applications)                                 |
| 14.   | [How do you implement code splitting with dynamic imports?](#question-14-how-do-you-implement-code-splitting-with-dynamic-imports)                     |
| 15.   | [How do you handle concurrency in React 18?](#question-15-how-do-you-handle-concurrency-in-react-18)                                                   |
| 16.   | [How do you implement suspense for asynchronous data fetching?](#question-16-how-do-you-implement-suspense-for-asynchronous-data-fetching)             |
| 17.   | [How do you implement caching for API requests in React?](#question-17-how-do-you-implement-caching-for-api-requests-in-react)                         |
| 18.   | [How do you implement optimistic updates with React Query or Redux?](#question-18-how-do-you-implement-optimistic-updates-with-react-query-or-redux)   |
| 19.   | [How do you implement real-time updates with WebSockets?](#question-19-how-do-you-implement-real-time-updates-with-websockets)                         |
| 20.   | [How do you handle authentication and authorization in React apps?](#question-20-how-do-you-handle-authentication-and-authorization-in-react-apps)     |

## Question 1. How do you use `useLayoutEffect` to measure DOM elements?

# How do you use `useLayoutEffect` to measure DOM elements?

## Short answer

`useLayoutEffect` runs **synchronously after React has updated the DOM but before the browser paints**. This makes it the correct hook for measuring DOM elements (`getBoundingClientRect()`, `offsetWidth`, `scrollHeight`, etc.) or making layout-related DOM updates without causing visible flickering.

Use it with a `ref` to access the DOM node.

---

# Explanation

React provides two effect hooks:

- **`useEffect`** → Runs **after paint** (non-blocking).
- **`useLayoutEffect`** → Runs **before paint** (blocking).

### Why this matters

If you need to:

- Measure element width/height
- Position tooltips/popovers
- Calculate scroll position
- Resize canvases
- Synchronize animations

using `useEffect` may briefly render the UI in the wrong position and then update it, causing a **layout shift (flicker)**.

`useLayoutEffect` avoids this because React waits until your layout calculations finish before painting.

### Execution order

```
Render
    ↓
DOM updated
    ↓
useLayoutEffect
    ↓
State updates (if any)
    ↓
Browser Paint
    ↓
useEffect
```

---

## Example (React + TypeScript using Vite)

### Create the project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Measure a DOM element

```tsx
import { useLayoutEffect, useRef, useState } from "react";

export default function App() {
  const boxRef = useRef<HTMLDivElement>(null);
  const [width, setWidth] = useState(0);

  useLayoutEffect(() => {
    if (boxRef.current) {
      const { width } = boxRef.current.getBoundingClientRect();
      setWidth(width);
    }
  }, []);

  return (
    <>
      <div
        ref={boxRef}
        style={{
          width: "300px",
          padding: "20px",
          background: "#61dafb",
        }}
      >
        Measure me
      </div>

      <p>Width: {width}px</p>
    </>
  );
}
```

### What happens?

1. React renders the `<div>`.
2. DOM is committed.
3. `useLayoutEffect` executes.
4. `getBoundingClientRect()` measures the width.
5. State updates synchronously.
6. Browser paints only the final UI.

The user never sees an incorrect width.

---

# Tooling & Setup

- **Use Vite** for modern React development. `create-react-app` is deprecated.
- For SSR or full-stack applications, prefer **Next.js (App Router)** or **Remix**.
- Vite uses **native ESM** during development for fast startup and HMR, while bundling optimized production assets (typically with Rollup). CommonJS is primarily relevant for older Node.js ecosystems and should be avoided for new frontend code when possible.

---

# Performance

### Avoid unnecessary measurements

Measuring layout forces the browser to calculate layout.

Bad:

```tsx
useLayoutEffect(() => {
  console.log(ref.current?.getBoundingClientRect());
});
```

Runs after every render.

Better:

```tsx
useLayoutEffect(() => {
  // measure once
}, []);
```

or only when dependencies change.

### Measure on resize

```tsx
useLayoutEffect(() => {
  const measure = () => {
    console.log(ref.current?.getBoundingClientRect());
  };

  measure();

  window.addEventListener("resize", measure);

  return () => window.removeEventListener("resize", measure);
}, []);
```

For production, prefer **`ResizeObserver`** over listening to `window.resize` because it reacts to element size changes regardless of their cause.

### React 18 considerations

- **Automatic batching** batches state updates made inside `useLayoutEffect`, reducing unnecessary re-renders.
- `useLayoutEffect` still blocks painting, so keep the work minimal.
- Avoid expensive computations inside the hook; measure only what's necessary.

### Optimization tips

- Use **`React.memo`** to avoid unnecessary child renders.
- Use **`useMemo`** for expensive derived values.
- Use **`useCallback`** for stable callback references when passing handlers to memoized children.
- Use **`React.lazy`** and dynamic imports for code splitting.
- Use the **React DevTools Profiler** to identify unnecessary renders and layout work.

---

# Testing

Layout measurements are limited in JSDOM because it does not perform real browser layout. Mock measurement APIs when unit testing.

Example with **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example mock:

```tsx
vi.spyOn(HTMLElement.prototype, "getBoundingClientRect").mockReturnValue({
  width: 300,
  height: 100,
  top: 0,
  left: 0,
  right: 300,
  bottom: 100,
  x: 0,
  y: 0,
  toJSON: () => {},
});
```

For end-to-end verification of real layout and positioning, use **Playwright** in a real browser.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures (note that they do not catch errors thrown asynchronously inside effects).
- Avoid excessive synchronous work in `useLayoutEffect`, especially on low-powered devices, because it delays painting.
- For SSR, note that `useLayoutEffect` does not run on the server. If a component must render on the server, guard browser-only logic or use an isomorphic pattern that falls back to `useEffect` on the server.
- Keep bundles small with route-level code splitting and lazy loading.
- Deploy optimized production builds behind a CDN or edge network to minimize latency.

---

# Pitfalls

- **Don't use `useLayoutEffect` unless you need synchronous layout work.** Prefer `useEffect` for data fetching and most side effects.
- **Avoid triggering render loops.** Updating state based on measurements every render can cause repeated layouts and poor performance.
- **Don't perform heavy calculations inside `useLayoutEffect`.** It blocks the browser from painting, harming responsiveness.

## Question 2. How do you implement infinite scroll with pagination?

# How do you implement infinite scroll with pagination?

## Short answer

Implement infinite scroll by combining **server-side pagination** with the **Intersection Observer API**. Fetch data page by page, append new items to existing state, and trigger the next page request when a sentinel element at the bottom of the list becomes visible. Prevent duplicate requests with loading flags, stop when there are no more pages, and consider virtualization for very large lists.

---

# Explanation

Infinite scrolling is essentially **pagination without visible page controls**.

The typical flow is:

```text
Initial Load (Page 1)
        │
        ▼
Render items
        │
        ▼
Observer watches bottom sentinel
        │
        ▼
Sentinel becomes visible
        │
        ▼
Fetch Page 2
        │
        ▼
Append items
        │
        ▼
Observe again
        │
        ▼
Repeat until hasMore = false
```

### Core architecture

A production-ready implementation typically maintains:

- **`items`** → accumulated list
- **`page`** → current page number or cursor
- **`loading`** → prevents duplicate requests
- **`hasMore`** → indicates whether additional data exists
- **`error`** → handles API failures

Example API response:

```json
{
  "data": [...],
  "page": 3,
  "totalPages": 8,
  "hasMore": true
}
```

Cursor-based pagination (`nextCursor`) is often preferred over page numbers because it is more stable when data changes frequently.

### React 18 considerations

- **Automatic batching** groups state updates (`setItems`, `setPage`, `setLoading`) to reduce re-renders.
- Keep the observer callback lightweight and avoid expensive synchronous work.
- Separate fetching logic from rendering for easier testing and reuse (e.g., a custom `useInfiniteScroll` hook).

---

# Example (React + TypeScript using Vite)

### Create the project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Infinite scroll component

```tsx
import { useCallback, useEffect, useRef, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users, setUsers] = useState<User[]>([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  const observer = useRef<IntersectionObserver | null>(null);

  const lastElementRef = useCallback(
    (node: HTMLLIElement | null) => {
      if (loading) return;

      observer.current?.disconnect();

      observer.current = new IntersectionObserver(([entry]) => {
        if (entry.isIntersecting && hasMore) {
          setPage((p) => p + 1);
        }
      });

      if (node) observer.current.observe(node);
    },
    [loading, hasMore],
  );

  useEffect(() => {
    async function fetchUsers() {
      setLoading(true);

      const res = await fetch(
        `https://jsonplaceholder.typicode.com/users?_page=${page}&_limit=3`,
      );

      const data: User[] = await res.json();

      setUsers((prev) => [...prev, ...data]);

      if (data.length < 3) {
        setHasMore(false);
      }

      setLoading(false);
    }

    fetchUsers();
  }, [page]);

  return (
    <>
      <ul>
        {users.map((user, index) => {
          if (index === users.length - 1) {
            return (
              <li key={user.id} ref={lastElementRef}>
                {user.name}
              </li>
            );
          }

          return <li key={user.id}>{user.name}</li>;
        })}
      </ul>

      {loading && <p>Loading...</p>}
      {!hasMore && <p>No more users.</p>}
    </>
  );
}
```

### Why this works

- Page 1 loads initially.
- The last list item is observed.
- When it enters the viewport, the observer increments the page.
- New items are appended rather than replacing existing ones.
- Fetching stops when `hasMore` becomes `false`.

---

# Tooling & Setup

- **Prefer Vite** for modern React applications. Avoid Create React App because it is deprecated.
- **Next.js App Router** is a strong choice when SSR, SEO, or streaming is required. Infinite scrolling is often implemented in Client Components, while initial data can be fetched on the server.
- Vite uses **ES Modules (ESM)** during development for fast startup and Hot Module Replacement (HMR). Production builds are optimized with Rollup. CommonJS is mainly relevant for legacy Node.js packages.

---

# Performance

### 1. Use `IntersectionObserver`

Avoid listening to the `scroll` event on every frame.

```ts
new IntersectionObserver(callback);
```

It is more efficient because the browser optimizes visibility detection.

### 2. Virtualize large lists

Thousands of DOM nodes hurt performance. Use libraries such as:

- **react-window**
- **react-virtualized**
- **@tanstack/react-virtual**

Only visible rows are rendered.

### 3. Memoize expensive rows

```tsx
const UserRow = React.memo(UserItem);
```

Avoid re-rendering unchanged items.

### 4. Stable callbacks

```tsx
const loadMore = useCallback(() => {
  // fetch next page
}, []);
```

Prevents unnecessary observer recreation when possible.

### 5. Cache fetched pages

Libraries like **TanStack Query** or **SWR** provide:

- request deduplication
- caching
- background refetching
- retry logic
- stale-while-revalidate behavior
- built-in support for infinite queries

### 6. Profile rendering

Use the React DevTools **Profiler** to identify:

- unnecessary list re-renders
- expensive row components
- repeated observer setup
- excessive fetches

---

# Testing

Use **Vitest + React Testing Library** for unit and integration tests.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Typical tests include:

- initial page loads
- observer callback triggers next fetch
- new items append correctly
- loading indicator appears during fetch
- `hasMore` stops additional requests
- API errors display fallback UI

For end-to-end validation, use **Playwright** to simulate scrolling and verify pagination behavior in a real browser.

---

# Ops & Deployment

- Use an **Error Boundary** to isolate rendering failures (API errors should still be handled within the fetching logic).
- Cancel in-flight requests on unmount using `AbortController` to avoid updating state after a component is removed.
- Debounce or guard against rapid repeated triggers from the observer if your API is slow.
- Support SSR when appropriate by rendering the first page on the server and hydrating the client to continue loading additional pages.
- Minimize bundle size with route-level code splitting and lazy loading, and serve production assets through a CDN or edge network.

---

# Pitfalls

- **Don't fetch the same page multiple times.** Use `loading` and `hasMore` guards or a request cache.
- **Don't use array indexes as keys.** Use stable IDs so React can reconcile items correctly.
- **Don't render thousands of DOM nodes.** Combine infinite scrolling with virtualization for large datasets.

## Question 3. How do you handle conditional rendering based on API response?

# How do you handle conditional rendering based on API response?

## Short answer

Handle conditional rendering by maintaining separate UI states such as **loading**, **success**, **error**, and **empty**. Render different components based on the current API state instead of relying on nested `if` statements or checking only whether data exists.

---

# Explanation

A typical API request goes through several states:

```text
Idle
   │
   ▼
Loading
   │
   ▼
Request Finished
   │
   ├──────────────┐
   ▼              ▼
Success         Error
   │
   ▼
Has Data?
   │
 ┌─┴───────┐
 ▼         ▼
Data      Empty State
```

In React, you usually manage these states with:

- **`loading`** → request in progress
- **`error`** → request failed
- **`data`** → successful response
- **Empty state** → request succeeded but returned no records

A common pattern is:

```tsx
if (loading) return <LoadingSpinner />;
if (error) return <ErrorMessage />;
if (data.length === 0) return <EmptyState />;

return <DataList data={data} />;
```

This approach keeps components easy to read and maintain.

### React 18 considerations

- **Automatic batching** groups multiple state updates (`setData`, `setLoading`, `setError`) into a single render.
- Keep API fetching logic separate from presentation, either in a custom hook or a data-fetching library like **TanStack Query**.
- For complex applications, prefer a state machine or query library over manually tracking many boolean flags.

---

# Example (React + TypeScript using Vite)

### Create the project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Conditional rendering based on API response

```tsx
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchUsers() {
      try {
        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users",
        );

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data: User[] = await response.json();
        setUsers(data);
      } catch (err) {
        setError((err as Error).message);
      } finally {
        setLoading(false);
      }
    }

    fetchUsers();
  }, []);

  if (loading) {
    return <h2>Loading...</h2>;
  }

  if (error) {
    return <h2>Error: {error}</h2>;
  }

  if (users.length === 0) {
    return <h2>No users found.</h2>;
  }

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### UI flow

```text
Loading...
      │
      ▼
 ┌───────────────┐
 │ API Response  │
 └───────────────┘
      │
 ┌────┴─────┐
 ▼          ▼
Error     Success
             │
      ┌──────┴──────┐
      ▼             ▼
 Empty Data     Show List
```

---

# Tooling & Setup

- **Use Vite** for modern React development. Avoid Create React App because it is deprecated.
- For production applications requiring SSR, SEO, or streaming, use **Next.js App Router**. Initial API data can be fetched on the server, while client-side interactions continue after hydration.
- Vite uses **ES Modules (ESM)** for fast development with Hot Module Replacement (HMR). Production builds are optimized with Rollup. CommonJS is mainly for legacy Node.js packages.

---

# Performance

### 1. Avoid unnecessary re-renders

Memoize expensive child components:

```tsx
const UserList = React.memo(UserListComponent);
```

### 2. Cache API responses

Instead of manual state management, use **TanStack Query** or **SWR** for:

- request caching
- background refetching
- retries
- deduplication
- stale-while-revalidate

### 3. Memoize derived data

```tsx
const sortedUsers = useMemo(
  () => [...users].sort((a, b) => a.name.localeCompare(b.name)),
  [users],
);
```

### 4. Stable callbacks

```tsx
const handleRefresh = useCallback(() => {
  fetchUsers();
}, []);
```

### 5. Lazy load heavy UI

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

Wrap lazy-loaded components with `Suspense` to show a fallback while they load.

### 6. Profile rendering

Use the **React DevTools Profiler** to identify:

- unnecessary renders
- expensive list updates
- slow child components
- repeated API-triggered renders

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Typical tests include:

- loading spinner is shown initially
- successful response renders the data
- empty response renders the empty state
- failed response renders the error message
- retry logic works correctly (if implemented)

Use **Playwright** for end-to-end testing to validate loading, success, and error flows against a real backend or mocked API.

---

# Ops & Deployment

- Handle API failures gracefully with user-friendly error messages and optional retry actions.
- Use **Error Boundaries** for rendering errors; note they do **not** catch errors from asynchronous API calls.
- Cancel in-flight requests with `AbortController` when components unmount to avoid setting state on unmounted components.
- For SSR, prefetch critical data on the server to improve perceived performance and SEO.
- Optimize bundle size using route-level code splitting, lazy loading, and CDN/edge deployment.

---

# Pitfalls

- **Don't render data before checking `loading`.** This can cause UI flickering or access to undefined values.
- **Don't rely on `data && ...` alone.** Distinguish between loading, error, empty, and success states explicitly.
- **Don't ignore failed HTTP responses.** Always check `response.ok` before parsing the response body.

## Question 4. How do you implement a toast notification system?

## Question 5. How do you implement drag-and-drop lists in React?

## Question 6. How do you implement collapsible panels or accordions?

## Question 7. How do you manage state for multiple modals?

## Question 8. How do you implement sorting functionality in a table?

## Question 9. How do you implement a multi-select dropdown in React?

## Question 10. How do you handle errors globally using an ErrorBoundary component?

## Question 11. How do you implement server-side rendering with data fetching?

## Question 12. How do you implement static generation with Next.js?

## Question 13. How do you implement hydration in SSR applications?

## Question 14. How do you implement code splitting with dynamic imports?

## Question 15. How do you handle concurrency in React 18?

## Question 16. How do you implement suspense for asynchronous data fetching?

## Question 17. How do you implement caching for API requests in React?

## Question 18. How do you implement optimistic updates with React Query or Redux?

## Question 19. How do you implement real-time updates with WebSockets?

## Question 20. How do you handle authentication and authorization in React apps?
