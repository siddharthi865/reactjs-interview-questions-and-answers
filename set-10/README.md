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

# How do you implement WebSocket-based real-time updates?

## Short answer

WebSocket-based real-time updates in React are typically implemented by establishing a persistent WebSocket connection (or using libraries like **Socket.IO**), managing the connection lifecycle with `useEffect`, storing received messages in state (or Redux/Zustand), and cleaning up the connection on component unmount to prevent memory leaks.

For large applications, encapsulate WebSocket logic in a **custom hook** or service layer, and integrate it with a global state manager if multiple components consume the same real-time data.

---

# Explanation

Unlike HTTP, which follows a request-response model, WebSockets provide a **persistent, bidirectional connection**.

```text
React Component
      │
      ▼
Custom Hook / Service
      │
      ▼
WebSocket Client
      │
 Persistent Connection
      │
      ▼
WebSocket Server
      │
      ▼
Real-time Events
      │
      ▼
State Update
      │
      ▼
React Re-renders
```

Common use cases:

- Chat applications
- Live dashboards
- Stock prices
- Sports scores
- Notifications
- Collaborative editors
- Multiplayer games

---

## WebSocket Lifecycle

```text
Create Connection
      │
      ▼
Connected
      │
      ▼
Receive Messages
      │
      ▼
Update State
      │
      ▼
Component Re-renders
      │
      ▼
Close Connection (cleanup)
```

Typical browser API events:

```ts
socket.onopen;
socket.onmessage;
socket.onerror;
socket.onclose;
```

---

## React Integration

Use `useEffect` because a WebSocket is a side effect.

```tsx
useEffect(() => {
  const socket = new WebSocket(url);

  return () => socket.close();
}, []);
```

This ensures:

- One connection per component lifecycle
- Proper cleanup
- No memory leaks

---

## Managing State

### Local state

Good for:

- Single widget
- Chat component
- Live counter

```tsx
const [messages, setMessages] = useState<Message[]>([]);
```

---

### Global state

Better for:

- Notifications
- User presence
- Live dashboards
- Shared chat

Common choices:

- Redux Toolkit
- Zustand
- Context API (small apps)

---

## Reconnection Strategy

Production applications should handle:

```text
Disconnected
      │
      ▼
Wait
      │
      ▼
Reconnect
      │
      ▼
Connected
```

Use:

- Exponential backoff
- Retry limits
- Heartbeats (ping/pong)
- Connection status indicators

---

## React 18 Rendering Behavior

React 18 automatically batches state updates where appropriate.

Instead of:

```text
Message
↓

Render

Message

↓

Render
```

Multiple updates received in the same task may be batched, reducing unnecessary renders.

For very high-frequency streams (e.g., telemetry), consider buffering updates before setting state.

---

# Example

## Create a Vite project

```bash
npm create vite@latest websocket-demo -- --template react-ts
cd websocket-demo

npm install

npm run dev
```

---

### `App.tsx`

```tsx
import { useEffect, useState } from "react";

export default function App() {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const socket = new WebSocket("ws://localhost:8080");

    socket.onopen = () => {
      console.log("Connected");
    };

    socket.onmessage = (event) => {
      setMessages((prev) => [...prev, event.data]);
    };

    socket.onerror = (error) => {
      console.error("WebSocket error:", error);
    };

    socket.onclose = () => {
      console.log("Disconnected");
    };

    return () => {
      socket.close();
    };
  }, []);

  return (
    <div>
      <h2>Live Messages</h2>

      <ul>
        {messages.map((message, index) => (
          <li key={index}>{message}</li>
        ))}
      </ul>
    </div>
  );
}
```

Flow:

```text
Server
   │
Send Message
   │
   ▼
WebSocket
   │
onmessage
   │
   ▼
setMessages()
   │
   ▼
React State
   │
   ▼
Component Re-renders
```

---

## Custom Hook (Recommended)

Encapsulate connection logic for reuse.

