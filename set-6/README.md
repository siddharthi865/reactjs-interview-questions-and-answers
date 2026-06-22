# Set 6

| #   | Question                                                                                                                                                                                                 |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What are the differences between React and Angular?](#question-1-what-are-the-differences-between-react-and-angular)                                                                                    |
| 2   | [What are the differences between React and Vue.js?](#question-2-what-are-the-differences-between-react-and-vuejs)                                                                                       |
| 3   | [How does React handle DOM updates?](#question-3-how-does-react-handle-dom-updates)                                                                                                                      |
| 4   | [What is the purpose of React.createElement?](#question-4-what-is-the-purpose-of-reactcreateelement)                                                                                                     |
| 5   | [Explain the difference between functional and class components in terms of lifecycle methods](#question-5-explain-the-difference-between-functional-and-class-components-in-terms-of-lifecycle-methods) |
| 6   | [How do you pass multiple props to a child component?](#question-6-how-do-you-pass-multiple-props-to-a-child-component)                                                                                  |
| 7   | [Can you use props to modify parent state? Why or why not?](#question-7-can-you-use-props-to-modify-parent-state-why-or-why-not)                                                                         |
| 8   | [How do you render a component conditionally without using if statements?](#question-8-how-do-you-render-a-component-conditionally-without-using-if-statements)                                          |
| 9   | [What are fragments used for in React?](#question-9-what-are-fragments-used-for-in-react)                                                                                                                |
| 10  | [How do you prevent default form submission in React?](#question-10-how-do-you-prevent-default-form-submission-in-react)                                                                                 |
| 11  | [How do you bind this in class components?](#question-11-how-do-you-bind-this-in-class-components)                                                                                                       |
| 12  | [What is the difference between state updates in class vs functional components?](#question-12-what-is-the-difference-between-state-updates-in-class-vs-functional-components)                           |
| 13  | [What is the difference between React.Component and React.PureComponent?](#question-13-what-is-the-difference-between-reactcomponent-and-reactpurecomponent)                                             |
| 14  | [How do you reuse components in React?](#question-14-how-do-you-reuse-components-in-react)                                                                                                               |
| 15  | [How do you handle errors in event handlers?](#question-15-how-do-you-handle-errors-in-event-handlers)                                                                                                   |
| 16  | [How can you pass children to a component?](#question-16-how-can-you-pass-children-to-a-component)                                                                                                       |
| 17  | [How do you render multiple components inside a parent?](#question-17-how-do-you-render-multiple-components-inside-a-parent)                                                                             |
| 18  | [What is the role of React.StrictMode?](#question-18-what-is-the-role-of-reactstrictmode)                                                                                                                |
| 19  | [Can you return multiple elements from a component? How?](#question-19-can-you-return-multiple-elements-from-a-component-how)                                                                            |
| 20  | [How do you convert class components to functional components?](#question-20-how-do-you-convert-class-components-to-functional-components)                                                               |

## Question 1. What are the differences between React and Angular?

## Short answer

React is a **UI library** focused on building component-based views, while Angular is a **full-fledged framework** that provides a complete opinionated solution (routing, DI, forms, HTTP, etc.) out of the box.

---

## Explanation

### 1. Core philosophy

- **React**
  - Library for building UI
  - Unopinionated → you choose routing, state management, architecture
  - Composition-based (functions + hooks)
  - React 18 introduces concurrent rendering, automatic batching

- **Angular**
  - Full MVC-ish framework (now component-based)
  - Highly opinionated architecture
  - Built-in solutions: routing, forms, HTTP client, DI, RxJS integration

---

### 2. Rendering & architecture

- **React**
  - Virtual DOM + reconciliation
  - Functional components + hooks
  - Flexible rendering strategies (CSR, SSR with Next.js, RSC)

- **Angular**
  - Real DOM with change detection strategy (zone.js)
  - Two-way binding is common
  - Component templates tightly coupled with logic

---

### 3. Data flow

- **React**
  - One-way data flow (props down, events up)
  - State managed via hooks, Redux, Zustand, etc.

- **Angular**
  - Two-way binding supported (`[(ngModel)]`)
  - RxJS heavily used for reactive state streams

---

### 4. Learning curve

- **React**
  - Easier to start
  - Harder to architect at scale (because choices are open)

- **Angular**
  - Steeper learning curve
  - Easier to scale consistently in large teams due to strict conventions

---

### 5. Ecosystem

- **React**
  - Huge ecosystem (Next.js, Remix, React Query, Zustand)
  - Fast innovation cycle

- **Angular**
  - Stable ecosystem
  - Strong CLI + official tooling

---

### 6. Performance model

- React relies on:
  - reconciliation + memoization (`React.memo`, `useMemo`)
  - concurrent rendering (React 18)

- Angular relies on:
  - change detection optimization strategies (`OnPush`)
  - zone.js tracking async updates

---

## Example

### React (Vite + TypeScript)

**Scaffold:**

```bash
npm create vite@latest react-app -- --template react-ts
cd react-app
npm install
npm run dev
```

**Component:**

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <h1>React Counter</h1>
      <p>{count}</p>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </div>
  );
}
```

---

### Angular equivalent (conceptual)

```ts
@Component({
  selector: "app-counter",
  template: `
    <h1>Angular Counter</h1>
    <p>{{ count }}</p>
    <button (click)="increment()">Increment</button>
  `,
})
export class CounterComponent {
  count = 0;

  increment() {
    this.count++;
  }
}
```

---

## Tooling & Setup

### React

- Preferred: **Vite / Next.js**
- Vite uses **ESM + fast HMR via esbuild**
- Flexible architecture choices:
  - React Router / TanStack Router
  - Zustand/Redux/React Query

### Angular

- Angular CLI (`ng new`)
- Webpack-based (or modern esbuild in newer Angular versions)
- Fully integrated toolchain

**Why Vite over CRA?**

- CRA is deprecated/legacy
- Vite provides faster dev server, better ESM support, and modern bundling

---

## Performance

### React

- `React.memo` for memoized components
- `useMemo` / `useCallback` for stable references
- Code splitting via `React.lazy`
- Profiler: React DevTools Profiler
- React 18: automatic batching + concurrent rendering

### Angular

- `ChangeDetectionStrategy.OnPush`
- `trackBy` in `*ngFor`
- Lazy-loaded modules
- RxJS stream optimization

---

## Testing

### React

- Unit/Integration: **Vitest + React Testing Library**

```bash
npm install -D vitest @testing-library/react jsdom
```

```ts
import { render, screen } from "@testing-library/react";
import Counter from "./Counter";

test("renders counter", () => {
  render(<Counter />);
  expect(screen.getByText(/React Counter/i)).toBeInTheDocument();
});
```

### Angular

- Jasmine/Karma (legacy) or Jest (modern setups)
- Angular Testing Utilities (`TestBed`)

---

## Ops & Deployment

- React:
  - SSR: Next.js (Vercel/Edge)
  - CSR: static hosting (S3 + CDN)

- Angular:
  - Angular Universal for SSR
  - Strong enterprise deployment patterns

Other concerns:

- Error boundaries (React)
- Logging: Sentry / Datadog
- Bundle optimization: tree-shaking, dynamic imports
- CDN caching for static assets

---

## Pitfalls

- React:
  - Over-flexibility leads to inconsistent architecture
  - Improper dependency arrays in hooks
  - Unoptimized re-renders

- Angular:
  - Overhead from zone.js change detection
  - Complex RxJS learning curve
  - Verbosity for simple apps

## Question 2. What are the differences between React and Vue.js?

## Question 3. How does React handle DOM updates?

## Question 4. What is the purpose of React.createElement?

## Question 5. Explain the difference between functional and class components in terms of lifecycle methods

## Question 6. How do you pass multiple props to a child component?

## Question 7. Can you use props to modify parent state? Why or why not?

## Question 8. How do you render a component conditionally without using if statements?

## Question 9. What are fragments used for in React?

## Question 10. How do you prevent default form submission in React?

## Question 11. How do you bind this in class components?

## Question 12. What is the difference between state updates in class vs functional components?

## Question 13. What is the difference between React.Component and React.PureComponent?

## Question 14. How do you reuse components in React?

## Question 15. How do you handle errors in event handlers?

## Question 16. How can you pass children to a component?

## Question 17. How do you render multiple components inside a parent?

## Question 18. What is the role of React.StrictMode?

## Question 19. Can you return multiple elements from a component? How?

## Question 20. How do you convert class components to functional components?
