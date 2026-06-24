# Set 19

| #   | Question                                                                                                                                                                               |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement virtualized lists for large datasets?](#question-1-how-do-you-implement-virtualized-lists-for-large-datasets)                                                    |
| 2   | [How do you handle dynamic tabs with add/remove functionality?](#question-2-how-do-you-handle-dynamic-tabs-with-addremove-functionality)                                               |
| 3   | [How do you implement a toast notification system globally?](#question-3-how-do-you-implement-a-toast-notification-system-globally)                                                    |
| 4   | [How do you implement a live search component that fetches results dynamically?](#question-4-how-do-you-implement-a-live-search-component-that-fetches-results-dynamically)            |
| 5   | [How do you create a reusable card component with dynamic content?](#question-5-how-do-you-create-a-reusable-card-component-with-dynamic-content)                                      |
| 6   | [How do you implement keyboard navigation for interactive elements?](#question-6-how-do-you-implement-keyboard-navigation-for-interactive-elements)                                    |
| 7   | [How do you implement image preview before upload?](#question-7-how-do-you-implement-image-preview-before-upload)                                                                      |
| 8   | [How do you debounce window scroll events in React?](#question-8-how-do-you-debounce-window-scroll-events-in-react)                                                                    |
| 9   | [How do you implement form reset after submission in functional components?](#question-9-how-do-you-implement-form-reset-after-submission-in-functional-components)                    |
| 10  | [How do you handle conditional rendering of multiple sibling components efficiently?](#question-10-how-do-you-handle-conditional-rendering-of-multiple-sibling-components-efficiently) |
| 11  | [How do you implement React Suspense for data fetching with Concurrent Mode?](#question-11-how-do-you-implement-react-suspense-for-data-fetching-with-concurrent-mode)                 |
| 12  | [How do you implement server-side rendering with data hydration?](#question-12-how-do-you-implement-server-side-rendering-with-data-hydration)                                         |
| 13  | [How do you implement React Query for caching, fetching, and updating server state?](#question-13-how-do-you-implement-react-query-for-caching-fetching-and-updating-server-state)     |
| 14  | [How do you implement optimistic updates with React Query or Redux?](#question-14-how-do-you-implement-optimistic-updates-with-react-query-or-redux)                                   |
| 15  | [How do you implement real-time notifications using WebSockets or SSE?](#question-15-how-do-you-implement-real-time-notifications-using-websockets-or-sse)                             |
| 16  | [How do you implement authentication with JWT tokens in React?](#question-16-how-do-you-implement-authentication-with-jwt-tokens-in-react)                                             |
| 17  | [How do you implement role-based access control (RBAC) with React Router?](#question-17-how-do-you-implement-role-based-access-control-rbac-with-react-router)                         |
| 18  | [How do you implement micro-frontends with React?](#question-18-how-do-you-implement-micro-frontends-with-react)                                                                       |
| 19  | [How do you integrate React with a GraphQL backend using Apollo Client?](#question-19-how-do-you-integrate-react-with-a-graphql-backend-using-apollo-client)                           |
| 20  | [How do you implement code-splitting at route level using React.lazy and Suspense?](#question-20-how-do-you-implement-code-splitting-at-route-level-using-reactlazy-and-suspense)      |

## Question 1. How do you implement virtualized lists for large datasets?

# Short answer

Virtualized lists (windowing) render only the items currently visible in the viewport (plus a small overscan buffer) instead of rendering the entire dataset. This significantly reduces DOM nodes, memory usage, and rendering time, making it possible to efficiently display tens of thousands of items. In modern React, libraries like **react-window**, **@tanstack/react-virtual**, and **react-virtuoso** are the preferred solutions.

---

# Explanation

Rendering 50,000 DOM elements at once is expensive because React must:

- Create thousands of component instances
- Diff all elements during reconciliation
- Allocate large amounts of memory
- Force the browser to perform expensive layout and painting

Virtualization solves this by rendering only what's visible.

Example:

```
Dataset: 100,000 items

Viewport:
+----------------------+
| Item 150            |
| Item 151            |
| Item 152            |
| Item 153            |
| Item 154            |
+----------------------+

DOM actually contains:
150-170 (overscan)

Instead of:
0-99,999
```

The scrollbar still represents the full list height, but React only mounts a small subset of rows.

### Popular virtualization libraries

| Library                 | Best for                         | Notes                      |
| ----------------------- | -------------------------------- | -------------------------- |
| react-window            | Simple fixed/variable size lists | Lightweight, widely used   |
| @tanstack/react-virtual | Modern, highly customizable      | Excellent React 18 support |
| react-virtuoso          | Dynamic heights, grouped lists   | Less manual configuration  |
| AG Grid                 | Enterprise data grids            | Includes virtualization    |

---

### React 18 considerations

Virtualization works well with React 18 because:

- Automatic batching reduces update overhead.
- Concurrent rendering keeps scrolling responsive.
- Only visible components participate in reconciliation.
- Less work means faster rendering during state updates.

---

### Fixed vs Variable height

**Fixed height**

```
Item height = 40px

scrollTop / 40
```

Very fast because offsets are computed mathematically.

**Variable height**

Rows may be:

```
Item A = 40px
Item B = 120px
Item C = 65px
```

Requires measuring or caching heights.

Libraries like:

- react-virtuoso
- @tanstack/react-virtual

handle this much better than react-window.

---

### Overscanning

Libraries render a few extra rows above and below the viewport.

```
Visible:
20-30

Rendered:
15-35
```

Benefits:

- smoother scrolling
- avoids blank areas
- reduces pop-in

Trade-off:

Higher overscan =

- more memory
- smoother UX

---

### Infinite scrolling + virtualization

These are often combined.

```
Scroll

↓

Reach end

↓

IntersectionObserver

↓

Fetch next page

↓

Append data

↓

Virtualizer updates
```

This allows millions of records while keeping only dozens mounted.

---

# Example

Using **Vite + React + TypeScript** with **@tanstack/react-virtual**.

## Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm i @tanstack/react-virtual
npm run dev
```

```tsx
import { useRef } from "react";
import { useVirtualizer } from "@tanstack/react-virtual";

const rows = Array.from({ length: 100000 }, (_, i) => `Item ${i + 1}`);

export default function VirtualList() {
  const parentRef = useRef<HTMLDivElement>(null);

  const virtualizer = useVirtualizer({
    count: rows.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 40,
    overscan: 5,
  });

  return (
    <div
      ref={parentRef}
      style={{
        height: "400px",
        overflow: "auto",
        border: "1px solid gray",
      }}
    >
      <div
        style={{
          height: virtualizer.getTotalSize(),
          position: "relative",
        }}
      >
        {virtualizer.getVirtualItems().map((item) => (
          <div
            key={item.key}
            style={{
              position: "absolute",
              top: 0,
              transform: `translateY(${item.start}px)`,
              height: `${item.size}px`,
              width: "100%",
              borderBottom: "1px solid #eee",
              display: "flex",
              alignItems: "center",
              paddingLeft: "12px",
            }}
          >
            {rows[item.index]}
          </div>
        ))}
      </div>
    </div>
  );
}
```

Only around 15–30 DOM nodes are mounted even though the dataset contains 100,000 items.

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript.

Avoid **Create React App (CRA)**, as it is deprecated. Vite provides a faster development server, native ES module support, and efficient production builds.

- **Bundler:** Vite (powered by Rollup for production)
- **Module system:** Prefer **ES Modules (ESM)**. CommonJS is mainly used in legacy Node.js ecosystems.
- **Alternatives:** Next.js (SSR, Server Components, App Router), Remix (nested routing, data loading), or Turbopack (Next.js development bundler).

Useful libraries:

```bash
npm i @tanstack/react-virtual
```

or

```bash
npm i react-window
```

or

```bash
npm i react-virtuoso
```

---

# Performance

Virtualization already removes the largest bottleneck, but combine it with other optimizations:

- Use **React.memo** for expensive row components.
- Memoize derived values with **useMemo**.
- Memoize event handlers with **useCallback** when passing them to memoized children.
- Keep row components lightweight and avoid unnecessary state inside each row.
- Use stable keys (e.g., unique IDs instead of array indices if items can reorder).
- Code split heavy pages with `React.lazy()` and `Suspense`.
- Cache fetched pages using libraries like TanStack Query to avoid unnecessary refetches.
- Use the React DevTools **Profiler** to identify unnecessary re-renders and validate that only visible rows update.

---

# Testing

For unit and integration testing, use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example command:

```bash
npx vitest
```

Test that:

- Visible rows render correctly.
- Scrolling updates the rendered window.
- Overscan behaves as expected.
- Newly loaded pages integrate correctly with virtualization.

Use **Playwright** for end-to-end testing of scrolling behavior and infinite loading.

---

# Ops & Deployment

- Add an **Error Boundary** around complex list views to isolate rendering failures.
- Log fetch and rendering errors using tools such as Sentry.
- Prefer **SSR** (e.g., Next.js) for SEO-sensitive pages, but note that virtualization primarily benefits client-side rendering after hydration.
- Use CDN caching for static assets and split large bundles with route-based lazy loading.
- Monitor bundle size with tools like `rollup-plugin-visualizer` or `source-map-explorer` and avoid shipping multiple virtualization libraries.

---

# Pitfalls

- **Don't render the full array and hide items with CSS**—the DOM cost remains.
- **Avoid unstable keys** (like array indices for reorderable data), which can cause incorrect state preservation and extra re-renders.
- **Be careful with dynamic row heights**; choose a library designed for measurement (e.g., `react-virtuoso` or `@tanstack/react-virtual`) or cache measured sizes to avoid layout thrashing.

## Question 2. How do you handle dynamic tabs with add/remove functionality?

# Short answer

Dynamic tabs are implemented by storing the tabs in React state (typically an array of objects), tracking the active tab by a stable identifier, and updating the state when tabs are added, removed, or reordered. Use unique IDs instead of array indexes, preserve component state where appropriate, and render only the active tab's content for better performance.

---

# Explanation

A dynamic tab system typically maintains two pieces of state:

- **tabs** – Array containing each tab's metadata (id, title, content).
- **activeTabId** – ID of the currently selected tab.

Example:

```text
tabs
[
  { id: "1", title: "Home" },
  { id: "2", title: "Users" },
  { id: "3", title: "Settings" }
]

activeTabId = "2"
```

When a user clicks **Add Tab**:

1. Generate a unique ID.
2. Append a new tab.
3. Make the new tab active.

When removing a tab:

- Remove it from the array.
- If the removed tab was active:
  - Activate the previous tab if available.
  - Otherwise activate the next tab.
  - If no tabs remain, set the active tab to `null`.

Example:

```text
Before

Home | Users | Settings
         ↑ Active

Delete Users

↓

Home | Settings
        ↑ Active
```

---

### Why use IDs instead of indexes?

Bad:

```tsx
key = { index };
```

Good:

```tsx
key={tab.id}
```

Stable IDs help React preserve component identity during reconciliation. Using indexes can lead to incorrect state retention when tabs are added, removed, or reordered.

---

### React 18 considerations

React 18's automatic batching combines multiple state updates (such as adding a tab and setting it active) into a single render, reducing unnecessary work.

For expensive tab content:

- Use `React.lazy()` for code splitting.
- Wrap inactive heavy components in lazy-loaded boundaries.
- Memoize tab content if props rarely change.

For applications with many open tabs (e.g., IDEs), consider keeping inactive tabs mounted to preserve local state, or persist state externally if you unmount inactive tabs.

---

### State management trade-offs

- **Local state (`useState`)**: Best for isolated tab components.
- **Context**: Useful when multiple components need access to the active tab.
- **Redux/Zustand**: Appropriate if tabs represent global application state (e.g., browser-like workspaces).

---

# Example

Using **Vite + React + TypeScript**.

## Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx
import { useState } from "react";

type Tab = {
  id: string;
  title: string;
  content: string;
};

export default function DynamicTabs() {
  const [tabs, setTabs] = useState<Tab[]>([
    { id: crypto.randomUUID(), title: "Home", content: "Home Content" },
  ]);

  const [activeId, setActiveId] = useState(tabs[0].id);

  const addTab = () => {
    const id = crypto.randomUUID();

    setTabs((prev) => [
      ...prev,
      {
        id,
        title: `Tab ${prev.length + 1}`,
        content: `Content ${prev.length + 1}`,
      },
    ]);

    setActiveId(id);
  };

  const removeTab = (id: string) => {
    setTabs((prev) => {
      const updated = prev.filter((tab) => tab.id !== id);

      if (activeId === id) {
        setActiveId(updated[updated.length - 1]?.id ?? "");
      }

      return updated;
    });
  };

  const activeTab = tabs.find((tab) => tab.id === activeId);

  return (
    <div>
      <button onClick={addTab}>Add Tab</button>

      <div style={{ display: "flex", gap: 8, marginTop: 12 }}>
        {tabs.map((tab) => (
          <div key={tab.id}>
            <button onClick={() => setActiveId(tab.id)}>{tab.title}</button>

            <button onClick={() => removeTab(tab.id)}>×</button>
          </div>
        ))}
      </div>

      <div style={{ marginTop: 20 }}>
        {activeTab?.content ?? "No tabs open"}
      </div>
    </div>
  );
}
```

This implementation:

- Uses stable IDs.
- Supports add/remove functionality.
- Automatically selects a valid active tab after deletion.
- Minimizes unnecessary re-renders through React's state batching.

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript.

Avoid **Create React App (CRA)**, as it is deprecated.

- **Bundler:** Vite (fast HMR, Rollup for production builds).
- **Module system:** Use **ES Modules (ESM)**. CommonJS is primarily for legacy Node.js environments.
- **Framework alternatives:** Next.js (SSR, Server Components, App Router), Remix (nested routing and data loading), or Turbopack (Next.js development).

If your application requires accessible, production-ready tabs, consider headless component libraries (e.g., Radix UI or Headless UI) and style them according to your design system.

---

# Performance

- Wrap heavy tab panels with `React.memo` when props are stable.
- Use `useMemo` for expensive derived values (e.g., filtered tab lists).
- Use `useCallback` for handlers passed to memoized children.
- Lazy-load expensive tab content with `React.lazy()` and `Suspense`.
- Profile tab switching using the React DevTools **Profiler**.
- For dozens or hundreds of tabs, virtualize the tab header list and cache inactive tab data if reopening should be instant.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example tests should verify:

- Adding a tab updates the UI and activates the new tab.
- Removing the active tab selects the correct fallback tab.
- Removing the last tab displays the empty state.
- Clicking tab headers changes the active content.

For end-to-end testing, use **Playwright** to validate complete user interactions such as opening, closing, and switching between tabs.

---

# Ops & Deployment

- Wrap tab panels with an **Error Boundary** if they render independently loaded modules.
- Log tab-related errors and unexpected state transitions using tools like Sentry.
- Consider **SSR** (e.g., Next.js) for initial page load, while handling dynamic tab interactions on the client.
- Keep bundles small by code-splitting rarely visited tab content.
- Serve static assets via a CDN and monitor bundle size with tools such as `rollup-plugin-visualizer`.

---

# Pitfalls

- **Don't use array indexes as React keys** for dynamic tabs; use stable unique IDs.
- **Always update the active tab after removal** to avoid referencing a deleted tab.
- **Avoid mutating the tabs array directly**; always create a new array to preserve React's immutable update pattern.

## Question 3. How do you implement a toast notification system globally?

# Short answer

A global toast notification system is typically implemented using **React Context** (or a lightweight state library like Zustand) with a **ToastProvider** that manages toast state and exposes methods such as `success()`, `error()`, `info()`, and `remove()`. The provider is mounted once near the application root, allowing any component to trigger notifications without prop drilling.

---

# Explanation

A production-ready toast system consists of three parts:

1. **Toast Context**
   - Exposes functions like `showToast()`, `success()`, and `removeToast()`.
   - Accessible from any component through a custom hook.

2. **Toast Provider**
   - Stores an array of active toasts.
   - Handles auto-dismiss timers.
   - Renders the toast container.

3. **Toast Component**
   - Displays individual notifications.
   - Supports variants (success, error, warning, info).
   - Can include actions, icons, progress bars, and animations.

Architecture:

```text
App
│
├── ToastProvider
│     │
│     ├── ToastContainer
│     │      ├── Toast #1
│     │      ├── Toast #2
│     │      └── Toast #3
│     │
│     └── Context API
│
├── Dashboard
├── Users
├── Orders
└── Settings

Any component

↓

useToast().success("Saved!")
```

### Toast lifecycle

```text
User clicks Save

↓

API succeeds

↓

showToast({
  type: "success",
  message: "Profile updated"
})

↓

Toast appears

↓

Auto-dismiss after 3 seconds

↓

Removed from state
```

### React 18 considerations

React 18 improves toast systems by:

- **Automatic batching** groups multiple toast-related state updates into a single render.
- **Concurrent rendering** helps keep the UI responsive even when notifications are triggered during expensive updates.
- Toasts are typically rendered through a **Portal**, preventing layout issues and avoiding interference with the main application tree.

### State management trade-offs

| Approach                                                   | Best for          | Pros               | Cons                                 |
| ---------------------------------------------------------- | ----------------- | ------------------ | ------------------------------------ |
| Context API                                                | Most apps         | Simple, built-in   | Provider re-renders unless optimized |
| Zustand                                                    | Medium/Large apps | Minimal re-renders | Additional dependency                |
| Redux Toolkit                                              | Enterprise apps   | Centralized state  | More boilerplate                     |
| Third-party libraries (React Hot Toast, Sonner, Notistack) | Production apps   | Feature-rich       | Less customization of internals      |

---

# Example

Using **Vite + React + TypeScript**.

## Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `ToastProvider.tsx`

```tsx
import { createContext, useContext, useState, ReactNode } from "react";

type Toast = {
  id: string;
  message: string;
};

type ToastContextType = {
  showToast: (message: string) => void;
};

const ToastContext = createContext<ToastContextType | null>(null);

export function ToastProvider({ children }: { children: ReactNode }) {
  const [toasts, setToasts] = useState<Toast[]>([]);

  const showToast = (message: string) => {
    const id = crypto.randomUUID();

    setToasts((prev) => [...prev, { id, message }]);

    setTimeout(() => {
      setToasts((prev) => prev.filter((t) => t.id !== id));
    }, 3000);
  };

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
              marginBottom: 8,
              padding: 12,
              background: "#333",
              color: "#fff",
              borderRadius: 6,
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
import { useToast } from "./ToastProvider";

export default function SaveButton() {
  const { showToast } = useToast();

  return (
    <button onClick={() => showToast("Profile saved successfully!")}>
      Save
    </button>
  );
}
```

Wrap your application once:

```tsx
<ToastProvider>
  <App />
</ToastProvider>
```

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript.

Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (fast HMR, Rollup for production builds).
- **Module system:** Prefer **ES Modules (ESM)**. CommonJS is mainly for legacy Node.js environments.
- **Framework alternatives:** Next.js (App Router, SSR, Server Components) or Remix for full-stack routing and data loading.

For production applications, well-maintained libraries such as **React Hot Toast**, **Sonner**, or **Notistack** provide accessibility, animations, promise-based APIs, and portal support out of the box.

---

# Performance

- Render the toast container once at the application root.
- Use **React Portal** to avoid layout shifts and z-index conflicts.
- Memoize toast components with `React.memo` if they receive stable props.
- Memoize context values with `useMemo` to avoid unnecessary consumer re-renders.
- Memoize callbacks with `useCallback` when passing them to memoized children.
- Use the React DevTools **Profiler** to verify that only the toast container re-renders when notifications change.
- Code-split rarely used notification-related UI (e.g., detailed error dialogs) with `React.lazy()` and `Suspense`.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example test scenarios:

- Toast appears after calling `showToast()`.
- Toast automatically disappears after the timeout.
- Multiple toasts stack correctly.
- Manual dismissal removes only the selected toast.
- Different variants (success, error, warning) render the correct styling and accessibility attributes.

Use **Playwright** for end-to-end testing to verify user-visible notifications during real workflows.

---

# Ops & Deployment

- Render toasts through a **React Portal** attached to `document.body`.
- Wrap the application in an **Error Boundary** so uncaught errors can trigger a global error toast while preventing the UI from crashing completely.
- Integrate logging tools such as Sentry or LogRocket to capture errors alongside toast notifications.
- In SSR frameworks (e.g., Next.js), ensure the toast provider is a client component (`"use client"` when required) since notifications are interactive.
- Keep the notification system lightweight to avoid increasing the initial bundle size.

---

# Pitfalls

- **Don't create multiple `ToastProvider` instances** unless separate notification scopes are intentionally required.
- **Always clear auto-dismiss timers** if a toast is manually removed or the provider unmounts to prevent memory leaks.
- **Ensure accessibility** by using appropriate ARIA roles (e.g., `role="status"` or `role="alert"`), keyboard support, and sufficient color contrast.

## Question 4. How do you implement a live search component that fetches results dynamically?

# Short answer

A live search component fetches results as the user types by combining a **controlled input**, **debouncing**, **asynchronous API requests**, **request cancellation** (using `AbortController`), and **loading/error states**. In production, use a data-fetching library like **TanStack Query** for caching and request deduplication, and debounce input to avoid excessive API calls.

---

# Explanation

A production-ready live search flow looks like this:

```text
User types

↓

Update input state

↓

Debounce (300ms)

↓

Cancel previous request

↓

Fetch latest results

↓

Display loading

↓

Render results

↓

Cache response
```

## Component architecture

A typical implementation has:

- **SearchInput** – Controlled input component.
- **useDebounce** – Delays API requests until the user pauses typing.
- **SearchResults** – Displays loading, errors, empty state, or results.
- **API layer** – Encapsulates fetch logic.
- **Cache layer** – TanStack Query or SWR for caching and deduplication.

```text
App
│
├── SearchBox
│      │
│      ├── Input
│      ├── Debounce
│      ├── API
│      └── Results
│
└── Search Service
```

---

## Why debounce?

Without debounce:

```text
User types:
R
Re
Rea
Reac
React

↓

5 API calls
```

With a **300ms debounce**:

```text
User types:
React

↓

1 API call
```

This reduces:

- Server load
- Network traffic
- UI flickering

---

## Avoid race conditions

A common issue:

```text
Search "Re"

↓

Request A

Search "React"

↓

Request B

↓

B finishes first

↓

A finishes later

↓

Old results overwrite new results ❌
```

Solution:

- `AbortController`
- Request IDs
- TanStack Query cancellation

---

## React 18 considerations

React 18 introduces features that improve live search responsiveness:

### `useDeferredValue`

Keeps typing responsive while rendering expensive search results.

```tsx
const deferredQuery = useDeferredValue(query);
```

---

### `useTransition`

Marks result updates as non-urgent.

```tsx
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setResults(data);
});
```

Typing stays responsive even if rendering is expensive.

---

### Automatic batching

Multiple updates like:

```tsx
setLoading(true);
setResults(data);
setLoading(false);
```

are batched into fewer renders.

---

# Example

Using **Vite + React + TypeScript**.

## Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function LiveSearch() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState<User[]>([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    if (!query.trim()) {
      setResults([]);
      return;
    }

    const controller = new AbortController();

    const timer = setTimeout(async () => {
      try {
        setLoading(true);

        const res = await fetch(
          `https://jsonplaceholder.typicode.com/users?name_like=${query}`,
          {
            signal: controller.signal,
          },
        );

        const data = await res.json();
        setResults(data);
      } catch (error) {
        if ((error as Error).name !== "AbortError") {
          console.error(error);
        }
      } finally {
        setLoading(false);
      }
    }, 300);

    return () => {
      clearTimeout(timer);
      controller.abort();
    };
  }, [query]);

  return (
    <div>
      <input
        placeholder="Search users..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />

      {loading && <p>Loading...</p>}

      {!loading && results.length === 0 && query && <p>No results found.</p>}

      <ul>
        {results.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

This example demonstrates:

- Controlled input
- Debounced API requests
- Request cancellation
- Loading state
- Empty state
- Cleanup on unmount

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**.

Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (fast HMR and Rollup for production builds).
- **Module system:** Prefer **ES Modules (ESM)**. CommonJS is primarily for legacy Node.js environments.
- **Framework alternatives:** Next.js (App Router, SSR, Server Components) or Remix for data-driven applications.

For production applications, consider:

```bash
npm i @tanstack/react-query
```

Benefits:

- Request caching
- Background refetching
- Automatic deduplication
- Retry handling
- Request cancellation
- Stale-while-revalidate caching

---

# Performance

Optimize live search by:

- Debouncing input (200–500 ms depending on UX requirements).
- Using `AbortController` to cancel stale requests.
- Applying `useDeferredValue` to keep typing responsive during expensive renders.
- Using `useTransition` for non-urgent result updates.
- Memoizing expensive result components with `React.memo`.
- Using `useMemo` for derived or filtered data.
- Memoizing callbacks with `useCallback` when passing them to memoized children.
- Virtualizing long result lists using libraries like `@tanstack/react-virtual`.
- Profiling with the React DevTools **Profiler** to identify unnecessary re-renders.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Test scenarios:

- Debounce delays API requests.
- Only the latest request updates the UI.
- Previous requests are cancelled.
- Loading indicator appears while fetching.
- Empty state is shown when no results are returned.
- Errors are handled gracefully.

Use **Playwright** for end-to-end tests covering complete search workflows and keyboard interactions.

---

# Ops & Deployment

- Centralize API logic in a dedicated service layer for reuse and easier testing.
- Log API failures using tools such as Sentry or LogRocket.
- Add an **Error Boundary** around the search page to isolate rendering failures (note that asynchronous fetch errors should still be handled in the component or data-fetching layer).
- For SSR frameworks like Next.js, consider server-rendering the initial page while performing live searches on the client.
- Use CDN caching for static assets and HTTP caching (e.g., `ETag`, `Cache-Control`) where appropriate for search endpoints.

---

# Pitfalls

- **Don't trigger an API request on every keystroke**—always debounce or throttle user input.
- **Don't ignore stale requests**—cancel them with `AbortController` or use a library that manages request cancellation.
- **Don't forget loading, empty, and error states**—they are essential for a polished user experience.

## Question 5. How do you create a reusable card component with dynamic content?

# Short answer

A reusable card component is created by designing it to be **composable** rather than tightly coupled to a specific use case. Instead of hardcoding content, accept props such as `title`, `subtitle`, `image`, and use `children` for flexible content. Expose optional slots (header, body, footer) so the same component can display products, users, blog posts, dashboards, or any custom UI.

---

# Explanation

A reusable card should follow these principles:

- **Composition over configuration** – Use `children` for arbitrary content instead of creating dozens of props.
- **Single Responsibility Principle** – The card handles layout and styling, not business logic.
- **Type safety** – Use TypeScript interfaces for predictable APIs.
- **Accessibility** – Use semantic HTML (`article`, `header`, `footer`) and meaningful headings.
- **Customizable** – Support optional actions, variants, loading states, and styling overrides.

Typical architecture:

```text
App
│
├── UserCard
│      └── Card
│
├── ProductCard
│      └── Card
│
├── BlogCard
│      └── Card
│
└── DashboardCard
       └── Card
```

Instead of creating separate layouts for each use case, all specialized cards reuse the same base component.

---

### Component API

A flexible API might look like:

```ts
type CardProps = {
  title?: string;
  subtitle?: string;
  footer?: React.ReactNode;
  children: React.ReactNode;
};
```

Using `children` allows any JSX to be rendered:

```tsx
<Card title="Product">
  <img />
  <p>Description</p>
  <button>Buy</button>
</Card>
```

This is more scalable than defining props like:

```tsx
<Card
  image=""
  description=""
  buttonText=""
  price=""
  rating=""
  stock=""
  ...
/>
```

---

### React 18 considerations

React 18 doesn't change how cards are built, but it improves rendering performance:

- **Automatic batching** reduces renders when multiple state updates affect cards.
- **Concurrent rendering** keeps the UI responsive when rendering many cards.
- **Server Components** (Next.js App Router) can render static card content on the server while keeping interactive elements as client components.

---

### State management trade-offs

- **Stateless cards** are preferred—they simply render props.
- If a card has local UI (e.g., "Expand", "Like"), manage that with `useState`.
- Shared card state (favorites, selection) should be managed via Context, Zustand, Redux Toolkit, or TanStack Query depending on the application's architecture.

---

# Example

Using **Vite + React + TypeScript**.

## Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `Card.tsx`

```tsx
import { ReactNode } from "react";

type CardProps = {
  title?: string;
  subtitle?: string;
  footer?: ReactNode;
  children: ReactNode;
};

export function Card({ title, subtitle, footer, children }: CardProps) {
  return (
    <article
      style={{
        border: "1px solid #ddd",
        borderRadius: 8,
        padding: 16,
        maxWidth: 320,
      }}
    >
      {(title || subtitle) && (
        <header>
          {title && <h2>{title}</h2>}
          {subtitle && <p>{subtitle}</p>}
        </header>
      )}

      <section>{children}</section>

      {footer && <footer>{footer}</footer>}
    </article>
  );
}
```

### Usage

```tsx
import { Card } from "./Card";

export default function App() {
  return (
    <Card
      title="React Handbook"
      subtitle="Frontend Guide"
      footer={<button>Read More</button>}
    >
      <p>
        Learn modern React with Hooks, TypeScript, and performance optimization.
      </p>
    </Card>
  );
}
```

This implementation demonstrates:

- Reusable layout
- Dynamic content via `children`
- Optional header and footer
- Strong typing with TypeScript

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript.

Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (fast development server with HMR, Rollup for production builds).
- **Module system:** Prefer **ES Modules (ESM)**. CommonJS is mainly used for legacy Node.js projects.
- **Alternatives:** Next.js (SSR, App Router, Server Components), Remix (nested routing and data loading), or Turbopack for Next.js development.

For larger design systems, consider pairing reusable cards with component libraries like Material UI, Chakra UI, or Radix UI primitives.

---

# Performance

- Wrap expensive card components with `React.memo` if their props rarely change.
- Use `useMemo` for expensive derived values (e.g., formatting large datasets).
- Use `useCallback` when passing event handlers to memoized child components.
- Lazy-load heavy card content (e.g., charts, media) with `React.lazy()` and `Suspense`.
- Virtualize large card grids using libraries such as `@tanstack/react-virtual` or `react-window`.
- Use the React DevTools **Profiler** to verify that only cards with changed props re-render.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example tests should verify:

- Header renders only when `title` or `subtitle` is provided.
- `children` are rendered correctly.
- Footer is optional.
- User interactions (e.g., footer button click) work as expected.

For end-to-end testing, use **Playwright** to validate card rendering and interactions in real user flows.

---

# Ops & Deployment

- Keep the base card component presentational and free of business logic.
- Wrap interactive card sections with an **Error Boundary** if they load remote or complex components.
- Log rendering or interaction errors using tools like Sentry.
- In Next.js, render static card data on the server when possible and hydrate only interactive elements.
- Minimize bundle size by sharing the base card component across the application and code-splitting feature-specific content.

---

# Pitfalls

- **Don't overload the component with dozens of props**—prefer `children` and composable slots.
- **Avoid embedding business logic** inside the card; keep it reusable and presentation-focused.
- **Use semantic HTML** (`article`, `header`, `section`, `footer`) to improve accessibility and maintainability.

## Question 6. How do you implement keyboard navigation for interactive elements?

## Question 7. How do you implement image preview before upload?

## Question 8. How do you debounce window scroll events in React?

## Question 9. How do you implement form reset after submission in functional components?

## Question 10. How do you handle conditional rendering of multiple sibling components efficiently?

## Question 11. How do you implement React Suspense for data fetching with Concurrent Mode?

## Question 12. How do you implement server-side rendering with data hydration?

## Question 13. How do you implement React Query for caching, fetching, and updating server state?

## Question 14. How do you implement optimistic updates with React Query or Redux?

## Question 15. How do you implement real-time notifications using WebSockets or SSE?

## Question 16. How do you implement authentication with JWT tokens in React?

## Question 17. How do you implement role-based access control (RBAC) with React Router?

## Question 18. How do you implement micro-frontends with React?

## Question 19. How do you integrate React with a GraphQL backend using Apollo Client?

## Question 20. How do you implement code-splitting at route level using React.lazy and Suspense?