```tsx
import { useEffect, useState } from "react";

export function useWebSocket(url: string) {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const socket = new WebSocket(url);

    socket.onmessage = (event) => {
      setMessages((prev) => [...prev, event.data]);
    };

    return () => socket.close();
  }, [url]);

  return messages;
}
```

Usage:

```tsx
const messages = useWebSocket("ws://localhost:8080");
```

---

## Using Socket.IO

For production systems requiring reconnection, acknowledgements, namespaces, or room support, many teams use **Socket.IO**.

```tsx
import { io } from "socket.io-client";
import { useEffect } from "react";

const socket = io("http://localhost:3000");

useEffect(() => {
  socket.on("message", (data) => {
    console.log(data);
  });

  return () => {
    socket.off("message");
    socket.disconnect();
  };
}, []);
```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid Create React App (CRA), as it is deprecated.

- **Bundler:** Vite for fast development and optimized builds.
- **ESM vs CommonJS:** Use ES Modules (`import`/`export`).
- **Native WebSocket API:** Lightweight and ideal when you control both client and server.
- **Socket.IO:** Adds reconnection, rooms, acknowledgements, and fallbacks, but requires a Socket.IO server (it is not a drop-in replacement for a standard WebSocket server).
- **Next.js:** Use API routes or a separate WebSocket server; avoid creating browser WebSocket connections in Server Components.

---

# Performance

- Create a **single shared WebSocket connection** instead of one per component.
- Buffer or debounce high-frequency updates before updating React state.
- Use `React.memo` for components rendering large message lists.
- Virtualize long lists with libraries like `react-window`.
- Memoize derived data with `useMemo`.
- Use `useCallback` for event handlers passed to memoized children.
- Profile frequent updates using the React DevTools **Profiler**.

---

# Testing

Use **Vitest** and **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Mock the WebSocket API:

```ts
class MockWebSocket {
  onmessage: ((event: MessageEvent) => void) | null = null;
  close() {}
}

(global as any).WebSocket = MockWebSocket;
```

For integration tests:

- Simulate incoming messages
- Verify UI updates
- Test cleanup on unmount
- Verify reconnection logic if implemented

---

# Ops & Deployment

- Use **WSS (`wss://`)** in production for encrypted communication.
- Authenticate connections using JWTs, cookies, or secure tokens during the handshake.
- Implement heartbeat (ping/pong) to detect stale connections.
- Log connection events and failures for observability.
- Add exponential backoff for reconnection attempts.
- Scale with pub/sub systems (e.g., Redis) when multiple WebSocket servers need to broadcast the same events.

---

# Pitfalls

- **Forgetting to close the socket in `useEffect` cleanup**, leading to memory leaks and duplicate connections.
- **Creating a new WebSocket on every render** by omitting or misconfiguring the dependency array.
- **Updating React state for every high-frequency message**, causing excessive re-renders; batch or buffer updates when necessary.

## Question 7. How do you implement offline-first React apps?

# Short answer

An **offline-first React app** is designed to work even when the network is unavailable. The core approach is to use a **Service Worker** for caching static assets and API responses, store application data locally (IndexedDB, Cache API, or local storage), queue writes while offline, and synchronize them with the server once connectivity is restored. Modern React apps typically use **Vite + vite-plugin-pwa**, **Next.js PWA plugins**, **Workbox**, **IndexedDB (Dexie/localForage)**, and **React Query or Apollo** for intelligent caching.

---

# Explanation

Offline-first architecture means the application prioritizes **local data** over network requests. Instead of waiting for the server, the UI loads immediately from local storage and synchronizes in the background.

Typical architecture:

```
React UI
    │
    ▼
State (Redux / React Query / Context)
    │
    ▼
IndexedDB (Offline Storage)
    │
    ├── Read immediately
    │
    ▼
Background Sync
    │
    ▼
REST API / GraphQL Server
```

Major building blocks:

### 1. Service Worker

A Service Worker runs separately from React.

Responsibilities:

- Cache JS/CSS/images
- Cache API responses
- Serve cached content offline
- Perform Background Sync
- Push notifications

