# Set 10

| S.No. | Question                                                                                                                                                                   |
| ----- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you integrate Redux with React?](#question-1-how-do-you-integrate-redux-with-react)                                                                                |
| 2.    | [How do you implement Redux selectors and memoization?](#question-2-how-do-you-implement-redux-selectors-and-memoization)                                                  |
| 3.    | [How do you implement asynchronous Redux actions using Thunk or Saga?](#question-3-how-do-you-implement-asynchronous-redux-actions-using-thunk-or-saga)                    |
| 4.    | [How do you integrate React with GraphQL?](#question-4-how-do-you-integrate-react-with-graphql)                                                                            |
| 5.    | [How do you handle caching with Apollo Client?](#question-5-how-do-you-handle-caching-with-apollo-client)                                                                  |
| 6.    | [How do you implement WebSocket-based real-time updates?](#question-6-how-do-you-implement-websocket-based-real-time-updates)                                              |
| 7.    | [How do you implement offline-first React apps?](#question-7-how-do-you-implement-offline-first-react-apps)                                                                |
| 8.    | [How do you implement service workers in React apps?](#question-8-how-do-you-implement-service-workers-in-react-apps)                                                      |
| 9.    | [How do you monitor React app performance with React Profiler?](#question-9-how-do-you-monitor-react-app-performance-with-react-profiler)                                  |
| 10.   | [How do you debug memory leaks in React?](#question-10-how-do-you-debug-memory-leaks-in-react)                                                                             |
| 11.   | [How do you prevent unnecessary network requests in React?](#question-11-how-do-you-prevent-unnecessary-network-requests-in-react)                                         |
| 12.   | [How do you implement drag-and-drop with React DnD or similar libraries?](#question-12-how-do-you-implement-drag-and-drop-with-react-dnd-or-similar-libraries)             |
| 13.   | [How do you implement dynamic component rendering based on JSON configuration?](#question-13-how-do-you-implement-dynamic-component-rendering-based-on-json-configuration) |
| 14.   | [How do you implement advanced form validation with Yup and Formik?](#question-14-how-do-you-implement-advanced-form-validation-with-yup-and-formik)                       |
| 15.   | [How do you implement internationalization (i18n) in React apps?](#question-15-how-do-you-implement-internationalization-i18n-in-react-apps)                               |
| 16.   | [How do you implement micro-frontends using React?](#question-16-how-do-you-implement-micro-frontends-using-react)                                                         |
| 17.   | [How do you handle versioning and backward compatibility in React components?](#question-17-how-do-you-handle-versioning-and-backward-compatibility-in-react-components)   |
| 18.   | [How do you integrate React with legacy jQuery code?](#question-18-how-do-you-integrate-react-with-legacy-jquery-code)                                                     |
| 19.   | [How do you implement analytics tracking in React applications?](#question-19-how-do-you-implement-analytics-tracking-in-react-applications)                               |
| 20.   | [How do you optimize React app bundle size using tree shaking and webpack?](#question-20-how-do-you-optimize-react-app-bundle-size-using-tree-shaking-and-webpack)         |

## Question 1. How do you integrate Redux with React?

# How do you integrate Redux with React?

## Short answer

Redux is integrated with React using **React Redux**, which connects the Redux store to React components via the **`<Provider>`** component and hooks like **`useSelector`** (read state) and **`useDispatch`** (dispatch actions). In modern React, **Redux Toolkit (RTK)** is the recommended way to configure the store and write Redux logic.

---

# Explanation

Modern Redux integration follows this architecture:

```
React Component
      │
      ├── useSelector() → Read Redux state
      │
      ├── useDispatch() → Dispatch action
      │
      ▼
Redux Store
      │
      ▼
Reducer (RTK Slice)
      │
      ▼
Updated State
      │
      ▼
React automatically re-renders subscribed components
```

The integration consists of four main steps:

### 1. Create the Redux Store

With Redux Toolkit:

```ts
configureStore();
```

This automatically:

- Combines reducers
- Adds Redux DevTools
- Enables good middleware defaults
- Supports async middleware

---

### 2. Wrap React with `<Provider>`

`Provider` makes the Redux store available to every component.

```tsx
<Provider store={store}>
  <App />
</Provider>
```

Without Provider:

- `useSelector()` won't work
- `useDispatch()` won't work

---

### 3. Read State

React components subscribe using:

```tsx
const value = useSelector(...)
```

`useSelector`

- subscribes only to required data
- re-renders only when selected value changes
- uses strict reference equality

---

### 4. Update State

Dispatch actions:

```tsx
const dispatch = useDispatch();

dispatch(increment());
```

The action:

```
dispatch()
    ↓
Store
    ↓
Reducer
    ↓
New State
    ↓
Subscribed Components Re-render
```

---

## React 18 Rendering Behavior

React Redux v8 uses React 18 APIs internally.

Benefits include:

- Concurrent rendering compatibility
- Automatic batching
- Efficient subscriptions
- Minimal re-renders

Example:

```tsx
dispatch(updateUser());
dispatch(updateSettings());
```

React 18 batches these updates automatically when appropriate, reducing unnecessary renders.

---

## Why Redux Toolkit?

Older Redux required:

- action types
- action creators
- reducers
- switch statements
- manual middleware

Redux Toolkit simplifies this:

```tsx
createSlice();
configureStore();
createAsyncThunk();
```

Less boilerplate

Better TypeScript

Better defaults

Official recommendation

---

## State Management Trade-offs

Use Redux when:

- Global application state
- Authentication
- User profile
- Shopping cart
- Feature flags
- Permissions
- Large enterprise apps

Avoid Redux for:

- Local component state
- Form input
- Modal visibility
- Simple UI toggles

Use:

- `useState`
- `useReducer`
- Context API

for local concerns.

---

# Example

### Create a Vite project

```bash
npm create vite@latest redux-demo -- --template react-ts
cd redux-demo

npm install
npm install @reduxjs/toolkit react-redux

npm run dev
```

### store.ts

```tsx
import { configureStore, createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value++;
    },
  },
});

export const { increment } = counterSlice.actions;

export const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### main.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { Provider } from "react-redux";
import App from "./App";
import { store } from "./store";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <Provider store={store}>
    <App />
  </Provider>,
);
```

### App.tsx

```tsx
import { useDispatch, useSelector } from "react-redux";
import type { RootState, AppDispatch } from "./store";
import { increment } from "./store";

export default function App() {
  const count = useSelector((state: RootState) => state.counter.value);
  const dispatch = useDispatch<AppDispatch>();

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
    </div>
  );
}
```

Flow:

```
Button Click
      ↓
dispatch(increment())
      ↓
Reducer updates state
      ↓
Store changes
      ↓
useSelector detects update
      ↓
Component re-renders
```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript** for fast startup, native ESM support, and an excellent developer experience. Avoid Create React App (CRA), as it is deprecated.

- **Bundler:** Vite (powered by esbuild for development and Rollup for production).
- **ESM vs CommonJS:** Prefer ES Modules (`import`/`export`) for modern React projects. Vite is ESM-first.
- **Alternatives:** Use **Next.js** (App Router) for SSR/SSG/Server Components or **Remix** for nested routing and web-standard APIs. Turbopack is an emerging option in the Next.js ecosystem.

---

# Performance

- Use **`useSelector`** with selectors that return the smallest possible slice of state to minimize re-renders.
- Memoize derived state with memoized selectors (e.g., Reselect) instead of computing expensive values in components.
- Use `React.memo` for presentational components receiving stable props.
- Use `useMemo` and `useCallback` only when they prevent measurable work or stabilize props passed to memoized children.
- Split large features with `React.lazy()` and dynamic imports.
- For server state (API caching), prefer **RTK Query** or libraries like TanStack Query rather than storing request lifecycle manually in Redux.
- Profile updates using the React DevTools **Profiler** and Redux DevTools to inspect dispatched actions and state transitions.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests, and **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example command:

```bash
npx vitest
```

Typical Redux tests include:

- Reducer logic
- Slice actions
- Components wrapped with a test `Provider`
- Integration tests that verify UI updates after dispatching actions

---

# Ops & Deployment

- Enable Redux DevTools only in development.
- Use React **Error Boundaries** to isolate rendering failures (Redux does not handle render errors).
- Choose **CSR** for highly interactive SPAs and **SSR/Streaming** (e.g., Next.js) when SEO or faster initial content is important.
- Keep the Redux store focused on shared client state; avoid storing large transient datasets unnecessarily.
- Optimize bundle size with route-based code splitting and lazy-loaded Redux slices where appropriate.
- Deploy static Vite builds to a CDN or use edge-enabled frameworks like Next.js for SSR workloads.

---

# Pitfalls

- **Don't put every piece of state into Redux.** Keep local UI state in `useState` or `useReducer`.
- **Avoid selecting the entire store** with `useSelector`; subscribe only to the data the component needs.
- **Don't mutate state outside Redux Toolkit reducers.** RTK uses Immer to allow "mutating" syntax safely inside reducers only.

## Question 2. How do you implement Redux selectors and memoization?

# How do you implement Redux selectors and memoization?

## Short answer

Redux selectors are functions that read data from the Redux store. For simple lookups, use plain selectors. For derived or computationally expensive data, use **memoized selectors** with **Reselect** (included in Redux Toolkit) using `createSelector()`. Memoized selectors only recompute when their input values change, reducing unnecessary work and improving rendering performance.

---

# Explanation

A **selector** encapsulates how state is read from the Redux store.

Instead of scattering state access throughout components:

```tsx
const todos = useSelector((state: RootState) => state.todos.items);
```

you centralize it:

```tsx
const todos = useSelector(selectTodos);
```

This provides:

- Better code reuse
- Easier refactoring
- Better TypeScript support
- Encapsulation of state shape

---

## Types of Selectors

### 1. Basic Selector

Simply returns part of the state.

```ts
export const selectTodos = (state: RootState) => state.todos.items;
```

No memoization is needed because there is no computation.

---

### 2. Derived Selector

Computes new data.

Example:

```text
Todos
 ↓
Filter completed
 ↓
Sort
 ↓
Count
```

Without memoization:

```tsx
const completed = todos.filter((todo) => todo.completed);
```

runs every time the component renders.

For large datasets, this becomes expensive.

---

## Memoized Selectors with `createSelector`

Use `createSelector` from Redux Toolkit (re-exported from Reselect):

```ts
createSelector(inputSelectors, resultFunction);
```

Example:

```ts
const selectCompletedTodos = createSelector([selectTodos], (todos) =>
  todos.filter((todo) => todo.completed),
);
```

Workflow:

```text
State Changed
      │
      ▼
Input Selector
      │
      ▼
Input Changed?
   │         │
  No        Yes
   │         │
Return      Recompute
Cached      Result
Value
```

If `todos` hasn't changed by reference, the cached result is returned.

---

## How Memoization Works

Suppose:

```ts
todos = [...]
```

First render:

```text
Filter executes
```

Second render:

```text
Same todos reference
↓
Cached value returned
```

Third render:

```text
User adds todo
↓
New array reference
↓
Filter executes again
```

Only actual changes trigger recomputation.

---

## React Rendering Behavior

`useSelector` subscribes to the Redux store.

When an action is dispatched:

```text
Dispatch
    ↓
Store updates
    ↓
Selector executes
    ↓
Compare previous result
    ↓
Changed?
    ↓
Component re-renders
```

If a memoized selector returns the same cached reference, React Redux can avoid unnecessary re-renders caused by derived data changing identity.

---

## React 18 Considerations

React 18 introduced:

- Concurrent rendering
- Automatic batching
- Improved scheduling

Selectors should remain:

- Pure
- Deterministic
- Free of side effects

Never perform:

- API calls
- Logging with side effects
- State mutations

inside selectors.

---

## When to Memoize

**Use memoization for:**

- Filtering large arrays
- Sorting data
- Aggregations
- Grouping
- Expensive calculations
- Derived dashboards
- Search results

**Do not memoize:**

```ts
state.user.name;
```

or

```ts
state.theme;
```

Simple property lookups are already very fast.

---

# Example

### Create a Vite project

```bash
npm create vite@latest redux-selectors-demo -- --template react-ts
cd redux-selectors-demo
npm install
npm install @reduxjs/toolkit react-redux
npm run dev
```

### `store.ts`

```tsx
import { configureStore, createSlice, createSelector } from "@reduxjs/toolkit";

type Todo = {
  id: number;
  text: string;
  completed: boolean;
};

const todosSlice = createSlice({
  name: "todos",
  initialState: {
    items: [
      { id: 1, text: "Learn Redux", completed: true },
      { id: 2, text: "Learn React", completed: false },
    ] as Todo[],
  },
  reducers: {},
});

export const store = configureStore({
  reducer: {
    todos: todosSlice.reducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;

// Basic selector
export const selectTodos = (state: RootState) => state.todos.items;

// Memoized selector
export const selectCompletedTodos = createSelector([selectTodos], (todos) =>
  todos.filter((todo) => todo.completed),
);
```

### `App.tsx`

```tsx
import { useSelector } from "react-redux";
import { selectCompletedTodos } from "./store";

export default function App() {
  const completedTodos = useSelector(selectCompletedTodos);

  return (
    <ul>
      {completedTodos.map((todo) => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}
```

Flow:

```text
Dispatch Action
      ↓
Redux Store Updates
      ↓
selectTodos()
      ↓
createSelector()
      ↓
Input Changed?
   │          │
  No         Yes
   │          │
Return      Recompute
Cache       Filter
   │
useSelector()
   │
React Re-renders (only if selected result changed)
```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid Create React App (CRA), as it is deprecated.

- **Redux Toolkit** includes and re-exports `createSelector`, so you typically don't need to install Reselect separately.
- **Bundler:** Vite for fast HMR and optimized production builds.
- **ESM vs CommonJS:** Use modern ES Modules (`import`/`export`). Vite is ESM-first.
- **Framework options:** Use **Next.js** for SSR/Server Components or **Remix** for nested routing and progressive enhancement when required.

---

# Performance

- Memoize **derived data**, not simple state access.
- Keep selectors small and composable by building complex selectors from simpler ones.
- Use `React.memo` for child components receiving derived data.
- Stabilize callback props with `useCallback` only when necessary.
- Use `useMemo` for component-local expensive computations that are unrelated to Redux state.
- Split large bundles with `React.lazy()` and route-level code splitting.
- Use **React DevTools Profiler** to identify expensive renders and **Redux DevTools** to inspect dispatched actions and state updates.
- For normalized collections, memoized selectors work well with entity adapters (`createEntityAdapter`) to efficiently derive filtered or sorted views.

---

# Testing

Use **Vitest** and **React Testing Library** for unit and integration tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example selector test:

```ts
import { describe, expect, it } from "vitest";
import { selectCompletedTodos } from "./store";

it("returns only completed todos", () => {
  const state = {
    todos: {
      items: [
        { id: 1, text: "A", completed: true },
        { id: 2, text: "B", completed: false },
      ],
    },
  };

  expect(selectCompletedTodos(state as any)).toHaveLength(1);
});
```

Also test that selectors return expected derived values and remain pure for identical inputs.

---

# Ops & Deployment

- Keep selectors in feature folders (e.g., `features/todos/selectors.ts`) to separate state access from UI.
- Avoid creating selectors inside React components, as doing so resets memoization on every render.
- For parameterized selectors (e.g., by ID), use selector factories or carefully structured selectors to preserve memoization.
- Monitor render frequency with React Profiler and Redux DevTools before optimizing.
- Keep Redux focused on shared client state; use dedicated libraries like RTK Query for server-state caching.

---

# Pitfalls

- **Memoizing every selector unnecessarily.** Use `createSelector` only for derived or expensive computations.
- **Creating new arrays or objects inside `useSelector`.** This causes new references and unnecessary re-renders unless memoized.
- **Defining memoized selectors inside components.** Create them outside the component (or use selector factories when per-instance memoization is required).

## Question 3. How do you implement asynchronous Redux actions using Thunk or Saga?

## Question 4. How do you integrate React with GraphQL?

## Question 5. How do you handle caching with Apollo Client?

## Question 6. How do you implement WebSocket-based real-time updates?

## Question 7. How do you implement offline-first React apps?

## Question 8. How do you implement service workers in React apps?

## Question 9. How do you monitor React app performance with React Profiler?

## Question 10. How do you debug memory leaks in React?

## Question 11. How do you prevent unnecessary network requests in React?

## Question 12. How do you implement drag-and-drop with React DnD or similar libraries?

## Question 13. How do you implement dynamic component rendering based on JSON configuration?

## Question 14. How do you implement advanced form validation with Yup and Formik?

## Question 15. How do you implement internationalization (i18n) in React apps?

## Question 16. How do you implement micro-frontends using React?

## Question 17. How do you handle versioning and backward compatibility in React components?

## Question 18. How do you integrate React with legacy jQuery code?

## Question 19. How do you implement analytics tracking in React applications?

## Question 20. How do you optimize React app bundle size using tree shaking and webpack?
