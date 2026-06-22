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

## Question 3. How do you handle conditional rendering based on API response?

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
