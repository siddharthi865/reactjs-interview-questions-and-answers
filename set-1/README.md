# Set 1

| #   | Question                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is ReactJS and why is it used?](#question-1-what-is-reactjs-and-why-is-it-used)                                                                                      |
| 2   | [What are the key features of React?](#question-2-what-are-the-key-features-of-react)                                                                                      |
| 3   | [What is JSX? Why do we use it?](#question-3-what-is-jsx-why-do-we-use-it)                                                                                                 |
| 4   | [Explain the Virtual DOM. How is it different from the real DOM?](#question-4-explain-the-virtual-dom-how-is-it-different-from-the-real-dom)                               |
| 5   | [What are React components? Difference between class and functional components?](#question-5-what-are-react-components-difference-between-class-and-functional-components) |
| 6   | [What is the difference between props and state in React?](#question-6-what-is-the-difference-between-props-and-state-in-react)                                            |
| 7   | [How do you pass data from parent to child component?](#question-7-how-do-you-pass-data-from-parent-to-child-component)                                                    |
| 8   | [What are default props?](#question-8-what-are-default-props)                                                                                                              |
| 9   | [How do you handle events in React? Give examples](#question-9-how-do-you-handle-events-in-react-give-examples)                                                            |
| 10  | [What is conditional rendering? Give examples](#question-10-what-is-conditional-rendering-give-examples)                                                                   |
| 11  | [What are lists and keys in React? Why are keys important?](#question-11-what-are-lists-and-keys-in-react-why-are-keys-important)                                          |
| 12  | [How do you handle forms in React?](#question-12-how-do-you-handle-forms-in-react)                                                                                         |
| 13  | [What is controlled vs uncontrolled component?](#question-13-what-is-controlled-vs-uncontrolled-component)                                                                 |
| 14  | [How do you lift state up in React?](#question-14-how-do-you-lift-state-up-in-react)                                                                                       |
| 15  | [What are React fragments?](#question-15-what-are-react-fragments)                                                                                                         |
| 16  | [What is React strict mode?](#question-16-what-is-react-strict-mode)                                                                                                       |
| 17  | [How do you prevent component re-rendering?](#question-17-how-do-you-prevent-component-re-rendering)                                                                       |
| 18  | [Explain the difference between stateful and stateless components](#question-18-explain-the-difference-between-stateful-and-stateless-components)                          |
| 19  | [What is the difference between function and arrow function components?](#question-19-what-is-the-difference-between-function-and-arrow-function-components)               |
| 20  | [How do you apply inline styles in React?](#question-20-how-do-you-apply-inline-styles-in-react)                                                                           |

## Question 1. What is ReactJS and why is it used?

## Short answer

ReactJS is a JavaScript library for building fast, interactive user interfaces using a component-based architecture. It is used because it enables efficient UI updates through a virtual DOM, reusable components, and predictable state-driven rendering.

---

## Explanation

ReactJS (developed by Meta) is primarily focused on building **declarative, component-based UIs**. Instead of manually manipulating the DOM, you describe _what the UI should look like for a given state_, and React handles updating the DOM efficiently.

### Key architectural ideas

**1. Component-based architecture**

- UI is split into reusable, isolated components
- Encourages separation of concerns and scalability in large apps
- Components can be functional (modern standard) or class-based (legacy)

**2. Declarative rendering**

- You describe UI as a function of state: `UI = f(state)`
- React automatically re-renders when state changes

**3. Virtual DOM & reconciliation**

- React keeps a lightweight in-memory representation of the DOM
- On state change, it computes a diff (reconciliation)
- Only minimal DOM updates are applied → improves performance

**4. React 18 concurrency model**

- Supports concurrent rendering (non-blocking UI updates)
- Automatic batching of state updates improves performance
- Features like `startTransition` allow prioritizing urgent vs non-urgent updates

---

### Why React is used in real-world applications

**1. Scalability**

- Large applications (Meta, Netflix, Airbnb) rely on component modularity

**2. Performance optimization**

- Virtual DOM + diffing reduces expensive DOM operations
- Supports memoization strategies (`React.memo`, `useMemo`, `useCallback`)

**3. Strong ecosystem**

- Works well with Next.js, Remix, Vite
- Rich state management options (Redux, Zustand, React Query)

**4. Developer experience**

- JSX makes UI logic and markup co-located
- Hot module replacement via modern tooling (Vite, Next.js)

**5. Cross-platform capability**

- React Native enables mobile app development using same principles

---

## Example

### Setup (Vite + React + TypeScript)

```bash
npm create vite@latest react-intro -- --template react-ts
cd react-intro
npm install
npm run dev
```

---

### Simple React component

```tsx
import { useState } from "react";

type CounterProps = {
  initialValue?: number;
};

export default function Counter({ initialValue = 0 }: CounterProps) {
  const [count, setCount] = useState(initialValue);

  return (
    <div>
      <h1>Counter: {count}</h1>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>

      <button onClick={() => setCount((c) => c - 1)}>Decrement</button>
    </div>
  );
}
```

---

## Tooling & Setup

- **Recommended stack**: Vite + React + TypeScript
  - Faster dev server (ESM-based, no bundling in dev)
  - Better HMR than legacy CRA

- Alternative:
  - Next.js (SSR/SSG, full-stack React)
  - Remix (server-first architecture)

**Why not CRA?**

- Deprecated and slower build tooling
- Lacks modern ESM-first architecture

---

## Performance

Key optimization strategies:

- `React.memo` → prevent unnecessary re-renders
- `useMemo` → memoize expensive computations
- `useCallback` → stable function references
- Code splitting: `React.lazy` + `Suspense`
- Use React DevTools Profiler to detect wasted renders
- Use concurrent features (`startTransition`) for non-urgent updates

---

## Testing

- Unit & integration: **Vitest + React Testing Library**
- E2E: **Playwright**

Example:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Basic test:

```tsx
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders counter", () => {
  render(<Counter initialValue={0} />);
  expect(screen.getByText(/Counter:/)).toBeInTheDocument();
});
```

---

## Ops & Deployment

- SSR vs CSR:
  - CSR (Vite): fast client apps, simpler deployment
  - SSR (Next.js): better SEO, faster initial load

- Use CDN for static assets (Vercel, Netlify, Cloudflare)
- Error boundaries for runtime safety
- Monitor bundle size with `vite-bundle-analyzer`
- Logging via services like Sentry

---

## Pitfalls

- Overusing state → unnecessary re-renders and complexity
- Ignoring dependency arrays in hooks
- Premature optimization without profiling
- Using global state when local state is sufficient

## Question 2. What are the key features of React?

## Question 3. What is JSX? Why do we use it?

## Question 4. Explain the Virtual DOM. How is it different from the real DOM?

## Question 5. What are React components? Difference between class and functional components?

## Question 6. What is the difference between props and state in React?

## Question 7. How do you pass data from parent to child component?

## Question 8. What are default props?

## Question 9. How do you handle events in React? Give examples

## Question 10. What is conditional rendering? Give examples

## Question 11. What are lists and keys in React? Why are keys important?

## Question 12. How do you handle forms in React?

## Question 13. What is controlled vs uncontrolled component?

## Question 14. How do you lift state up in React?

## Question 15. What are React fragments?

## Question 16. What is React strict mode?

## Question 17. How do you prevent component re-rendering?

## Question 18. Explain the difference between stateful and stateless components

## Question 19. What is the difference between function and arrow function components?

## Question 20. How do you apply inline styles in React?