Typical caching strategies:

| Strategy               | Use Case       |
| ---------------------- | -------------- |
| Cache First            | Images, fonts  |
| Network First          | User profile   |
| Stale While Revalidate | API lists      |
| Network Only           | Authentication |
| Cache Only             | Static assets  |

---

### 2. Local Database

Avoid LocalStorage for real applications.

Preferred:

- IndexedDB
- Dexie
- localForage

Example stored data:

```
Todos

[
  {
    id: 1,
    title: "Buy milk",
    synced: true
  },
  {
    id: 2,
    title: "Meeting",
    synced: false
  }
]
```

---

### 3. Sync Queue

When offline:

```
POST /todos
```

cannot reach server.

Instead:

```
Pending Queue

[
 {
   action: "CREATE_TODO",
   payload: {...}
 }
]
```

When internet returns:

```
Queue
   ↓
Replay Requests
   ↓
Server Updated
   ↓
Remove Queue Item
```

---

### 4. Detect Online Status

```
navigator.onLine
```

or

```
window.addEventListener("online")
window.addEventListener("offline")
```

---

### 5. Conflict Resolution

Suppose:

Offline:

```
Todo title = A
```

Meanwhile another user changes it:

```
Todo title = B
```

Choose strategy:

- Last write wins
- Merge
- Server wins
- User confirmation

Large apps usually use timestamps or version numbers.

---

### 6. React Query Integration

React Query already provides:

- cache
- retries
- stale data
- background refresh
- persistence plugins

This is much easier than writing caching manually.

---

### 7. Redux Integration

Redux stores UI state.

Persistence is handled using:

- Redux Persist
- IndexedDB middleware
- Sync middleware

Flow:

```
React
   ↓
Redux
   ↓
Persist
   ↓
IndexedDB
   ↓
Server Sync
```

---

### 8. Background Sync

Modern browsers allow:

```
Offline

↓

Queue requests

↓

Internet available

↓

Service Worker sync event

↓

Send requests automatically
```

No user interaction needed.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest offline-demo -- --template react-ts
cd offline-demo
npm install
npm install dexie vite-plugin-pwa
npm run dev
```

```tsx
// App.tsx
import { useEffect, useState } from "react";

