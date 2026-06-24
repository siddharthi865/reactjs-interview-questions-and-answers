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

# How do you implement asynchronous Redux actions using Thunk or Saga?

## Short answer

Asynchronous Redux actions are implemented using middleware because Redux reducers must remain **pure and synchronous**. The two most common approaches are:

- **Redux Thunk**: Write async logic inside action creators (recommended for most applications and included by default in Redux Toolkit).
- **Redux Saga**: Handle async workflows using generator functions (`function*`) for complex side effects like retries, polling, cancellation, and orchestration.

For modern React applications, **Redux Toolkit + `createAsyncThunk` or RTK Query** is the preferred solution unless your application has highly complex side-effect workflows.

---

# Explanation

Redux follows a unidirectional data flow:

```text
Component
    │
dispatch()
    │
    ▼
Middleware (Thunk/Saga)
    │
    ▼
API Call
    │
    ▼
Success / Failure Action
    │
    ▼
Reducer
    │
    ▼
Updated Store
    │
    ▼
React Re-renders
```

Reducers should never perform:

- API requests
- Timers
- Local storage writes
- Navigation
- Logging with side effects

These belong in middleware.

---

## Option 1: Redux Thunk

Thunk allows an action creator to return a **function** instead of a plain object.

Example:

```ts
dispatch(fetchUsers());
```

Internally:

```text
dispatch()
      ↓
Thunk Middleware
      ↓
Execute async function
      ↓
API request
      ↓
Dispatch pending
      ↓
Dispatch fulfilled
      ↓
Reducer updates state
```

Redux Toolkit simplifies this with `createAsyncThunk`.

Example lifecycle:

```text
fetchUsers.pending

↓

fetchUsers.fulfilled

↓

fetchUsers.rejected
```

Benefits:

- Easy to learn
- Minimal boilerplate
- Great TypeScript support
- Built into Redux Toolkit
- Best choice for most CRUD applications

---

## Option 2: Redux Saga

Saga uses **generator functions**.

Example flow:

```text
dispatch(fetchUsers)

↓

watcherSaga

↓

workerSaga

↓

call(API)

↓

put(success)

↓

Reducer
```

Common Saga effects:

```ts
takeLatest();

takeEvery();

call();

put();

select();

delay();

race();

cancel();

fork();
```

Saga excels when coordinating complex asynchronous workflows.

Example scenarios:

- Authentication flows
- WebSockets
- Background synchronization
- Retry strategies
- Request cancellation
- Polling
- Multiple dependent API calls

---

## Thunk vs Saga

| Feature                   | Thunk   | Saga                            |
| ------------------------- | ------- | ------------------------------- |
| Learning curve            | Easy    | Steeper                         |
| Uses async/await          | ✅      | ❌ (Generators)                 |
| Boilerplate               | Low     | Higher                          |
| Complex workflows         | Limited | Excellent                       |
| Request cancellation      | Manual  | Built-in                        |
| Parallel execution        | Manual  | Excellent                       |
| Official RTK support      | ✅      | External middleware             |
| Recommended for most apps | ✅      | Only when workflows are complex |

---

## React 18 Rendering Behavior

With React 18:

- Automatic batching reduces unnecessary renders.
- React Redux integrates with concurrent rendering.
- Multiple state updates triggered by async actions are batched where appropriate.

Example:

```ts
dispatch(fetchUsers());
dispatch(fetchPosts());
```

React minimizes re-renders when these updates occur within the same batching context.

---

# Example

### Create a Vite project

```bash
npm create vite@latest redux-async-demo -- --template react-ts
cd redux-async-demo

npm install
npm install @reduxjs/toolkit react-redux

npm run dev
```

### `store.ts` (Redux Toolkit + Thunk)

```tsx
import {
  configureStore,
  createSlice,
  createAsyncThunk,
} from "@reduxjs/toolkit";

type User = {
  id: number;
  name: string;
};

export const fetchUsers = createAsyncThunk<User[]>(
  "users/fetchUsers",
  async () => {
    const response = await fetch("https://jsonplaceholder.typicode.com/users");
    return response.json();
  },
);

const usersSlice = createSlice({
  name: "users",
  initialState: {
    users: [] as User[],
    loading: false,
    error: "",
  },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.loading = false;
        state.users = action.payload;
      })
      .addCase(fetchUsers.rejected, (state) => {
        state.loading = false;
        state.error = "Failed to load users";
      });
  },
});

export const store = configureStore({
  reducer: {
    users: usersSlice.reducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### `App.tsx`

```tsx
import { useEffect } from "react";
import { useDispatch, useSelector } from "react-redux";
import { fetchUsers } from "./store";
import type { AppDispatch, RootState } from "./store";

