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

# Short answer

Implement a multi-select dropdown by maintaining an array (or `Set`) of selected values in state, rendering each option with a checkbox, and updating the selection when a checkbox is toggled. Build it as a reusable, controlled component and optimize large option lists with memoization or virtualization.

---

# Explanation

A multi-select dropdown lets users choose multiple options without closing the dropdown after every selection.

A reusable component typically consists of:

```text
MultiSelect
├── Trigger Button
├── Selected Values / Chips
├── Search (optional)
├── Dropdown Menu
│   ├── Checkbox Item
│   ├── Checkbox Item
│   └── Checkbox Item
└── Footer (Clear / Select All)
```

## State management

The component usually tracks:

- Whether the dropdown is open
- Selected options
- Optional search query

```ts
const [isOpen, setIsOpen] = useState(false);
const [selectedIds, setSelectedIds] = useState<number[]>([]);
```

For very large datasets, using a `Set<number>` provides faster membership checks (`O(1)`), though you'll typically convert it to an array when exposing values to parent components.

## Toggle selection

When a checkbox is clicked:

- If already selected → remove it.
- Otherwise → add it.

Use a functional state update to avoid stale state:

```ts
setSelectedIds((prev) =>
  prev.includes(id) ? prev.filter((item) => item !== id) : [...prev, id],
);
```

## React 18 considerations

- Automatic batching groups multiple state updates occurring in the same event.
- Keep the component **controlled** by accepting `value` and `onChange` props when integrating into larger forms.
- Use `startTransition` if filtering thousands of options causes noticeable UI lag.

---

# Example

**Scaffold (Vite + React + TypeScript)**

```bash
npm create vite@latest multi-select-demo -- --template react-ts
cd multi-select-demo
npm install
npm run dev
```

```tsx
import { useState } from "react";

type Option = {
  id: number;
  label: string;
};

const options: Option[] = [
  { id: 1, label: "React" },
  { id: 2, label: "Angular" },
  { id: 3, label: "Vue" },
  { id: 4, label: "Svelte" },
];

export default function MultiSelect() {
  const [selected, setSelected] = useState<number[]>([]);

  const toggleOption = (id: number) => {
    setSelected((prev) =>
      prev.includes(id) ? prev.filter((item) => item !== id) : [...prev, id],
    );
  };

  return (
    <div style={{ width: 250 }}>
      <h3>Frameworks</h3>

      {options.map((option) => (
        <label key={option.id} style={{ display: "block", marginBottom: 8 }}>
          <input
            type="checkbox"
            checked={selected.includes(option.id)}
            onChange={() => toggleOption(option.id)}
          />{" "}
          {option.label}
        </label>
      ))}

      <p>
        Selected:{" "}
        {options
          .filter((o) => selected.includes(o.id))
          .map((o) => o.label)
          .join(", ") || "None"}
      </p>
    </div>
  );
}
```

### Production enhancements

A production-ready multi-select often adds:

- Search/filter input
- Select All / Clear All
- Keyboard navigation (↑ ↓ Enter Esc)
- Click outside to close
- ARIA roles (`listbox`, `option`, `aria-selected`)
- Selected chips/tags
- Async option loading
- Virtualized rendering for large lists

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stacks:

- **Vite** – Fast HMR, native ESM, ideal for SPAs.
- **Next.js** – App Router with SSR, Server Components, and streaming.
- **Remix** – Excellent nested routing and data loading.

### ESM vs CommonJS

Modern React applications use **ES Modules (ESM)**:

```ts
import { useState } from "react";
```

ESM enables tree shaking, faster startup, and better optimization with bundlers such as Vite.

---

# Performance

For large option lists:

- Memoize individual option rows with `React.memo`.
- Memoize filtered results using `useMemo`.
- Memoize event handlers with `useCallback`.
- Use virtualization (`@tanstack/react-virtual` or `react-window`) to render only visible options.
- Lazy-load the dropdown component with `React.lazy` if it's not immediately needed.
- Use the React DevTools Profiler to confirm that only affected option rows re-render when selections change.

---

# Testing

Use **Vitest** with **React Testing Library** for unit/integration tests and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test idea:

```tsx
it("toggles a checkbox and updates the selected values", () => {
  // Render the component, click a checkbox,
  // and assert that the selected label is displayed.
});
```

Also test:

- Select All / Clear All
- Keyboard navigation
- Search filtering
- Accessibility attributes
- Click-outside behavior

---

# Ops & Deployment

- Wrap async option loading in an Error Boundary if it depends on remote data.
- Log option-loading failures with observability tools such as Sentry or OpenTelemetry.
- If options come from an API, cache them with TanStack Query or SWR to reduce repeated requests.
- Split large UI libraries (if used) into separate chunks to keep the initial bundle small.
- Monitor interaction latency (e.g., INP) to ensure the dropdown remains responsive under heavy datasets.

---

# Pitfalls

- **Using array indexes as keys:** Always use stable unique IDs to avoid incorrect checkbox state after reordering.
- **Mutating state directly:** Always return a new array or `Set` when updating selections.
- **Ignoring accessibility:** Support keyboard navigation, focus management, ARIA roles, and labels for an accessible experience.

## Question 5. How do you implement conditional animations in React?