export default function App() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    const onOnline = () => setOnline(true);
    const onOffline = () => setOnline(false);

    window.addEventListener("online", onOnline);
    window.addEventListener("offline", onOffline);

    return () => {
      window.removeEventListener("online", onOnline);
      window.removeEventListener("offline", onOffline);
    };
  }, []);

  return (
    <div>
      <h2>{online ? "🟢 Online" : "🔴 Offline"}</h2>

      {!online && (
        <p>Changes will sync automatically when connection returns.</p>
      )}
    </div>
  );
}
```

For production, combine this with:

- IndexedDB (Dexie)
- Workbox
- Background Sync
- React Query persistence

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stack:

- **Vite** for fast development with native ESM and Rollup-based production builds.
- **vite-plugin-pwa** to generate and register a Service Worker automatically.
- **Workbox** for advanced runtime caching strategies and background sync.
- **Dexie** or **localForage** for IndexedDB abstraction.
- **React Query (TanStack Query)** for server-state caching and offline persistence.
- **Next.js** can also support PWAs via community plugins if SSR or App Router features are required.

**ESM vs CommonJS**

- Prefer **ES Modules (ESM)** for modern React tooling.
- Vite uses native ESM during development for fast startup and hot module replacement.

---

# Performance

Offline-first apps benefit from caching but require cache management.

Best practices:

- Profile rendering with **React DevTools Profiler**.
- Use `React.memo`, `useMemo`, and `useCallback` to reduce unnecessary renders.
- Lazy-load non-critical routes using `React.lazy` and `Suspense`.
- Cache static assets aggressively while using `Stale While Revalidate` for API responses.
- Persist React Query or Apollo caches to reduce startup latency.
- Keep IndexedDB writes batched to avoid excessive disk operations.
- Version caches and clean up outdated assets during Service Worker updates.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests, and **Playwright** for end-to-end testing.

Example commands:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test ideas:

- App renders cached data while offline.
- Queued mutations are replayed after reconnecting.
- Service Worker serves cached assets when the network is unavailable.

For browser-level offline scenarios, use Playwright to emulate offline mode and verify synchronization behavior.

---

# Ops & Deployment

Production considerations:

- Register an **Error Boundary** to handle rendering failures gracefully.
- Log synchronization failures to monitoring tools (e.g., Sentry).
- Implement retry policies with exponential backoff.
- Version Service Worker caches to avoid serving stale bundles indefinitely.
- Prefer CDN hosting for static assets while keeping API endpoints highly available.
- Evaluate **SSR vs CSR**: SSR improves initial load, while CSR with a Service Worker offers excellent offline capabilities after the first visit.
- Monitor bundle size and split large features to minimize initial downloads.

---

# Pitfalls

- **Don't store large datasets in LocalStorage**; use IndexedDB instead.
- **Don't assume cached data is current**; implement background refresh and conflict resolution.
- **Don't forget Service Worker versioning**, or users may continue running outdated code after deployments.

## Question 8. How do you implement service workers in React apps?

# Short answer

A **Service Worker** is a background script that runs separately from your React application and intercepts network requests. It enables **offline support, asset caching, faster page loads, background sync, and push notifications**. In modern React applications, Service Workers are commonly implemented using **Vite + `vite-plugin-pwa`** or **Workbox**. The Service Worker lifecycle (install → activate → fetch) should be understood, along with appropriate caching strategies and update handling.

---

# Explanation

A Service Worker acts as a programmable network proxy between the browser and your React app.

```text
Browser
    │
    ▼
Service Worker
    │
 ┌──┴───────────────┐
 │                  │
Cache API       Network
 │                  │
 └──────┬───────────┘
        ▼
     React App
```

Unlike React components, a Service Worker runs in a separate thread and continues functioning even when the React app isn't active.

## Service Worker lifecycle

### 1. Register

The browser downloads and registers the Service Worker.

```ts
navigator.serviceWorker.register("/sw.js");
```

---

### 2. Install

Executed once when the Service Worker is first installed.

Typical tasks:

- Cache JavaScript bundles
- Cache CSS
- Cache fonts
- Cache images

```text
Install
    │
Cache assets
    │
Done
```

---

### 3. Activate

Runs after installation.

Common responsibilities:

- Delete old caches
- Take control of clients
- Migrate cached data

```text
Old Cache
    │
Delete
    │
Activate New Cache
```

---

### 4. Fetch

Every network request passes through the Service Worker.

```text
React

↓

fetch()

↓

Service Worker

↓

Cache?

↓

Yes → Return Cache

No → Network
```

This is where caching strategies are implemented.

---

## Common caching strategies

| Strategy               | Best for                       |
| ---------------------- | ------------------------------ |
| Cache First            | Images, fonts, logos           |
| Network First          | User profiles, dashboards      |
| Stale While Revalidate | Lists, feeds, product catalogs |
| Cache Only             | Static build assets            |
| Network Only           | Authentication, payment APIs   |

---

## Workbox

Most production React apps use **Workbox** instead of writing Service Worker code manually.

Workbox provides:

- Runtime caching
- Pre-caching
- Background Sync
- Cache expiration
- Offline fallbacks
- Push notification helpers

---

## Using `vite-plugin-pwa`

For Vite projects, `vite-plugin-pwa` automatically:

- Generates a Service Worker
- Registers it
- Precaches build assets
- Supports automatic updates
- Integrates with Workbox

This is the preferred approach over manually managing `sw.js`.

---

## Updating a Service Worker

A newly installed Service Worker doesn't immediately replace the old one if existing tabs are still open.

Typical flow:

```text
Deploy New Version

↓

New SW Installed

↓

Waiting

↓

User Refreshes

↓

