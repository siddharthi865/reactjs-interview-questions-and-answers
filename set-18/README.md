# Set 18

| #   | Question                                                                                                                                                                      |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement infinite scrolling with IntersectionObserver?](#question-1-how-do-you-implement-infinite-scrolling-with-intersectionobserver)                           |
| 2   | [How do you implement a debounce function in React?](#question-2-how-do-you-implement-a-debounce-function-in-react)                                                           |
| 3   | [How do you memoize a component that receives multiple props?](#question-3-how-do-you-memoize-a-component-that-receives-multiple-props)                                       |
| 4   | [How do you implement a multi-select dropdown with checkboxes?](#question-4-how-do-you-implement-a-multi-select-dropdown-with-checkboxes)                                     |
| 5   | [How do you implement conditional animations in React?](#question-5-how-do-you-implement-conditional-animations-in-react)                                                     |
| 6   | [How do you share state between deeply nested components without props drilling?](#question-6-how-do-you-share-state-between-deeply-nested-components-without-props-drilling) |
| 7   | [How do you implement drag-and-drop reordering of a list?](#question-7-how-do-you-implement-drag-and-drop-reordering-of-a-list)                                               |
| 8   | [How do you create a custom hook for window resize events?](#question-8-how-do-you-create-a-custom-hook-for-window-resize-events)                                             |
| 9   | [How do you prevent unnecessary re-rendering of components with useMemo?](#question-9-how-do-you-prevent-unnecessary-re-rendering-of-components-with-usememo)                 |
| 10  | [How do you implement tab navigation using context?](#question-10-how-do-you-implement-tab-navigation-using-context)                                                          |
| 11  | [How do you integrate a charting library like Chart.js in React?](#question-11-how-do-you-integrate-a-charting-library-like-chartjs-in-react)                                 |
| 12  | [How do you implement a responsive modal dialog?](#question-12-how-do-you-implement-a-responsive-modal-dialog)                                                                |
| 13  | [How do you implement a multi-step form with validation?](#question-13-how-do-you-implement-a-multi-step-form-with-validation)                                                |
| 14  | [How do you implement dynamic import of components based on route?](#question-14-how-do-you-implement-dynamic-import-of-components-based-on-route)                            |
| 15  | [How do you cancel ongoing fetch requests when a component unmounts?](#question-15-how-do-you-cancel-ongoing-fetch-requests-when-a-component-unmounts)                        |
| 16  | [How do you implement a global error handler for API calls?](#question-16-how-do-you-implement-a-global-error-handler-for-api-calls)                                          |
| 17  | [How do you implement theme toggling with localStorage persistence?](#question-17-how-do-you-implement-theme-toggling-with-localstorage-persistence)                          |
| 18  | [How do you optimize images for better performance in React apps?](#question-18-how-do-you-optimize-images-for-better-performance-in-react-apps)                              |
| 19  | [How do you implement a copy-to-clipboard functionality?](#question-19-how-do-you-implement-a-copy-to-clipboard-functionality)                                                |
| 20  | [How do you integrate a third-party date picker component?](#question-20-how-do-you-integrate-a-third-party-date-picker-component)                                            |

## Question 1. How do you implement infinite scrolling with IntersectionObserver?

# Short answer

Use the browser's **IntersectionObserver API** to observe a sentinel element (usually placed at the bottom of a list). When the sentinel enters the viewport, trigger the next page fetch. This approach is more efficient than listening to `scroll` events because the browser optimizes visibility detection.

---

# Explanation

Infinite scrolling loads additional content as the user approaches the end of the currently rendered list.

The recommended React approach is:

1. Render a list of items.
2. Place a small empty `<div>` (sentinel) after the list.
3. Create an `IntersectionObserver` inside `useEffect`.
4. When the sentinel becomes visible (`isIntersecting === true`):
   - Check if you're already loading.
   - Check if more pages exist.
   - Fetch the next page.

5. Disconnect the observer during cleanup.

### Why IntersectionObserver?

Compared to scroll listeners:

- Doesn't fire on every scroll event.
- Browser optimizes intersection calculations.
- Less JavaScript execution.
- Better battery and CPU usage.
- Works well with lazy loading and virtualization.

### React 18 considerations

- Automatic batching reduces unnecessary re-renders when multiple state updates occur after an API response.
- Keep loading state separate to prevent duplicate requests.
- Prefer functional state updates:

  ```tsx
  setPage((p) => p + 1);
  ```

- Consider `startTransition()` if appending thousands of items causes noticeable UI delays.

### Component architecture

A production architecture often looks like:

```
Page
 ├── Search Filters
 ├── InfiniteList
 │     ├── Item
 │     ├── Item
 │     ├── Item
 │     └── Sentinel
 └── API Layer
```

The observer should only know **when** to load.

The API layer should know **how** to load.

---

# Example

**Scaffold (Vite + React + TypeScript)**

```bash
npm create vite@latest infinite-scroll-demo -- --template react-ts
cd infinite-scroll-demo
npm install
npm run dev
```

```tsx
import { useEffect, useRef, useState } from "react";

type Item = {
  id: number;
  title: string;
};

export default function InfiniteList() {
  const [items, setItems] = useState<Item[]>([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  const loaderRef = useRef<HTMLDivElement | null>(null);

  useEffect(() => {
    async function fetchItems() {
      setLoading(true);

      const res = await fetch(
        `https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`,
      );

      const data: Item[] = await res.json();

      setItems((prev) => [...prev, ...data]);

      if (data.length < 10) {
        setHasMore(false);
      }

      setLoading(false);
    }

    fetchItems();
  }, [page]);

  useEffect(() => {
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting && !loading && hasMore) {
          setPage((prev) => prev + 1);
        }
      },
      {
        root: null,
        threshold: 1,
        rootMargin: "100px",
      },
    );

    const node = loaderRef.current;

    if (node) observer.observe(node);

    return () => observer.disconnect();
  }, [loading, hasMore]);

  return (
    <>
      {items.map((item) => (
        <div
          key={item.id}
          style={{
            padding: 16,
            borderBottom: "1px solid #ddd",
          }}
        >
          {item.title}
        </div>
      ))}

      {loading && <p>Loading...</p>}

      <div ref={loaderRef} style={{ height: 20 }} />
    </>
  );
}
```

This implementation:

- Loads the next page automatically.
- Prevents duplicate requests using `loading`.
- Stops requesting when `hasMore` becomes `false`.
- Cleans up the observer on unmount.

---

# Tooling & Setup

Avoid **Create React App (CRA)** as it is deprecated.

Recommended stacks:

- **Vite** – Fast dev server, native ESM, ideal for SPAs.
- **Next.js (App Router)** – SSR, Server Components, streaming, SEO.
- **Remix** – Excellent data loading and nested routing.

### ESM vs CommonJS

- Modern React projects use **ES Modules (ESM)**:

  ```ts
  import { useEffect } from "react";
  ```

- Vite and modern bundlers optimize ESM for faster startup and tree shaking.
- CommonJS (`require`) is primarily used in older Node.js projects.

---

# Performance

Infinite scrolling can become slow as the DOM grows.

### Use virtualization

Combine `IntersectionObserver` with:

- `react-window`
- `react-virtualized`
- `@tanstack/react-virtual`

This renders only visible rows while still loading additional pages.

### Memoization

- `React.memo` for expensive list items.
- `useMemo` for derived values.
- `useCallback` for stable callbacks passed to children.

### Code splitting

```tsx
const ProductDetails = React.lazy(() => import("./ProductDetails"));
```

Load large components only when needed.

### React Profiler

Use React DevTools Profiler to measure:

- unnecessary re-renders,
- commit durations,
- rendering bottlenecks,
- interaction traces.

### Caching

Instead of manually managing pages, consider:

- TanStack Query (`useInfiniteQuery`)
- SWR

These libraries provide:

- caching,
- request deduplication,
- background refetching,
- pagination helpers,
- retry logic.

---

# Testing

Use **Vitest** with **React Testing Library** for unit/integration tests and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test idea:

```tsx
it("loads more items when sentinel intersects", () => {
  // Mock IntersectionObserver and assert that
  // the next page fetch is triggered.
});
```

For E2E, use Playwright to verify that scrolling loads additional items without duplicates.

---

# Ops & Deployment

- Wrap list pages in an Error Boundary to handle fetch failures gracefully.
- Log API errors and pagination metrics with tools like Sentry or OpenTelemetry.
- Use SSR/ISR (e.g., Next.js) for the initial page to improve SEO, then hydrate and continue infinite loading on the client.
- Keep bundle size small with route-based code splitting and lazy-loaded components.
- Serve static assets through a CDN and enable compression (Brotli/Gzip). Cache paginated API responses where appropriate to reduce latency.

---

# Pitfalls

- **Observing the wrong element:** Observe a dedicated sentinel, not every list item, unless implementing specialized behavior.
- **Duplicate fetches:** Always guard with `loading` (or request deduplication) to avoid triggering multiple concurrent page loads.
- **Growing DOM indefinitely:** Without list virtualization, thousands of rendered items will eventually degrade scrolling and rendering performance.

## Question 2. How do you implement a debounce function in React?

# Short answer

A **debounce function** delays executing a function until a specified period has passed since the last invocation. In React, it's commonly used for **search inputs, API requests, validation, filtering, and resize events** to reduce unnecessary work and improve performance.

---

# Explanation

Without debouncing, every keystroke can trigger:

- API requests
- Expensive filtering
- Re-rendering
- Validation
- Analytics events

For example, typing **"react"** generates six input events:

```
r
re
rea
reac
react
```

Without debounce:

```
6 API requests
```

With a **500ms debounce**:

```
User types...
(wait 500ms)

1 API request
```

This significantly reduces network traffic and improves user experience.

## Common implementation approaches

### 1. Debouncing a value (recommended)

Create a reusable `useDebounce` hook that returns a delayed version of a state value. This is the most "React-like" solution because components react to state changes rather than imperative timers.

### 2. Debouncing a callback

Wrap an event handler with a debounce function (or a utility such as Lodash's `debounce`) when you need to delay executing a specific callback.

## React 18 considerations

- **Automatic batching** groups state updates triggered after the debounced callback, reducing unnecessary renders.
- Always clear pending timers in `useEffect` cleanup to avoid updating state after unmount.
- For expensive filtering of already-loaded data, consider combining debouncing with `useDeferredValue` or `startTransition` to keep typing responsive.

---

# Example

**Scaffold (Vite + React + TypeScript)**

```bash
npm create vite@latest debounce-demo -- --template react-ts
cd debounce-demo
npm install
npm run dev
```

### Reusable `useDebounce` hook

```tsx
import { useEffect, useState } from "react";

export function useDebounce<T>(value: T, delay = 500): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = window.setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => window.clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
```

### Using the hook

```tsx
import { useEffect, useState } from "react";
import { useDebounce } from "./useDebounce";

export default function Search() {
  const [query, setQuery] = useState("");

  const debouncedQuery = useDebounce(query, 500);

  useEffect(() => {
    if (!debouncedQuery) return;

    console.log("Fetching:", debouncedQuery);

    // fetch(`/api/search?q=${encodeURIComponent(debouncedQuery)}`);
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

This implementation:

- Updates the input immediately.
- Waits 500ms after the user stops typing.
- Performs only one API call for a burst of keystrokes.
- Cleans up pending timers to avoid memory leaks.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stacks:

- **Vite** – Fast development server, native ESM support, excellent for SPAs.
- **Next.js** – Supports SSR, Server Components, and routing for production-grade applications.
- **Remix** – Great for data loading and nested routing.

### ESM vs CommonJS

Modern React projects use **ES Modules (ESM)**:

```ts
import { useEffect } from "react";
```

Benefits include:

- Tree shaking
- Faster dev server startup
- Native browser module support
- Better bundler optimization

---

# Performance

For production applications:

- Debounce **network requests**, not controlled input state, so typing stays responsive.
- Use `React.memo` to prevent unnecessary child re-renders.
- Memoize expensive computations with `useMemo`.
- Memoize callbacks passed to children with `useCallback`.
- Use `React.lazy` and dynamic imports for code splitting.
- Profile with the React DevTools Profiler to verify that debouncing reduces unnecessary work.
- For server-state management, libraries like TanStack Query can complement debouncing with caching, request deduplication, and stale-data management.

---

# Testing

Use **Vitest** with **React Testing Library** for unit/integration tests and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test idea:

```tsx
it("calls the search API only once after the debounce delay", async () => {
  // Use fake timers to advance time and verify
  // the callback runs only after the configured delay.
});
```

Use fake timers (`vi.useFakeTimers()`) to make debounce tests deterministic.

---

# Ops & Deployment

- Log search latency and API errors with observability tools (e.g., Sentry or OpenTelemetry).
- Cancel stale requests using `AbortController` when a newer debounced search starts.
- If using SSR (e.g., Next.js), debounce only client-side interactions.
- Monitor bundle size; if using a utility library, prefer importing only the required function (or implement a small custom hook) to minimize bundle impact.
- Cache search responses when appropriate to reduce repeated network requests.

---

# Pitfalls

- **Debouncing state updates:** Keep the input state immediate; debounce the side effect (e.g., API call) or derived value instead.
- **Not clearing timers:** Always clean up `setTimeout` in `useEffect` to prevent memory leaks or updates after unmount.
- **Ignoring stale requests:** Debouncing limits request frequency but doesn't cancel previous requests—use `AbortController` or a data-fetching library to avoid race conditions.

## Question 3. How do you memoize a component that receives multiple props?

# Short answer

Use **`React.memo`** to memoize the component. By default, `React.memo` performs a **shallow comparison** of all props and skips re-rendering if none of the prop references or primitive values have changed. When passing objects, arrays, or functions, stabilize them with `useMemo` and `useCallback`, or provide a custom comparison function if necessary.

---

# Explanation

When a parent component re-renders, all of its children normally re-render as well, even if their props haven't changed.

`React.memo` optimizes this by memoizing the rendered output:

- If **all props are shallowly equal**, React skips rendering the component.
- If **any prop changes**, the component re-renders.

## Example with multiple props

Suppose a component receives:

- `title` (string)
- `price` (number)
- `inStock` (boolean)
- `onAddToCart` (function)
- `metadata` (object)

```text
Parent
 ├── title ✓
 ├── price ✓
 ├── inStock ✓
 ├── onAddToCart ⚠️
 └── metadata ⚠️
            │
            ▼
      React.memo(ProductCard)
```

If `onAddToCart` or `metadata` are recreated on every render, `React.memo` will detect different references and the child will still re-render.

### Stabilize reference props

Use:

- `useCallback` → functions
- `useMemo` → objects and arrays

This allows `React.memo` to be effective.

### React 18 considerations

- React 18's automatic batching reduces unnecessary renders from grouped state updates.
- `React.memo` only helps when rendering the component is more expensive than comparing props.
- Measure performance with the React DevTools Profiler before adding memoization everywhere.

---

# Example

**Scaffold (Vite + React + TypeScript)**

```bash
npm create vite@latest memo-demo -- --template react-ts
cd memo-demo
npm install
npm run dev
```

```tsx
import { memo, useCallback, useMemo, useState } from "react";

type ProductCardProps = {
  title: string;
  price: number;
  inStock: boolean;
  metadata: { category: string };
  onAddToCart: () => void;
};

const ProductCard = memo(function ProductCard({
  title,
  price,
  inStock,
  metadata,
  onAddToCart,
}: ProductCardProps) {
  console.log("ProductCard rendered");

  return (
    <div>
      <h3>{title}</h3>
      <p>${price}</p>
      <p>{metadata.category}</p>
      <p>{inStock ? "In Stock" : "Out of Stock"}</p>
      <button onClick={onAddToCart}>Add to Cart</button>
    </div>
  );
});

export default function App() {
  const [count, setCount] = useState(0);

  const metadata = useMemo(() => ({ category: "Electronics" }), []);

  const handleAddToCart = useCallback(() => {
    console.log("Added");
  }, []);

  return (
    <>
      <button onClick={() => setCount((c) => c + 1)}>Counter: {count}</button>

      <ProductCard
        title="Laptop"
        price={999}
        inStock
        metadata={metadata}
        onAddToCart={handleAddToCart}
      />
    </>
  );
}
```

Clicking the counter button re-renders `App`, but `ProductCard` does **not** re-render because all its props remain shallowly equal.

### Custom comparison (only when necessary)

If a prop changes reference frequently but only part of it matters for rendering, you can pass a custom comparator:

```tsx
const ProductCard = memo(
  function ProductCard(props: ProductCardProps) {
    // ...
  },
  (prev, next) =>
    prev.title === next.title &&
    prev.price === next.price &&
    prev.inStock === next.inStock &&
    prev.metadata.category === next.metadata.category,
);
```

Use custom comparison sparingly, as it also has a runtime cost.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stacks:

- **Vite** – Fast HMR, native ESM, excellent for SPAs.
- **Next.js** – App Router, SSR, Server Components, and streaming.
- **Remix** – Strong data-loading model and nested routing.

### ESM vs CommonJS

Modern React projects use **ES Modules (ESM)**:

```ts
import { memo, useMemo, useCallback } from "react";
```

ESM enables tree shaking and faster development with modern bundlers such as Vite.

---

# Performance

- Use `React.memo` only for components with meaningful render cost or frequent parent updates.
- Stabilize object, array, and function props using `useMemo` and `useCallback`.
- Avoid creating inline objects/functions in JSX when passing them to memoized children.
- Use `React.lazy` and dynamic imports for code splitting.
- Profile with the React DevTools Profiler to verify that memoization reduces render time rather than adding unnecessary comparison overhead.
- For large lists, combine `React.memo` with virtualization libraries like `@tanstack/react-virtual` or `react-window`.

---

# Testing

Use **Vitest** with **React Testing Library** for unit/integration testing and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test idea:

```tsx
it("does not re-render ProductCard when unrelated parent state changes", () => {
  // Render App, update the counter,
  // and verify ProductCard render count remains unchanged.
});
```

---

# Ops & Deployment

- Use the React DevTools Profiler in development to identify expensive renders before introducing memoization.
- Wrap memoized UI with Error Boundaries where appropriate for resilience.
- In SSR frameworks (e.g., Next.js), remember that `React.memo` primarily optimizes client-side re-renders after hydration.
- Keep bundle sizes small through route-level code splitting and lazy loading.
- Monitor real user performance (e.g., Web Vitals) to validate optimization efforts in production.

---

# Pitfalls

- **Passing inline objects or functions:** New references on every render negate the benefits of `React.memo`.
- **Overusing custom comparators:** Deep or complex comparisons may cost more than simply re-rendering.
- **Memoizing everything:** Apply memoization based on profiling, not by default, as unnecessary memoization increases code complexity.

## Question 4. How do you implement a multi-select dropdown with checkboxes?

## Question 5. How do you implement conditional animations in React?

## Question 6. How do you share state between deeply nested components without props drilling?

## Question 7. How do you implement drag-and-drop reordering of a list?

## Question 8. How do you create a custom hook for window resize events?

## Question 9. How do you prevent unnecessary re-rendering of components with useMemo?

## Question 10. How do you implement tab navigation using context?

## Question 11. How do you integrate a charting library like Chart.js in React?

## Question 12. How do you implement a responsive modal dialog?

## Question 13. How do you implement a multi-step form with validation?

## Question 14. How do you implement dynamic import of components based on route?

## Question 15. How do you cancel ongoing fetch requests when a component unmounts?

## Question 16. How do you implement a global error handler for API calls?

## Question 17. How do you implement theme toggling with localStorage persistence?

## Question 18. How do you optimize images for better performance in React apps?

## Question 19. How do you implement a copy-to-clipboard functionality?

## Question 20. How do you integrate a third-party date picker component?
