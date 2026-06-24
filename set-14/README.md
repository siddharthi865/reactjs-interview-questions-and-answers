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

# How do you implement a toast notification system?

## Short answer

A toast notification system is typically implemented using **React Context + a custom hook** (or a library like **React Hot Toast** or **Sonner**) to provide a global API such as `showToast()`. Toasts are rendered from a single provider, automatically dismiss after a timeout, support multiple notification types (success, error, info), and are usually rendered via a **Portal** to avoid layout issues.

---

# Explanation

A production-ready toast system has four main parts:

1. **Toast Provider** – Stores the list of active toasts.
2. **Toast Context** – Exposes methods like `showToast()` and `removeToast()`.
3. **Toast Container** – Renders all active toasts, usually fixed to a screen corner.
4. **Toast Component** – Displays an individual notification with optional auto-dismiss and animations.

Architecture:

```text
User Action
     │
     ▼
showToast("Saved!", "success")
     │
     ▼
Toast Context
     │
     ▼
Add Toast to State
     │
     ▼
Toast Container
     │
     ▼
Toast Component
     │
     ▼
Auto-dismiss → Remove Toast
```

### Why Context?

Instead of passing notification callbacks through props, Context provides a global API:

```tsx
const { showToast } = useToast();

showToast("Profile updated", "success");
```

This avoids prop drilling and allows any component to trigger notifications.

### React 18 considerations

- **Automatic batching** ensures adding/removing multiple toasts results in fewer re-renders.
- Keep toast state localized inside the provider rather than a global Redux store unless notifications need to interact with broader application state.
- Use stable callbacks (`useCallback`) to avoid unnecessary re-renders of consumers.

---

# Example (React + TypeScript using Vite)

### Create the project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Toast Provider

```tsx
import {
  createContext,
  useCallback,
  useContext,
  useState,
  ReactNode,
} from "react";

type Toast = {
  id: number;
  message: string;
  type: "success" | "error";
};

type ToastContextType = {
  showToast: (message: string, type: Toast["type"]) => void;
};

const ToastContext = createContext<ToastContextType | null>(null);

export function ToastProvider({ children }: { children: ReactNode }) {
  const [toasts, setToasts] = useState<Toast[]>([]);

  const showToast = useCallback((message: string, type: Toast["type"]) => {
    const id = Date.now();

    setToasts((prev) => [...prev, { id, message, type }]);

    setTimeout(() => {
      setToasts((prev) => prev.filter((toast) => toast.id !== id));
    }, 3000);
  }, []);

  return (
    <ToastContext.Provider value={{ showToast }}>
      {children}

      <div
        style={{
          position: "fixed",
          top: 20,
          right: 20,
        }}
      >
        {toasts.map((toast) => (
          <div
            key={toast.id}
            style={{
              marginBottom: 10,
              padding: 12,
              color: "#fff",
              background: toast.type === "success" ? "green" : "crimson",
            }}
          >
            {toast.message}
          </div>
        ))}
      </div>
    </ToastContext.Provider>
  );
}

export function useToast() {
  const context = useContext(ToastContext);

  if (!context) {
    throw new Error("useToast must be used inside ToastProvider");
  }

  return context;
}
```

### Usage

```tsx
import { ToastProvider, useToast } from "./ToastProvider";

function Dashboard() {
  const { showToast } = useToast();

  return (
    <button onClick={() => showToast("Saved successfully!", "success")}>
      Save
    </button>
  );
}

export default function App() {
  return (
    <ToastProvider>
      <Dashboard />
    </ToastProvider>
  );
}
```

---

# Tooling & Setup

- **Use Vite** for modern React applications. Avoid Create React App because it is deprecated.
- For production applications, many teams use:
  - **React Hot Toast** (lightweight, simple API)
  - **Sonner** (modern, accessible, highly customizable)
  - **Notistack** (great with Material UI)

- In **Next.js App Router**, place the toast provider in the root layout so all Client Components can access it.
- Vite uses **ES Modules (ESM)** for development and Rollup for optimized production builds. CommonJS is primarily for legacy Node.js packages.

