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

## Question 3. Explain useReducer hook with an example

## Question 4. Difference between useState and useReducer

## Question 5. What is context API? How is it used?

## Question 6. How do you handle side effects in React?

## Question 7. Explain higher-order components (HOC)

## Question 8. What is React Portal? Give a use case

## Question 9. How do you handle errors in React? Explain error boundaries

## Question 10. Difference between controlled and uncontrolled forms

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