# Short answer

Conditional animations in React are implemented by changing **state or props** and conditionally applying CSS classes, inline styles, or animation components. For simple transitions, use CSS transitions/animations. For complex enter/exit animations, layout transitions, and gesture support, use a library like **Framer Motion** (now Motion for React).

---

# Explanation

Animations in React should be driven by **state**, not direct DOM manipulation.

Typical flow:

```text
User Action
      │
      ▼
State changes
      │
      ▼
Conditional render / className change
      │
      ▼
CSS Transition or Animation Library
```

For example:

- Toggle a modal
- Expand/collapse an accordion
- Fade a notification
- Slide a sidebar
- Animate list items
- Show/hide a loading spinner

## Approaches

### 1. CSS Transitions (Recommended for simple animations)

Ideal for:

- Fade
- Scale
- Slide
- Hover effects
- Color changes

React only changes the class or style.

CSS performs the animation.

### 2. CSS Keyframes

Useful for:

- Bounce
- Pulse
- Rotate
- Infinite loading animations

### 3. Motion for React (Framer Motion)

Best for:

- Enter/exit animations
- Shared layout transitions
- Drag & gesture animations
- Page transitions
- Complex sequencing

## React 18 considerations

- Automatic batching reduces unnecessary renders when multiple state updates occur during animation triggers.
- Use `startTransition` for non-urgent state updates if animations coincide with expensive rendering.
- Avoid forcing synchronous layout reads (`getBoundingClientRect`) during every render, as they can hurt animation performance.

---

# Example

**Scaffold (Vite + React + TypeScript)**

```bash
npm create vite@latest animation-demo -- --template react-ts
cd animation-demo
npm install
npm run dev
```

### Component

```tsx
import { useState } from "react";
import "./App.css";

export default function App() {
  const [visible, setVisible] = useState(false);

  return (
    <>
      <button onClick={() => setVisible((v) => !v)}>Toggle</button>

      <div className={`box ${visible ? "show" : "hide"}`}>Hello React</div>
    </>
  );
}
```

### CSS

```css
.box {
  opacity: 0;
  transform: translateY(-20px);
  transition: all 300ms ease;
}

.box.show {
  opacity: 1;
  transform: translateY(0);
}

.box.hide {
  opacity: 0;
  transform: translateY(-20px);
}
```

When the button is clicked:

- State changes.
- `className` changes.
- CSS animates the transition smoothly.

### Motion for React example

For enter/exit animations:

```tsx
import { AnimatePresence, motion } from "motion/react";

<AnimatePresence>
  {visible && (
    <motion.div
      initial={{ opacity: 0, y: -20 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -20 }}
      transition={{ duration: 0.3 }}
    >
      Hello React
    </motion.div>
  )}
</AnimatePresence>;
```

This handles mounting and unmounting animations automatically.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stacks:

- **Vite** – Fast HMR, native ESM, ideal for SPAs.
- **Next.js** – SSR, App Router, Server Components, and route transitions.
- **Remix** – Great for nested routing and progressive enhancement.

### Animation libraries

- **Motion for React** (formerly Framer Motion) – Recommended for production React applications.
- **React Transition Group** – Lightweight option for enter/exit transitions.
- **GSAP** – Excellent for timeline-based and advanced animations.

### ESM vs CommonJS

Modern React applications use **ES Modules (ESM)**:

```ts
import { motion } from "motion/react";
```

ESM enables tree shaking and efficient bundling with tools like Vite.

---

# Performance

- Prefer animating **`transform`** and **`opacity`**, as they are GPU-accelerated and avoid layout recalculations.
- Avoid animating layout-affecting properties such as `width`, `height`, `top`, and `left` unless necessary.
- Memoize animated child components with `React.memo` if parent re-renders are frequent.
- Use `useMemo` and `useCallback` to stabilize expensive props passed into animated components.
- Lazy-load large animation libraries using `React.lazy` or dynamic imports.
- Use the React DevTools Profiler and browser Performance tools to identify dropped frames and unnecessary renders.

---

# Testing

Use **Vitest** with **React Testing Library** for unit/integration tests and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
it("shows the animated element after clicking the toggle button", () => {
  // Render component
  // Click the button
  // Assert the element is present and has the expected class
});
```

For E2E, use Playwright to verify visibility, transitions, and accessibility during animations.

---

# Ops & Deployment

- Respect the user's **`prefers-reduced-motion`** setting to reduce or disable non-essential animations for accessibility.
- Wrap animated sections with Error Boundaries if they depend on async data.
- Keep animation libraries out of the initial bundle when possible through code splitting.
- In SSR frameworks (e.g., Next.js), ensure initial server-rendered markup matches the first client render to avoid hydration mismatches.
- Monitor Core Web Vitals (especially INP and CLS) to ensure animations don't negatively affect user experience.

---

# Pitfalls

- **Animating layout properties:** Prefer `transform` and `opacity` for smoother, GPU-accelerated animations.
- **Unmounting immediately:** If using conditional rendering, use an animation library (e.g., Motion for React or React Transition Group) to allow exit animations before removal.
- **Ignoring accessibility:** Honor `prefers-reduced-motion` and avoid excessive or distracting animations.

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