---

# Performance

### 1. Memoize context functions

```tsx
const showToast = useCallback(() => {}, []);
```

Prevents unnecessary consumer re-renders.

### 2. Keep provider state isolated

Only the toast container should re-render when notifications change.

### 3. Render through a Portal

Use `createPortal` to render the toast container into `document.body`. This avoids clipping by parent containers with `overflow: hidden` and simplifies positioning.

### 4. Limit active toasts

Avoid rendering dozens of notifications simultaneously. Queue or cap visible toasts (e.g., maximum of 3–5).

### 5. Animate efficiently

Use CSS transitions or animation libraries (e.g., Framer Motion) with transform/opacity animations to minimize layout work.

### 6. Profile rendering

Use the React DevTools **Profiler** to verify:

- only the toast container updates
- unrelated components do not re-render
- context updates remain localized

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Typical tests:

- toast appears after `showToast()`
- auto-dismiss removes the toast
- multiple toasts render correctly
- close button removes a toast immediately
- correct styling for success/error variants

For end-to-end testing, use **Playwright** to verify notifications appear after user actions.

---

# Ops & Deployment

- Log important events (e.g., failed API calls) separately from user-facing toasts; don't rely on notifications for observability.
- Use **Error Boundaries** for rendering errors, but surface recoverable issues (e.g., API failures) via toasts.
- Ensure accessibility with `role="status"` or `aria-live="polite"` (or `assertive` for critical errors) so screen readers announce notifications.
- Keep the toast provider mounted at the application root for consistent behavior across routes.
- Lazy-load animation libraries if they are only used for notifications to keep the initial bundle small.

---

# Pitfalls

- **Don't create multiple toast providers.** Use a single global provider to avoid inconsistent behavior.
- **Don't use array indexes as toast IDs.** Use stable unique IDs so React reconciles correctly.
- **Don't forget to clean up timers.** If you manage timers manually, clear them on unmount to avoid memory leaks or state updates after unmount.

## Question 5. How do you implement drag-and-drop lists in React?

# How do you implement drag-and-drop lists in React?

## Short answer

Implement drag-and-drop lists using a dedicated library such as **@dnd-kit** (recommended) or **Pragmatic Drag and Drop**. Store the list in React state, update the order when a drag operation completes, and render items with stable IDs. For large lists, combine drag-and-drop with virtualization.

> **Note:** **react-beautiful-dnd** is archived and no longer maintained. For new projects, prefer **@dnd-kit**.

---

# Explanation

A drag-and-drop list consists of four parts:

1. **Draggable items** – Elements users can drag.
2. **Droppable container** – Area where items can be reordered.
3. **Drag context** – Tracks the active drag operation.
4. **State update** – Reorders the list after dropping.

Architecture:

```text
User drags item
       │
       ▼
Drag starts
       │
       ▼
Drag Context tracks active item
       │
       ▼
Drop occurs
       │
       ▼
Calculate new index
       │
       ▼
Update React state
       │
       ▼
React re-renders reordered list
```

### Why use a library?

Implementing drag-and-drop with the HTML5 Drag and Drop API is possible but becomes difficult when handling:

- touch devices
- keyboard accessibility
- collision detection
- smooth animations
- nested lists
- sortable grids

Libraries like **@dnd-kit** solve these problems while remaining flexible and performant.

### React 18 considerations

- **Automatic batching** minimizes renders when updating drag state.
- Use immutable updates when reordering arrays.
- Keep list items memoized if they contain expensive UI.

---

# Example (React + TypeScript using Vite)

### Create the project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm install @dnd-kit/core @dnd-kit/sortable @dnd-kit/utilities
npm run dev
```

### Sortable list

```tsx
import { useState } from "react";
import { DndContext, closestCenter, DragEndEvent } from "@dnd-kit/core";

import {
  SortableContext,
  verticalListSortingStrategy,
  arrayMove,
  useSortable,
} from "@dnd-kit/sortable";