Activate New SW
```

Many apps show a notification such as:

> "A new version is available. Refresh?"

---

## Offline support

When offline:

```text
Request

↓

Service Worker

↓

Cached?

↓

Yes

↓

Return Cached Data
```

If no cached response exists, you can return an offline fallback page.

---

## Background Sync

When the user is offline:

```text
POST /todos

↓

Queue Request

↓

Internet Returns

↓

Background Sync

↓

Replay Request
```

This allows writes to be synchronized automatically once connectivity is restored.

---

## Push Notifications

A Service Worker is responsible for receiving push events even when the React app is closed.

Flow:

```text
Server

↓

Push Service

↓

Service Worker

↓

Browser Notification
```

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest my-pwa -- --template react-ts
cd my-pwa
npm install
npm install vite-plugin-pwa
npm run dev
```

Configure Vite:

```ts
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { VitePWA } from "vite-plugin-pwa";

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: "autoUpdate",
      workbox: {
        globPatterns: ["**/*.{js,css,html,png,svg}"],
      },
    }),
  ],
});
```

Register the Service Worker:

```tsx
// main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { registerSW } from "virtual:pwa-register";

registerSW({
  onNeedRefresh() {
    console.log("New version available.");
  },
  onOfflineReady() {
    console.log("App ready for offline use.");
  },
});

ReactDOM.createRoot(document.getElementById("root")!).render(<App />);
```

This setup automatically generates a production-ready Service Worker with precaching and update support.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stack:

- **Vite** for fast development with native ESM and Rollup-based production builds.
- **vite-plugin-pwa** for automatic Service Worker generation and registration.
- **Workbox** for advanced caching strategies, runtime caching, and background sync.
- **Next.js** can support PWAs through community plugins if SSR or App Router features are required.

**ESM vs CommonJS**

- Prefer **ES Modules (ESM)** for modern React applications.
- Vite uses native ESM during development, resulting in faster startup and Hot Module Replacement (HMR).

---

# Performance

Service Workers can significantly improve perceived performance when configured correctly.

- Use **React DevTools Profiler** to profile rendering independently of network improvements.
- Cache immutable assets (hashed JS/CSS bundles) with a **Cache First** strategy.
- Use **Stale While Revalidate** for frequently changing API responses.
- Lazy-load routes with `React.lazy` and `Suspense` to reduce the initial bundle size.
- Use `React.memo`, `useMemo`, and `useCallback` where appropriate to minimize unnecessary re-renders.
- Remove old cache versions during activation to prevent excessive storage usage.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests, and **Playwright** for end-to-end testing.

Example setup:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Test scenarios:

- Application loads successfully while offline.
- Cached assets are served after the first visit.
- New Service Worker versions trigger update notifications.
- Runtime API caching behaves as expected.
- Playwright can emulate offline mode to verify the complete offline experience.

---

# Ops & Deployment

Production best practices:

- Log Service Worker lifecycle events and update failures (e.g., using Sentry).
- Version cache names to invalidate outdated assets safely.
- Handle update prompts gracefully to avoid users running stale bundles indefinitely.
- Use immutable asset hashing and serve static files via a CDN.
- Balance **SSR vs CSR**: SSR improves the first visit, while a Service Worker enhances subsequent visits with offline capability and faster loading.
- Monitor bundle size and split large features to reduce the initial download.

---

# Pitfalls

- **Don't cache every API response**; choose caching strategies based on data freshness requirements.
- **Don't forget cache versioning and cleanup**, or users may receive outdated assets after deployments.
- **Don't assume a newly installed Service Worker is immediately active**; handle the waiting state and update flow explicitly.

## Question 9. How do you monitor React app performance with React Profiler?

# Short answer

**React Profiler** is a performance analysis tool available in **React DevTools** that measures how long components take to render, why they re-render, and which updates are causing performance bottlenecks. In production, you can also use React's `<Profiler>` API to collect render metrics and send them to monitoring tools like Sentry, Datadog, or New Relic.

---

