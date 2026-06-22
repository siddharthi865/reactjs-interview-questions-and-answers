# Set 2

| #   | Question                                                                                                                                                   |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is the difference between className and class in JSX?](#question-1-what-is-the-difference-between-classname-and-class-in-jsx)                        |
| 2   | [What are synthetic events in React?](#question-2-what-are-synthetic-events-in-react)                                                                      |
| 3   | [How do you conditionally apply CSS classes?](#question-3-how-do-you-conditionally-apply-css-classes)                                                      |
| 4   | [Explain the role of key prop in React lists](#question-4-explain-the-role-of-key-prop-in-react-lists)                                                     |
| 5   | [How do you debug React applications?](#question-5-how-do-you-debug-react-applications)                                                                    |
| 6   | [What is ReactDOM? Difference between React and ReactDOM?](#question-6-what-is-reactdom-difference-between-react-and-reactdom)                             |
| 7   | [How do you include images in React projects?](#question-7-how-do-you-include-images-in-react-projects)                                                    |
| 8   | [What is React component lifecycle?](#question-8-what-is-react-component-lifecycle)                                                                        |
| 9   | [Difference between mounting, updating, and unmounting phases](#question-9-difference-between-mounting-updating-and-unmounting-phases)                     |
| 10  | [How do you use ref in React?](#question-10-how-do-you-use-ref-in-react)                                                                                   |
| 11  | [What is the difference between props drilling and context API?](#question-11-what-is-the-difference-between-props-drilling-and-context-api)               |
| 12  | [Explain lifting state up with an example](#question-12-explain-lifting-state-up-with-an-example)                                                          |
| 13  | [How do you pass a callback function as a prop?](#question-13-how-do-you-pass-a-callback-function-as-a-prop)                                               |
| 14  | [What is JSX transpilation?](#question-14-what-is-jsx-transpilation)                                                                                       |
| 15  | [How do you create React app from scratch using CRA (Create React App)?](#question-15-how-do-you-create-react-app-from-scratch-using-cra-create-react-app) |
| 16  | [What are React hooks? Name a few commonly used hooks](#question-16-what-are-react-hooks-name-a-few-commonly-used-hooks)                                   |
| 17  | [Explain useState hook with an example](#question-17-explain-usestate-hook-with-an-example)                                                                |
| 18  | [Explain useEffect hook with an example](#question-18-explain-useeffect-hook-with-an-example)                                                              |
| 19  | [What is dependency array in useEffect?](#question-19-what-is-dependency-array-in-useeffect)                                                               |
| 20  | [Explain useRef hook and its use cases](#question-20-explain-useref-hook-and-its-use-cases)                                                                |

## Question 1. What is the difference between className and class in JSX?

## Short answer

In JSX, you use `className` instead of `class` because `class` is a reserved keyword in JavaScript. Both serve the same purpose: assigning CSS classes to elements.

---

## Explanation

### Why `className` exists in React (JSX)

JSX is not HTML—it is syntactic sugar over `React.createElement()` calls. Since JavaScript already uses `class` as a reserved keyword for ES6 classes, React avoids conflicts by using `className`.

```jsx
// JSX
<div className="container" />
```

Compiles roughly to:

```js
React.createElement("div", { className: "container" });
```

### Rendering behavior

- `className` is passed as a prop to the DOM element.
- React reconciles it during the virtual DOM diffing process.
- It updates only when the value changes (React 18 batching optimizes this further).

### React 18 concurrency note

In concurrent rendering, class updates are still part of the commit phase. React batches multiple state updates (even across async boundaries) and applies DOM changes efficiently, including `className` updates.

---

## Example

### React + TypeScript (Vite setup)

#### Scaffold

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

#### Component example

```tsx
import { useState } from "react";

export default function Button() {
  const [active, setActive] = useState(false);

  return (
    <button
      className={active ? "btn btn-active" : "btn"}
      onClick={() => setActive((prev) => !prev)}
    >
      Toggle
    </button>
  );
}
```

---

## Tooling & Setup

- Prefer **Vite** for fast ESM-based development (native ES modules, fast HMR).
- Avoid CRA (deprecated and slower tooling).
- In modern stacks:
  - Vite → CSR apps
  - Next.js → SSR/SSG + React Server Components

JSX compilation happens via:

- Vite: esbuild / SWC (fast transforms)
- Babel (older setups)

---

## Performance

- `className` updates trigger DOM diffing; avoid unnecessary re-renders.
- Use:
  - `React.memo` for component memoization
  - `useMemo` for computed class strings
  - `clsx` or `classnames` for efficient conditional classes

Example optimization:

```tsx
import { useMemo } from "react";

const className = useMemo(() => (active ? "btn btn-active" : "btn"), [active]);
```

- Use React DevTools Profiler to detect re-render causes.

---

## Testing

Using **Vitest + React Testing Library**:

```ts
import { render, screen } from "@testing-library/react";
import Button from "./Button";

test("renders button with correct class", () => {
  render(<Button />);
  const btn = screen.getByRole("button");
  expect(btn.className).toContain("btn");
});
```

Run:

```bash
npm run test
```

---

## Ops & Deployment

- Class-based styling integrates well with:
  - CSS Modules
  - Tailwind CSS (preferred in modern apps)

- Ensure:
  - Avoid inline string concatenation for large apps → use utility libraries
  - SSR frameworks (Next.js) ensure consistent hydration className matching

- Deploy via:
  - Vercel (Next.js)
  - Netlify / Cloudflare Pages (Vite apps)

---

## Pitfalls

- Using `class` instead of `className` causes JSX errors
- Overusing string concatenation for dynamic classes reduces readability
- Not memoizing complex class logic can lead to unnecessary re-renders

## Question 2. What are synthetic events in React?

## Question 3. How do you conditionally apply CSS classes?

## Question 4. Explain the role of key prop in React lists

## Question 5. How do you debug React applications?

## Question 6. What is ReactDOM? Difference between React and ReactDOM?

## Question 7. How do you include images in React projects?

## Question 8. What is React component lifecycle?

## Question 9. Difference between mounting, updating, and unmounting phases

## Question 10. How do you use ref in React?

## Question 11. What is the difference between props drilling and context API?

## Question 12. Explain lifting state up with an example

## Question 13. How do you pass a callback function as a prop?

## Question 14. What is JSX transpilation?

## Question 15. How do you create React app from scratch using CRA (Create React App)?

## Question 16. What are React hooks? Name a few commonly used hooks

## Question 17. Explain useState hook with an example

## Question 18. Explain useEffect hook with an example

## Question 19. What is dependency array in useEffect?

## Question 20. Explain useRef hook and its use cases
