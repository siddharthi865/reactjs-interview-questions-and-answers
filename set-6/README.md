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

## Short answer

React is a **UI library** focused on building user interfaces with JavaScript/TypeScript and JSX, while Vue.js is a **progressive framework** that provides more built-in features (such as templating, reactivity, transitions, and official routing/state libraries). React offers greater flexibility, whereas Vue generally provides a smoother learning curve and more opinionated conventions.

---

# Explanation

## 1. Core philosophy

### React

- UI library maintained by Meta.
- Uses a component-based architecture with JSX.
- Minimal core; routing, state management, and data fetching are chosen separately.
- Highly flexible for projects of any size.

### Vue.js

- Progressive framework maintained by the open-source community.
- Supports both small incremental adoption and large applications.
- Official ecosystem includes Vue Router, Pinia, and build tooling.
- More convention-driven than React.

---

## 2. Syntax

### React

Uses **JSX**, where HTML-like syntax is written directly inside JavaScript/TypeScript.

```tsx
function Welcome() {
  return <h1>Hello React</h1>;
}
```

### Vue

Uses **Single File Components (SFCs)**.

```vue
<template>
  <h1>Hello Vue</h1>
</template>

<script setup lang="ts"></script>
```

Vue separates template, logic, and styles while keeping them in one file.

---

## 3. Rendering

### React

- Virtual DOM
- Reconciliation algorithm
- React 18 concurrent rendering
- Automatic batching
- Server Components (framework-dependent, e.g., Next.js)

### Vue

- Virtual DOM
- Fine-grained reactive dependency tracking
- Efficient updates because Vue knows exactly which reactive values changed

Both are highly performant for most applications.

---

## 4. State management

### React

No built-in global state.

Popular choices include:

- Context API
- Redux Toolkit
- Zustand
- Jotai
- TanStack Query (server state)

### Vue

Official ecosystem:

- Pinia
- Vue Router
- VueUse
- Composables

State management tends to feel more integrated.

---

## 5. Reactivity

### React

Updates happen through state setters.

```tsx
setCount(count + 1);
```

### Vue

Reactive variables update automatically.

```ts
count.value++;
```

Vue's reactivity system tracks dependencies automatically.

---

## 6. Learning curve

### React

- Easier to learn initially
- More architectural decisions for larger applications
- Requires understanding hooks and JSX

### Vue

- Very beginner friendly
- HTML templates feel familiar
- Less boilerplate for common patterns

---

## 7. Ecosystem

### React

- Largest ecosystem
- Next.js
- Remix
- Expo (React Native)
- React Query
- Redux Toolkit

### Vue

- Nuxt
- Pinia
- Vue Router
- VueUse

---

## 8. TypeScript support

Both support TypeScript well.

- React works naturally with TypeScript because components are plain functions.
- Vue 3 significantly improved TypeScript support with the Composition API and `<script setup>`.

---

## 9. Performance

Both frameworks are extremely fast.

### React optimization

- React.memo
- useMemo
- useCallback
- React.lazy
- Suspense
- Concurrent rendering

### Vue optimization

- Computed properties
- watch/watchEffect
- Lazy-loaded routes
- KeepAlive
- Fine-grained reactivity

---

## Example

### React (Vite + TypeScript)

**Scaffold:**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}
```

### Vue 3 (Vite + TypeScript)

```bash
npm create vite@latest my-vue-app -- --template vue-ts
cd my-vue-app
npm install
npm run dev
```

```vue
<script setup lang="ts">
import { ref } from "vue";

const count = ref(0);
</script>

<template>
  <button @click="count++">Count: {{ count }}</button>
</template>
```

---

# Tooling & Setup

### React

- Prefer **Vite** for client-side applications.
- **Next.js** for SSR, Server Components, and full-stack features.
- Uses ESM by default and benefits from fast HMR.

### Vue

- Prefer **Vite** with Vue 3.
- **Nuxt** for SSR, static generation, and full-stack capabilities.
- Also uses ESM and provides excellent developer experience.

**Avoid Create React App (CRA)** because it is deprecated. Vite offers significantly faster startup, hot module replacement, and optimized builds.

---

# Performance

For both React and Vue:

- Use browser DevTools and framework profilers to identify rendering bottlenecks.
- Memoize expensive computations (`useMemo` in React, `computed` in Vue).
- Lazy-load routes and large components.
- Split bundles using dynamic imports.
- Cache server data with libraries such as TanStack Query (React) or Vue Query.

---

# Testing

### React

Unit/Integration:

```bash
npm install -D vitest @testing-library/react jsdom
```

### Vue

Unit/Integration:

```bash
npm install -D vitest @vue/test-utils jsdom
```

For end-to-end testing, **Playwright** is an excellent choice for both ecosystems.

---

# Ops & Deployment

- Deploy static builds to a CDN for fast global delivery.
- Use Next.js (React) or Nuxt (Vue) when SSR or SEO is required.
- Implement centralized logging (e.g., Sentry).
- Use error boundaries in React; in Vue, use `errorCaptured` or global error handlers.
- Analyze bundle size regularly and remove unused dependencies.

---

# Pitfalls

- **React**
  - Choosing too many third-party libraries without clear standards can lead to inconsistent architecture.
  - Overusing `useMemo` and `useCallback` without measuring performance.
  - Mutating state instead of treating it as immutable.

- **Vue**
  - Mixing the Options API and Composition API inconsistently within a codebase.
  - Overusing watchers where computed properties are more appropriate.
  - Forgetting that refs require `.value` in JavaScript/TypeScript (outside templates).

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
