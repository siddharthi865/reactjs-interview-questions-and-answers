# Set 8

| S.No. | Question                                                                                                                                                                                    |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between `useState` lazy initialization vs direct initialization?](#question-1-what-is-the-difference-between-usestate-lazy-initialization-vs-direct-initialization) |
| 2.    | [How do you implement theme switching using context API?](#question-2-how-do-you-implement-theme-switching-using-context-api)                                                               |
| 3.    | [Explain the concept of Render Props in React](#question-3-explain-the-concept-of-render-props-in-react)                                                                                    |
| 4.    | [Difference between render props and higher-order components](#question-4-difference-between-render-props-and-higher-order-components)                                                      |
| 5.    | [How do you implement infinite scroll using IntersectionObserver in React?](#question-5-how-do-you-implement-infinite-scroll-using-intersectionobserver-in-react)                           |
| 6.    | [How do you handle memoization of components with dynamic props?](#question-6-how-do-you-handle-memoization-of-components-with-dynamic-props)                                               |
| 7.    | [How do you prevent stale closures in hooks?](#question-7-how-do-you-prevent-stale-closures-in-hooks)                                                                                       |
| 8.    | [Explain the difference between `componentDidMount` and `useEffect(() => {}, [])`](#question-8-explain-the-difference-between-componentdidmount-and-useeffect---)                           |
| 9.    | [How do you handle forms with dynamic fields in React?](#question-9-how-do-you-handle-forms-with-dynamic-fields-in-react)                                                                   |
| 10.   | [How do you manage state across multiple components using context?](#question-10-how-do-you-manage-state-across-multiple-components-using-context)                                          |
| 11.   | [How do you implement dark mode in React?](#question-11-how-do-you-implement-dark-mode-in-react)                                                                                            |
| 12.   | [How do you debounce input changes in React?](#question-12-how-do-you-debounce-input-changes-in-react)                                                                                      |
| 13.   | [How do you implement image lazy loading with `React.lazy` and `Suspense`?](#question-13-how-do-you-implement-image-lazy-loading-with-reactlazy-and-suspense)                               |
| 14.   | [How do you handle error messages in forms in React?](#question-14-how-do-you-handle-error-messages-in-forms-in-react)                                                                      |
| 15.   | [How do you implement client-side caching in React?](#question-15-how-do-you-implement-client-side-caching-in-react)                                                                        |
| 16.   | [How do you implement optimistic UI updates in React?](#question-16-how-do-you-implement-optimistic-ui-updates-in-react)                                                                    |
| 17.   | [How do you persist state across page refreshes?](#question-17-how-do-you-persist-state-across-page-refreshes)                                                                              |
| 18.   | [How do you integrate React with third-party libraries like Chart.js or D3.js?](#question-18-how-do-you-integrate-react-with-third-party-libraries-like-chartjs-or-d3js)                    |
| 19.   | [How do you implement a search/filter component in React?](#question-19-how-do-you-implement-a-searchfilter-component-in-react)                                                             |
| 20.   | [How do you implement multi-step forms in React?](#question-20-how-do-you-implement-multi-step-forms-in-react)                                                                              |

## Question 1. What is the difference between `useState` lazy initialization vs direct initialization?

## Short answer

Direct initialization computes the initial state on every render (though React only uses it on the first render), while lazy initialization computes the initial state only once on mount using a function passed to `useState`.

---

## Explanation

### 1. Direct initialization

```js
const [state, setState] = useState(expensiveComputation());
```

- `expensiveComputation()` runs **on every render**.
- React only uses the returned value for the **initial render**, but the function is still executed repeatedly.
- This can cause unnecessary performance overhead.

#### Key behavior:

- Execution happens during render phase.
- Wasteful if computation is heavy.

---

### 2. Lazy initialization

```js
const [state, setState] = useState(() => expensiveComputation());
```

- The function is executed **only once (on initial mount)**.
- React ignores it on subsequent renders.
- Ideal for expensive computations or reading from localStorage, IndexedDB, etc.

#### Key behavior:

- Function is passed, not executed immediately.
- React invokes it only during initial state setup.

---

### React rendering behavior (important for interviews)

- React 18 with concurrent rendering may render components multiple times before committing.
- Direct initialization can re-run expensive logic unnecessarily during these render attempts.
- Lazy initialization ensures computation is **isolated to initial mount phase only**, improving performance stability.

---

### State management trade-offs

- Use **direct initialization** when:
  - Value is cheap (constants, simple calculations)
  - Readability matters more than micro-optimization

- Use **lazy initialization** when:
  - Computation is expensive (sorting large arrays, parsing JSON)
  - Reading from browser APIs (`localStorage`, `sessionStorage`)
  - Avoiding unnecessary work during re-renders

---

## Example (React + TypeScript)

### Direct vs Lazy Initialization

```tsx
import React, { useState } from "react";

function expensiveComputation() {
  console.log("Expensive computation running...");
  return Array.from({ length: 1000000 }, (_, i) => i).reduce(
    (a, b) => a + b,
    0,
  );
}

export default function Counter() {
  // ❌ Runs on every render (bad for expensive logic)
  const [directValue] = useState(expensiveComputation());

  // ✅ Runs only once on mount
  const [lazyValue] = useState(() => expensiveComputation());

  const [count, setCount] = useState(0);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount((c) => c + 1)}>Re-render</button>

      <p>Direct init value: {directValue}</p>
      <p>Lazy init value: {lazyValue}</p>
    </div>
  );
}
```

---

## Tooling & Setup

Use **Vite (recommended)** instead of CRA:

```bash
npm create vite@latest react-lazy-state -- --template react-ts
cd react-lazy-state
npm install
npm run dev
```

- Vite uses **ESM-based dev server** (fast HMR)
- Bundler: **Rollup for production builds**
- Modern React setups rely on ESM instead of CommonJS for tree-shaking efficiency

---

## Performance

- Prefer lazy initialization when:
  - CPU-heavy computations exist
  - Data hydration from storage/API sync is needed

### Optimization techniques:

- Avoid unnecessary re-computation in render
- Combine with:
  - `React.memo` for component memoization
  - `useMemo` for derived state (not initial state)
  - `useCallback` for stable handlers

- Use React DevTools Profiler to detect re-render cost

---

## Testing

Using **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders lazy initialized value", () => {
  render(<Counter />);
  expect(screen.getByText(/Direct init value/)).toBeInTheDocument();
});
```

- Mock expensive functions using `vi.fn()`
- Ensure initialization only happens once (spy on function calls)

---

## Ops & Deployment

- Use lazy init to reduce **client-side startup cost**
- Important in SSR frameworks (Next.js):
  - Avoid running browser-only logic during SSR

- Combine with:
  - Code splitting (`React.lazy`)
  - CDN caching for static assets
  - Edge rendering (Next.js / Vercel)

---

## Pitfalls

- ❌ Calling expensive functions directly in `useState`
- ❌ Assuming lazy init runs on every render (it does NOT)
- ❌ Using lazy init for derived state (better with `useMemo`)
- ❌ Over-optimizing trivial computations

## Question 2. How do you implement theme switching using context API?

## Question 3. Explain the concept of Render Props in React

## Question 4. Difference between render props and higher-order components

## Question 5. How do you implement infinite scroll using IntersectionObserver in React?

## Question 6. How do you handle memoization of components with dynamic props?

## Question 7. How do you prevent stale closures in hooks?

## Question 8. Explain the difference between `componentDidMount` and `useEffect(() => {}, [])`

## Question 9. How do you handle forms with dynamic fields in React?

## Question 10. How do you manage state across multiple components using context?

## Question 11. How do you implement dark mode in React?

## Question 12. How do you debounce input changes in React?

## Question 13. How do you implement image lazy loading with `React.lazy` and `Suspense`?

## Question 14. How do you handle error messages in forms in React?

## Question 15. How do you implement client-side caching in React?

## Question 16. How do you implement optimistic UI updates in React?

## Question 17. How do you persist state across page refreshes?

## Question 18. How do you integrate React with third-party libraries like Chart.js or D3.js?

## Question 19. How do you implement a search/filter component in React?

## Question 20. How do you implement multi-step forms in React?