export default function App() {
  const dispatch = useDispatch<AppDispatch>();
  const { users, loading } = useSelector((state: RootState) => state.users);

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  if (loading) return <p>Loading...</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

Flow:

```text
Component Mount
      │
dispatch(fetchUsers())
      │
      ▼
pending
      │
      ▼
HTTP Request
      │
 ┌────┴────┐
 │         │
Success   Failure
 │         │
fulfilled rejected
 │         │
Reducer Updates State
      │
      ▼
React Re-renders
```

> **Saga equivalent (conceptual):**
>
> ```ts
> function* fetchUsersWorker() {
>   const users = yield call(api.fetchUsers);
>   yield put(usersLoaded(users));
> }
>
> function* watchUsers() {
>   yield takeLatest(fetchUsers.type, fetchUsersWorker);
> }
> ```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid Create React App (CRA), as it is deprecated.

- **Bundler:** Vite for fast HMR and optimized production builds.
- **ESM vs CommonJS:** Use ES Modules (`import`/`export`) in modern React applications.
- **Redux Toolkit:** Includes Redux Thunk by default.
- **Redux Saga:** Install separately (`redux-saga`) and configure it as middleware when your application requires advanced side-effect orchestration.
- **Alternative:** For server-state fetching and caching, consider **RTK Query**, which removes much of the manual async Redux code.

---

# Performance

- Avoid dispatching duplicate requests; cache or deduplicate where appropriate.
- Prefer **RTK Query** for API-heavy applications because it provides caching, request deduplication, automatic refetching, and cache invalidation.
- Memoize derived state with `createSelector`.
- Use `React.memo`, `useMemo`, and `useCallback` where profiling shows measurable benefits.
- Code split large feature modules with `React.lazy()` and dynamic imports.
- Use the React DevTools **Profiler** and Redux DevTools to analyze render frequency and action timelines.

---

# Testing

Use **Vitest** with **React Testing Library** for components and **Mock Service Worker (MSW)** to mock network requests during integration tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom msw
```

Example command:

```bash
npx vitest
```

Typical async tests verify:

- Pending state
- Successful responses
- Error handling
- Loading indicators
- Reducer state after fulfilled/rejected actions

---

# Ops & Deployment

- Centralize API clients and error handling to avoid duplicating request logic.
- Log async failures through an observability platform (e.g., Sentry) rather than only displaying UI errors.
- Use Error Boundaries for rendering errors; async errors should be handled in middleware or async thunks.
- For SSR frameworks like Next.js, consider where data should be fetched (server vs. client) to reduce client-side waterfalls.
- Monitor bundle size—if using Redux Saga, remember it adds additional runtime overhead compared to Thunk.

---

# Pitfalls

- **Performing async work inside reducers.** Reducers must always remain pure and synchronous.
- **Using Redux Saga for simple CRUD operations.** `createAsyncThunk` or RTK Query is usually simpler and easier to maintain.
- **Ignoring loading and error states.** Always model request lifecycle (`pending`, `fulfilled`, `rejected`) for a better user experience.

## Question 4. How do you integrate React with GraphQL?

# How do you integrate React with GraphQL?

## Short answer

React integrates with GraphQL primarily using a client like **Apollo Client**, **Relay**, or **urql**. The most common approach is **Apollo Client + React hooks (`useQuery`, `useMutation`)**, which manages caching, networking, and state synchronization between the GraphQL server and React UI.

---

# Explanation

GraphQL integration in React replaces traditional REST calls with **declarative data fetching**:

```text id="gql_flow_1"
React Component
      ↓
GraphQL Query (useQuery)
      ↓
GraphQL Client (Apollo / Relay / urql)
      ↓
GraphQL Server
      ↓
Normalized Cache
      ↓
React re-renders automatically
```

Unlike REST:

- You fetch **exact data you need**
- You avoid over-fetching/under-fetching
- You rely heavily on **client caching**
- UI is tightly coupled with data requirements

---

## Core Concepts in React + GraphQL

### 1. Query (Read data)

```graphql id="gql_q1"
query GetUsers {
  users {
    id
    name
  }
}
```

### 2. Mutation (Write data)

```graphql id="gql_m1"
mutation AddUser {
  addUser(name: "John") {
    id
    name
  }
}
```

### 3. Subscription (Real-time updates)

```graphql id="gql_s1"
subscription OnUserAdded {
  userAdded {
    id
    name
  }
}
```

---

## Apollo Client Architecture (Most common)

```text id="apollo_arch"
React UI
   ↓
Apollo Hooks
   ↓
Apollo Client
   ↓
GraphQL Server
   ↓
Normalized Cache (InMemoryCache)
   ↓
UI updates automatically
```

Key features:

- Intelligent caching (normalized)
- Automatic request deduplication
- Pagination support
- SSR support (Next.js)
- DevTools integration

---

# React 18 Behavior with GraphQL

With React 18:

- Concurrent rendering improves perceived loading UX
- Suspense can delay rendering until GraphQL data is ready
- Automatic batching reduces re-renders from multiple cache updates

Apollo + React can integrate with:

- **Suspense mode (experimental/modern setups)**
- Streaming SSR in frameworks like Next.js

---

# Example (Apollo Client + React + TypeScript)

## Create project (Vite recommended)

```bash id="setup_graphql"
npm create vite@latest graphql-react -- --template react-ts
cd graphql-react

npm install
npm install @apollo/client graphql
npm run dev
```

---

## 1. Setup Apollo Client

```ts id="apollo_client"
import { ApolloClient, InMemoryCache, ApolloProvider } from "@apollo/client";

export const client = new ApolloClient({
  uri: "https://example.com/graphql",
  cache: new InMemoryCache(),
});
```

---

## 2. Wrap App with Provider

```tsx id="apollo_provider"
import React from "react";
import ReactDOM from "react-dom/client";
import { ApolloProvider } from "@apollo/client";
import App from "./App";
import { client } from "./apolloClient";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
);
```

---

## 3. Query data using `useQuery`

```tsx id="use_query_example"
import { gql, useQuery } from "@apollo/client";