# Explanation

React Profiler helps identify unnecessary renders, expensive components, and slow updates. Instead of guessing where performance issues are, you can inspect actual render timings and optimize only the components that need attention.

## Ways to profile React applications

### 1. React DevTools Profiler (Most Common)

Open:

```text
Browser DevTools
    ↓
React DevTools
    ↓
Profiler Tab
    ↓
Start Recording
    ↓
Interact with App
    ↓
Stop Recording
```

The Profiler shows:

- Which components rendered
- Render duration
- Why components rendered
- Render order
- Commit duration
- Flamegraph and Ranked views

---

### 2. React `<Profiler>` Component

React provides a built-in `<Profiler>` component for collecting render metrics programmatically.

```text
<App>
   ↓
<Profiler>
   ↓
Dashboard
```

It invokes a callback after every render with detailed timing information.

---

## Important metrics

| Metric              | Meaning                                                   |
| ------------------- | --------------------------------------------------------- |
| **Actual Duration** | Time spent rendering the component for the current update |
| **Base Duration**   | Estimated time to render the subtree without memoization  |
| **Commit Time**     | Time when React committed changes to the DOM              |
| **Render Count**    | Number of renders triggered                               |
| **Interactions**    | User actions that caused updates (legacy tracing support) |

---

## Reading the Flamegraph

```text
App
├── Navbar (0.5 ms)
├── Sidebar (1 ms)
├── Dashboard (18 ms)
│      ├── Chart (15 ms)
│      └── Stats (2 ms)
└── Footer (0.2 ms)
```

Here, the `Chart` component is the main bottleneck.

---

## Common causes of unnecessary re-renders

- Parent component state changes
- New object or array references
- Inline functions
- Context updates affecting many consumers
- Large lists without virtualization
- Expensive calculations inside render

---

## Optimization workflow

```text
Record Profile
      ↓
Find Slow Component
      ↓
Identify Re-render Reason
      ↓
Optimize
      ↓
Profile Again
```

Common optimizations include:

- `React.memo`
- `useMemo`
- `useCallback`
- Splitting Context providers
- List virtualization (`react-window`, `react-virtualized`)
- Code splitting with `React.lazy`

---

## Using the `<Profiler>` API

Wrap only the components you want to measure.

```text
<Profiler>
    <Dashboard />
</Profiler>
```

The callback receives:

- Component ID
- Render phase (mount/update)
- Actual duration
- Base duration
- Start time
- Commit time

These metrics can be sent to observability platforms for production monitoring.

---

## Production monitoring

React Profiler data can be combined with:

- Sentry Performance
- Datadog RUM
- New Relic Browser
- OpenTelemetry
- Google Analytics custom events

Example flow:

```text
React Profiler
      ↓
Render Metrics
      ↓
Monitoring Service
      ↓
Performance Dashboard
```

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest react-profiler-demo -- --template react-ts
cd react-profiler-demo
npm install
npm run dev
```

```tsx
// App.tsx
import { Profiler, useState } from "react";

function onRender(
  id: string,
  phase: "mount" | "update",
  actualDuration: number,
  baseDuration: number,
) {
  console.log({
    id,
    phase,
    actualDuration,
    baseDuration,
  });
}

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <h2>{count}</h2>
      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </>
  );
}

