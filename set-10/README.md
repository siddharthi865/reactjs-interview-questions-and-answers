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