const GET_USERS = gql`
  query GetUsers {
    users {
      id
      name
    }
  }
`;

type User = {
  id: string;
  name: string;
};

export default function App() {
  const { data, loading, error } = useQuery(GET_USERS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error occurred</p>;

  return (
    <ul>
      {data.users.map((user: User) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## 4. Mutation using `useMutation`

```tsx id="mutation_example"
import { gql, useMutation } from "@apollo/client";

const ADD_USER = gql`
  mutation AddUser($name: String!) {
    addUser(name: $name) {
      id
      name
    }
  }
`;

export default function AddUserButton() {
  const [addUser, { loading }] = useMutation(ADD_USER);

  const handleClick = async () => {
    await addUser({
      variables: { name: "Alice" },
      refetchQueries: ["GetUsers"], // simple cache update strategy
    });
  };

  return (
    <button onClick={handleClick} disabled={loading}>
      Add User
    </button>
  );
}
```

---

## 5. Cache-based update (preferred over refetch)

Instead of refetching:

```ts id="cache_update"
update(cache, { data }) {
  cache.modify({
    fields: {
      users(existingUsers = []) {
        return [...existingUsers, data.addUser];
      },
    },
  });
}
```

This avoids network round-trips and improves UX.

---

# State Management Trade-offs

GraphQL changes how you think about state:

### Server state (GraphQL handles this)

- Users
- Posts
- Products
- API data
- Caching
- Syncing

### Client state (React/Redux/Zustand)

- Modal open/close
- Theme
- Local UI state
- Form inputs

👉 Best practice:

> Do NOT store GraphQL server data in Redux unless necessary.

Instead:

- Apollo Client cache replaces Redux for server state
- Redux/Zustand remains for UI state only

---

# Tooling & Setup

## Recommended Stack

### 1. Apollo Client (most popular)

- Best caching system
- Large ecosystem
- Works well with React + Next.js

### 2. urql (lightweight alternative)

- Smaller bundle
- Flexible exchanges
- Good for simple apps

### 3. Relay (Meta)

- Highly optimized
- Strict conventions
- Best for large-scale apps with complex data graphs

---

## Vite vs Next.js

### Vite (CSR)

- Fast development
- Simple SPA apps
- No SSR by default

### Next.js (recommended for production GraphQL apps)

- SSR / SSG / ISR
- Better SEO
- Server Components
- Streaming support

---

# Performance

### 1. Normalize cache (critical)

Apollo uses:

```text id="norm_cache"
__typename + id
```

This avoids duplicate fetches and enables automatic updates.

---

### 2. Avoid over-fetching queries

Only request needed fields:

```graphql id="bad_good"
# BAD
query {
  users {
    id
    name
    email
    address
    posts { ... }
  }
}

# GOOD
query {
  users {
    id
    name
  }
}
```

---

### 3. Use pagination strategies

- Cursor-based pagination (preferred)
- Offset-based pagination (simple but less scalable)

---

### 4. Optimize re-renders

- `React.memo` for list components
- Split queries into smaller fragments
- Use `@client` fields carefully (Apollo local state)

---

### 5. Use Suspense (advanced)

Apollo supports experimental Suspense:

```tsx id="suspense"
const { data } = useSuspenseQuery(GET_USERS);
```

This improves loading orchestration in concurrent React.

---

# Testing

Use:

- Vitest or Jest
- React Testing Library
- MockedProvider (Apollo)

Install:

```bash id="test_setup"
npm install -D vitest @testing-library/react @apollo/client @testing-library/jest-dom
```

Example:

```tsx id="mock_apollo_test"
import { MockedProvider } from "@apollo/client/testing";

const mocks = [
  {
    request: {
      query: GET_USERS,
    },
    result: {
      data: {
        users: [{ id: "1", name: "Test User" }],
      },
    },
  },
];
```

Run:

```bash id="test_run"
npx vitest
```

---

# Ops & Deployment

- Use persisted queries to reduce payload size
- Enable CDN caching for static queries
- Use GraphQL gateway (Apollo Federation) for microservices
- Monitor query complexity (prevent expensive nested queries)
- Use error boundaries for UI-level GraphQL failures
- Use environment-based endpoints (`.env`) for dev/staging/prod

---

# Pitfalls

- Storing GraphQL server data in Redux unnecessarily
- Over-fetching deeply nested fields
- Not handling cache invalidation correctly after mutations
- Ignoring loading/error states in UI
- Creating too many small Apollo clients instead of a single shared instance

## Question 5. How do you handle caching with Apollo Client?

# How do you handle caching with Apollo Client?

## Short answer

Apollo Client handles caching using a **normalized in-memory cache (`InMemoryCache`)**. You control caching behavior using **cache policies (`fetchPolicy`)**, **cache updates after mutations**, and **manual cache manipulation (`cache.readQuery`, `cache.writeQuery`, `cache.modify`)**. The goal is to avoid unnecessary network requests while keeping UI data consistent.

---

# Explanation

Apollo’s caching system is one of its most important features. It works in three layers:

```text id="cache_flow"
GraphQL Server
      ↓
Apollo Client Network Layer
      ↓
InMemoryCache (Normalized Cache)
      ↓
React UI (useQuery/useMutation)
```

## 1. Normalized Cache (Core Concept)

Apollo stores data like a database instead of raw responses.

Example response:

```json id="cache_example"
{
  "users": [
    { "id": "1", "name": "A" },
    { "id": "2", "name": "B" }
  ]
}
```

Stored internally as:

```text id="normalized_cache"
User:1 → { id: "1", name: "A" }
User:2 → { id: "2", name: "B" }
Query.users → [User:1, User:2]
```

### Why normalization matters:

- Prevents duplicate data
- Enables automatic UI updates
- Efficient mutation propagation
- Shared references across queries

---

## 2. Cache Policies (`fetchPolicy`)

Apollo controls when to use cache vs network.

### Common policies:

| Policy              | Behavior                          |
| ------------------- | --------------------------------- |
| `cache-first`       | Uses cache if available (default) |
| `network-only`      | Always fetch from server          |
| `cache-and-network` | Cache first, then refresh         |
| `no-cache`          | Always bypass cache               |
| `standby`           | Skip automatic updates            |

Example:

```ts id="fetch_policy"
useQuery(GET_USERS, {
  fetchPolicy: "cache-first",
});
```

---

## 3. Reading & Writing Cache Manually

### Read from cache

```ts id="read_cache"
const data = client.readQuery({
  query: GET_USERS,
});
```

---

### Write to cache

```ts id="write_cache"
client.writeQuery({
  query: GET_USERS,
  data: {
    users: [{ id: "3", name: "New User" }],
  },
});
```

---

## 4. Updating Cache After Mutations

This is the most important real-world use case.

### Option 1: `refetchQueries` (simple but less efficient)

```ts id="refetch_mutation"
useMutation(ADD_USER, {
  refetchQueries: ["GetUsers"],
});
```

✔ Easy
❌ Extra network call

---

### Option 2: Manual cache update (preferred)

```ts id="cache_modify"
update(cache, { data: { addUser } }) {
  cache.modify({
    fields: {
      users(existingUsers = []) {
        const newUserRef = cache.writeFragment({
          data: addUser,
          fragment: gql`
            fragment NewUser on User {
              id
              name
            }
          `,
        });

        return [...existingUsers, newUserRef];
      },
    },
  });
}
```

✔ No network call
✔ Fast UI updates
✔ Best practice

---

### Option 3: Direct `cache.writeQuery`

```ts id="write_query_update"
update(cache, { data: { addUser } }) {
  const existing = cache.readQuery({ query: GET_USERS });

  cache.writeQuery({
    query: GET_USERS,
    data: {
      users: [...existing.users, addUser],
    },
  });
}
```

---

## 5. Cache Invalidation Strategy

Apollo does NOT auto-invalidate like React Query. You must design updates.

### Common strategies:

### A. Normalized ID-based updates (best)

If your schema includes:

```graphql
id
__typename
```

Apollo automatically updates related queries.

---

### B. Manual field eviction

```ts id="evict_cache"
cache.evict({ id: "User:1" });
cache.gc();
```

---

### C. Field-level modification

```ts id="cache_field_update"
cache.modify({
  fields: {
    users(existing) {
      return existing.filter((userRef) => userRef.__ref !== "User:1");
    },
  },
});
```

---

## 6. Type Policies (Advanced Cache Control)

Define how Apollo identifies and merges data:

```ts id="type_policies"
const cache = new InMemoryCache({
  typePolicies: {
    User: {
      keyFields: ["id"],
    },
    Query: {
      fields: {
        users: {
          merge(existing = [], incoming) {
            return [...existing, ...incoming];
          },
        },
      },
    },
  },
});
```

### Why this matters:

- Controls pagination merging
- Avoids duplicate lists
- Enables fine-grained cache behavior

---

## 7. Pagination Caching

Apollo supports cursor-based pagination using `merge`.

```ts id="pagination_cache"
fields: {
  posts: {
    keyArgs: false,
    merge(existing = [], incoming) {
      return [...existing, ...incoming];
    },
  },
}
```

Used for:

- Infinite scroll
- Feed loading
- Chat messages

---

## React 18 Behavior with Apollo Cache

- Cache updates trigger **batched re-renders**
- Multiple query updates are grouped (automatic batching)
- Only components using affected cache references re-render

```text id="react_cache_render"
Mutation
  ↓
Cache update
  ↓
Reactive query invalidation
  ↓
Minimal component re-render
```

---

## Example (Full Setup)

### Create project

```bash id="apollo_cache_setup"
npm create vite@latest apollo-cache-demo -- --template react-ts
cd apollo-cache-demo

npm install
npm install @apollo/client graphql
npm run dev
```

---

### Apollo Client setup

```ts id="apollo_cache_client"
import { ApolloClient, InMemoryCache } from "@apollo/client";

export const client = new ApolloClient({
  uri: "https://example.com/graphql",
  cache: new InMemoryCache({
    typePolicies: {
      Query: {
        fields: {
          users: {
            merge(existing = [], incoming) {
              return [...incoming];
            },
          },
        },
      },
    },
  }),
});
```

---

### Mutation with cache update

```ts id="mutation_cache_update"
const ADD_USER = gql`
  mutation AddUser($name: String!) {
    addUser(name: $name) {
      id
      name
    }
  }
`;

const [addUser] = useMutation(ADD_USER, {
  update(cache, { data }) {
    cache.modify({
      fields: {
        users(existing = []) {
          return [...existing, data.addUser];
        },
      },
    });
  },
});
```

---

# Performance Considerations

- Prefer **normalized cache updates** over refetching
- Avoid deeply nested cache writes when possible
- Use `cache.modify` instead of full `writeQuery` for large datasets
- Use `cache-first` for static data, `network-only` for real-time sensitive data
- Split large queries into fragments to improve cache reuse
- Use pagination merge functions to avoid memory bloat
- Monitor cache size in Apollo DevTools

---

# Testing

Use **MockedProvider** from Apollo Client testing utilities.

```bash id="apollo_test_install"
npm install -D @apollo/client @testing-library/react vitest
```

Example:

```ts id="cache_test"
import { MockedProvider } from "@apollo/client/testing";

const mocks = [
  {
    request: { query: GET_USERS },
    result: {
      data: {
        users: [{ id: "1", name: "Test" }],
      },
    },
  },
];
```

Test cache updates by:

- Triggering mutations
- Asserting UI re-renders
- Verifying cache state via `client.cache.extract()`

---

# Ops & Deployment

- Enable Apollo DevTools for cache inspection
- Use persisted queries to reduce payload size
- Implement cache persistence (localStorage) for offline support
- Configure error handling for stale cache states
- Use CDN caching at GraphQL gateway level where appropriate
- Avoid overly aggressive caching for real-time data (use `no-cache` or `network-only`)

---

# Pitfalls

- Not including `id` and `__typename` → breaks normalization
- Overusing `refetchQueries` instead of cache updates
- Mutating cache incorrectly (breaking referential integrity)
- Not defining `typePolicies` for pagination
- Storing UI state in Apollo cache instead of React state

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