export default function App() {
  return (
    <Profiler id="Counter" onRender={onRender}>
      <Counter />
    </Profiler>
  );
}
```

Every render logs timing information, helping you measure the impact of optimizations.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stack:

- **Vite** for fast development with native ESM support.
- **React DevTools** browser extension for interactive profiling.
- **Next.js** when SSR, Server Components, or App Router features are required.
- **Chrome Performance Panel** for correlating React rendering with browser events.

**ESM vs CommonJS**

- Prefer **ES Modules (ESM)** for modern React development.
- Vite uses native ESM in development, enabling fast startup and Hot Module Replacement (HMR).

---

# Performance

Use React Profiler as part of a systematic optimization process.

- Record a baseline profile before making changes.
- Use **React DevTools Profiler** to identify slow components instead of optimizing prematurely.
- Apply `React.memo` only to components with stable props and measurable benefits.
- Use `useMemo` for expensive computations and `useCallback` for stable callback references when passing props to memoized children.
- Virtualize large lists with libraries like `react-window`.
- Split large bundles with `React.lazy` and `Suspense`.
- Leverage React 18 automatic batching to reduce unnecessary renders from multiple state updates.

---

# Testing

Use **Vitest** with **React Testing Library** for component behavior and **Playwright** for end-to-end performance scenarios.

Example setup:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Performance-focused tests can verify:

- Memoized components don't re-render unnecessarily.
- Expensive calculations execute only when dependencies change.
- Lazy-loaded components render correctly after loading.

---

# Ops & Deployment

Production best practices:

- Wrap critical components with `<Profiler>` only when collecting metrics, as profiling adds overhead.
- Send render durations to observability platforms such as Sentry or Datadog.
- Use Error Boundaries to isolate rendering failures.
- Monitor Core Web Vitals (LCP, INP, CLS) alongside React render metrics for a complete performance picture.
- Serve optimized bundles through a CDN and analyze bundle size using tools like `rollup-plugin-visualizer` or `source-map-explorer`.

---

# Pitfalls

- **Don't optimize every component**—focus on bottlenecks identified by profiling.
- **Avoid excessive memoization**; it adds complexity and can hurt performance if props change frequently.
- **Remember that React render time is only part of performance**; also measure network latency, JavaScript execution, and browser rendering.

## Question 10. How do you debug memory leaks in React?

# Short answer

Memory leaks in React typically occur when resources created by a component are **not cleaned up after the component unmounts**. Common causes include unremoved event listeners, uncleared timers, unfinished async requests, WebSocket connections, subscriptions, and stale closures. To debug them, use **React DevTools**, **Chrome DevTools Memory & Performance panels**, and ensure every `useEffect` that creates a side effect also returns a cleanup function.

---

# Explanation

A memory leak happens when objects that are no longer needed remain referenced, preventing JavaScript's garbage collector from reclaiming memory.

Typical lifecycle:

```text
Component Mount
      │
      ▼
Create Resource
(Event, Timer, Socket, Subscription)
      │
      ▼
Component Unmount
      │
      ▼
Cleanup?
   ├── Yes → Memory Released ✅
   └── No  → Memory Leak ❌
```

React itself rarely leaks memory—the leaks usually come from browser APIs or third-party libraries used within React components.

---

## Common causes of memory leaks

### 1. Event listeners

Incorrect:

```tsx
useEffect(() => {
  window.addEventListener("resize", handleResize);
}, []);
```

Correct:

```tsx
useEffect(() => {
  window.addEventListener("resize", handleResize);

  return () => {
    window.removeEventListener("resize", handleResize);
  };
}, []);
```

---

### 2. Timers

Incorrect:

```tsx
useEffect(() => {
  setInterval(fetchData, 1000);
}, []);
```

Correct:

```tsx
useEffect(() => {
  const id = setInterval(fetchData, 1000);

  return () => clearInterval(id);
}, []);
```

---

### 3. Async requests

A request may complete after the component has unmounted.

Use `AbortController`:

```tsx
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/users", {
    signal: controller.signal,
  });

  return () => controller.abort();
}, []);
```

---

### 4. WebSocket connections

Always close sockets:

```tsx
useEffect(() => {
  const socket = new WebSocket("ws://localhost");

  return () => socket.close();
}, []);
```

---

### 5. External subscriptions

Examples:

- Redux store subscriptions
- RxJS observables
- Firebase listeners
- GraphQL subscriptions

Always unsubscribe:

```tsx
useEffect(() => {
  const subscription = observable.subscribe();

  return () => subscription.unsubscribe();
}, []);
```

---

### 6. Stale closures

Effects that capture outdated values can keep unnecessary references alive.

Prefer functional updates or correct dependency arrays:

```tsx
setCount((prev) => prev + 1);
```

---

## Debugging with Chrome DevTools

### Memory Panel

1. Open **Chrome DevTools → Memory**
2. Take a heap snapshot.
3. Navigate through the app (mount/unmount components).
4. Force garbage collection.
5. Take another snapshot.
6. Compare snapshots.

If component instances continue increasing after unmounting, something is retaining references.

---

### Performance Panel

Record a session while interacting with the app.

Look for:

- Long-running timers
- Excessive event callbacks
- Continuous rendering
- JavaScript memory growth

---

### React DevTools

Use the **Profiler** to verify:

- Components unmount as expected.
- Components are not repeatedly re-rendering due to lingering subscriptions or state updates.

---

## Memory leak checklist

```text
Component Unmounted
        │
        ▼