import { CSS } from "@dnd-kit/utilities";

function SortableItem({ id }: { id: string }) {
  const { attributes, listeners, setNodeRef, transform, transition } =
    useSortable({ id });

  const style = {
    transform: CSS.Transform.toString(transform),
    transition,
    padding: "12px",
    marginBottom: "8px",
    border: "1px solid #ccc",
    background: "#fff",
    cursor: "grab",
  };

  return (
    <div ref={setNodeRef} style={style} {...attributes} {...listeners}>
      {id}
    </div>
  );
}

export default function App() {
  const [items, setItems] = useState(["React", "Vue", "Angular", "Svelte"]);

  function handleDragEnd(event: DragEndEvent) {
    const { active, over } = event;

    if (!over || active.id === over.id) return;

    setItems((items) => {
      const oldIndex = items.indexOf(active.id as string);
      const newIndex = items.indexOf(over.id as string);

      return arrayMove(items, oldIndex, newIndex);
    });
  }

  return (
    <DndContext collisionDetection={closestCenter} onDragEnd={handleDragEnd}>
      <SortableContext items={items} strategy={verticalListSortingStrategy}>
        {items.map((item) => (
          <SortableItem key={item} id={item} />
        ))}
      </SortableContext>
    </DndContext>
  );
}
```

### How it works

1. User drags an item.
2. `DndContext` tracks the drag operation.
3. `handleDragEnd()` receives the source and destination.
4. `arrayMove()` creates a reordered array.
5. React re-renders the list with the new order.

---

# Tooling & Setup

- **Use Vite** for new React projects. Avoid Create React App because it is deprecated.
- Recommended libraries:
  - **@dnd-kit** – Modern, accessible, flexible, actively maintained.
  - **Pragmatic Drag and Drop** – Well-suited for complex enterprise interactions.

- **Next.js App Router** works well for SSR applications; drag-and-drop components should be Client Components because they rely on browser events.
- Vite uses **ES Modules (ESM)** for development and Rollup for optimized production builds. CommonJS is mainly relevant for older Node.js packages.

---

# Performance

### 1. Memoize list items

```tsx
const Item = React.memo(ItemComponent);
```

Avoid re-rendering every row during a drag.

### 2. Stable callbacks

```tsx
const handleDragEnd = useCallback((event: DragEndEvent) => {
  // reorder items
}, []);
```

Prevents unnecessary re-renders of child components.

### 3. Use stable IDs

Avoid array indexes as keys.

```tsx
key={item.id}
```

Stable IDs ensure React reconciliation works correctly.

### 4. Virtualize large lists

For thousands of items, combine drag-and-drop with:

- **@tanstack/react-virtual**
- **react-window**

Only visible rows are rendered.

### 5. Minimize state updates

Only update state on **drop**, not on every pointer movement, unless your UX specifically requires live reordering.

### 6. Profile rendering

Use the React DevTools **Profiler** to confirm:

- only affected items re-render
- drag interactions remain smooth
- expensive children are memoized appropriately

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Test cases:

- list renders correctly
- drag completion updates item order
- keyboard drag interactions (if supported)
- accessibility attributes are present
- drop outside a valid target leaves the order unchanged

Use **Playwright** for end-to-end testing because real pointer interactions are best validated in a browser.

---

# Ops & Deployment

- Wrap drag-and-drop areas with **Error Boundaries** to isolate rendering failures.
- Log drag-related errors for debugging complex workflows.
- Ensure keyboard accessibility and screen reader announcements for reorder operations.
- Code-split drag-and-drop libraries if they are only used on specific routes to reduce the initial bundle size.
- Deploy optimized production builds behind a CDN or edge network for faster asset delivery.

---

# Pitfalls

- **Don't use array indexes as keys.** Always use stable unique IDs.
- **Don't mutate the original array.** Return a new array when reordering to preserve React's immutable state model.
- **Don't build new projects with archived drag-and-drop libraries.** Prefer actively maintained solutions such as **@dnd-kit**.

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
