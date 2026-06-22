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

## Question 3. How do you memoize a component that receives multiple props?

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