Remove Event Listeners
        │
        ▼
Clear Timers
        │
        ▼
Abort Fetch Requests
        │
        ▼
Close WebSockets
        │
        ▼
Unsubscribe Observables
        │
        ▼
Release References
```

---

## React 18 considerations

React 18 removed the warning:

> "Can't perform a React state update on an unmounted component."

Its removal doesn't mean leaks no longer exist—it simply means React no longer reports every post-unmount state update because many were harmless. Developers are still responsible for cleaning up effects.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest memory-leak-demo -- --template react-ts
cd memory-leak-demo
npm install
npm run dev
```

```tsx
// App.tsx
import { useEffect, useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setCount((c) => c + 1);
    }, 1000);

    return () => {
      clearInterval(id);
    };
  }, []);

  return <h2>Count: {count}</h2>;
}
```

Without the cleanup (`clearInterval`), the timer would continue running even after the component unmounts.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended stack:

- **Vite** for fast development with native ESM support.
- **React DevTools** for component inspection and profiling.
- **Chrome DevTools** (Memory and Performance panels) for heap snapshots and allocation analysis.
- **Next.js** if SSR, App Router, or Server Components are required.

**ESM vs CommonJS**

- Prefer **ES Modules (ESM)** in modern React applications.
- Vite uses native ESM during development, providing fast startup and HMR.

---

# Performance

Memory leaks often lead to gradual performance degradation.

Optimization tips:

- Use the **React DevTools Profiler** to confirm components unmount correctly.
- Memoize expensive computations with `useMemo` and callbacks with `useCallback` only when profiling indicates a benefit.
- Use `React.memo` for components with stable props.
- Lazy-load routes with `React.lazy` and `Suspense` to reduce memory usage at startup.
- Virtualize large lists using `react-window` or `react-virtualized`.
- Monitor heap size over long-running sessions to detect memory growth.

---

# Testing

Use **Vitest** with **React Testing Library** for unit tests and **Playwright** for end-to-end scenarios.

Example setup:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx
import { render } from "@testing-library/react";

test("cleans up interval on unmount", () => {
  const { unmount } = render(<App />);
  unmount();
  // Verify cleanup logic using mocked timers if applicable.
});
```

Also consider fake timers (`vi.useFakeTimers()`) to verify `clearTimeout`/`clearInterval` behavior.

---

# Ops & Deployment

Production best practices:

- Use **Error Boundaries** to isolate rendering failures (they don't catch memory leaks but improve resilience).
- Monitor browser memory usage and JavaScript errors with tools such as Sentry, Datadog, or New Relic.
- Ensure WebSocket connections and subscriptions are closed when users navigate away.
- Split large bundles and lazy-load features to reduce baseline memory consumption.
- Periodically profile production builds, as development mode (especially with Strict Mode) has additional checks that can affect behavior.

---

# Pitfalls

- **Forgetting cleanup functions** in `useEffect` for timers, event listeners, or subscriptions.
- **Ignoring aborted async requests**, allowing responses to update state after a component is no longer relevant.
- **Confusing Strict Mode behavior with leaks**—in development, React intentionally mounts, unmounts, and remounts components once to help detect missing cleanup logic.

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
