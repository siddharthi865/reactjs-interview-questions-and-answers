# Set 5

| #   | Question                                                                                                                                                          |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [Difference between thunk, saga, and observable in Redux](#question-1-difference-between-thunk-saga-and-observable-in-redux)                                      |
| 2   | [How do you structure a large React project?](#question-2-how-do-you-structure-a-large-react-project)                                                             |
| 3   | [How do you implement authentication in React?](#question-3-how-do-you-implement-authentication-in-react)                                                         |
| 4   | [Explain JWT-based authentication in React apps](#question-4-explain-jwt-based-authentication-in-react-apps)                                                      |
| 5   | [How do you implement route guards in React Router?](#question-5-how-do-you-implement-route-guards-in-react-router)                                               |
| 6   | [Explain Progressive Web App (PWA) features in React](#question-6-explain-progressive-web-app-pwa-features-in-react)                                              |
| 7   | [How do you handle real-time updates in React apps (WebSocket, SSE)?](#question-7-how-do-you-handle-real-time-updates-in-react-apps-websocket-sse)                |
| 8   | [How do you prevent XSS attacks in React apps?](#question-8-how-do-you-prevent-xss-attacks-in-react-apps)                                                         |
| 9   | [What is lazy loading of images in React?](#question-9-what-is-lazy-loading-of-images-in-react)                                                                   |
| 10  | [How do you implement infinite scrolling in React?](#question-10-how-do-you-implement-infinite-scrolling-in-react)                                                |
| 11  | [Difference between controlled, uncontrolled, and hybrid components](#question-11-difference-between-controlled-uncontrolled-and-hybrid-components)               |
| 12  | [How do you memoize expensive functions in React?](#question-12-how-do-you-memoize-expensive-functions-in-react)                                                  |
| 13  | [How do you manage React forms with Formik or React Hook Form?](#question-13-how-do-you-manage-react-forms-with-formik-or-react-hook-form)                        |
| 14  | [How do you handle file uploads in React?](#question-14-how-do-you-handle-file-uploads-in-react)                                                                  |
| 15  | [Explain reconciliation with keys and lists in React](#question-15-explain-reconciliation-with-keys-and-lists-in-react)                                           |
| 16  | [How do you handle multi-language (i18n) in React apps?](#question-16-how-do-you-handle-multi-language-i18n-in-react-apps)                                        |
| 17  | [How do you implement drag and drop functionality in React?](#question-17-how-do-you-implement-drag-and-drop-functionality-in-react)                              |
| 18  | [How do you monitor performance in React apps (React Profiler)?](#question-18-how-do-you-monitor-performance-in-react-apps-react-profiler)                        |
| 19  | [How do you use web workers in React?](#question-19-how-do-you-use-web-workers-in-react)                                                                          |
| 20  | [Explain the difference between hydration, SSR, and CSR in React ecosystem](#question-20-explain-the-difference-between-hydration-ssr-and-csr-in-react-ecosystem) |

## Question 1. Difference between thunk, saga, and observable in Redux

## Short answer

Redux Thunk is a simple function-based middleware for async logic, Redux Saga uses generator functions for complex side-effect orchestration, and Redux Observable uses RxJS streams (observables) for reactive, event-driven async flows.

---

## Explanation

In Redux architecture, **thunks, sagas, and observables are middleware patterns for handling side effects** (API calls, async flows, background tasks) outside reducers.

### 1. Redux Thunk (imperative, simple async)

- Middleware that allows action creators to return a function instead of an action.
- That function receives `dispatch` and `getState`.
- Best for simple async flows like API calls.
- Easy to learn, minimal abstraction.

**Key characteristics:**

- Imperative style
- Minimal boilerplate
- Harder to manage complex workflows (cancellation, race conditions)

---

### 2. Redux Saga (declarative, generator-based)

- Uses ES6 **generator functions (`function*`)** to manage side effects.
- Sagas listen for actions and run side-effect workflows.
- Built for **complex async orchestration**: retries, cancellation, debouncing, race conditions.

**Key characteristics:**

- Declarative side-effect model
- Easy to model complex flows
- Powerful control (takeLatest, takeEvery, race, cancel)
- Steeper learning curve due to generators

---

### 3. Redux Observable (reactive, RxJS-based)

- Uses **RxJS observables** to model async actions as streams.
- Actions are streams, and epics transform them.
- Best for **event-heavy, real-time systems** (web sockets, UI events, streaming data).

**Key characteristics:**

- Functional reactive programming (FRP)
- Extremely powerful for streaming/event composition
- Requires RxJS knowledge
- Harder mental model for beginners

---

### Comparison Summary

| Feature             | Thunk              | Saga              | Observable                |
| ------------------- | ------------------ | ----------------- | ------------------------- |
| Style               | Imperative         | Generator-based   | Reactive (streams)        |
| Learning curve      | Low                | Medium–High       | High                      |
| Best for            | Simple async calls | Complex workflows | Event streams / real-time |
| Cancellation        | Manual             | Built-in          | Built-in                  |
| Complexity handling | Low                | High              | Very high                 |
| Dependencies        | None               | redux-saga        | RxJS                      |

---

## Example

### 1. Redux Thunk Example (TypeScript)

```tsx
// store.ts
import { configureStore } from "@reduxjs/toolkit";
import thunk from "redux-thunk";
import { useDispatch, useSelector } from "react-redux";

type State = {
  data: string[];
  loading: boolean;
};

const initialState: State = {
  data: [],
  loading: false,
};

const reducer = (state = initialState, action: any): State => {
  switch (action.type) {
    case "FETCH_START":
      return { ...state, loading: true };
    case "FETCH_SUCCESS":
      return { ...state, loading: false, data: action.payload };
    default:
      return state;
  }
};

export const fetchData = () => async (dispatch: any) => {
  dispatch({ type: "FETCH_START" });

  const res = await fetch("https://jsonplaceholder.typicode.com/todos");
  const data = await res.json();

  dispatch({ type: "FETCH_SUCCESS", payload: data });
};

export const store = configureStore({
  reducer,
  middleware: (getDefault) => getDefault().concat(thunk),
});
```

---

### 2. Redux Saga Example (conceptual)

```ts
import { call, put, takeLatest } from "redux-saga/effects";

function* fetchDataSaga() {
  try {
    const res = yield call(fetch, "/api/data");
    const data = yield res.json();
    yield put({ type: "FETCH_SUCCESS", payload: data });
  } catch (e) {
    yield put({ type: "FETCH_ERROR" });
  }
}

export function* rootSaga() {
  yield takeLatest("FETCH_REQUEST", fetchDataSaga);
}
```

---

### 3. Redux Observable Example (Epic)

```ts
import { ofType } from "redux-observable";
import { ajax } from "rxjs/ajax";
import { map, switchMap } from "rxjs/operators";

const fetchEpic = (action$) =>
  action$.pipe(
    ofType("FETCH_REQUEST"),
    switchMap(() =>
      ajax
        .getJSON("/api/data")
        .pipe(
          map((response) => ({ type: "FETCH_SUCCESS", payload: response })),
        ),
    ),
  );

export default fetchEpic;
```

---

## Tooling & Setup

Prefer modern Redux setup:

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

Recommended stack:

- **Vite + React + TypeScript**
- Redux Toolkit (RTK) instead of raw Redux
- Middleware only when needed (RTK Query often replaces all three)

Notes:

- ESM-first ecosystem (Vite uses ES modules, faster HMR)
- Saga/Observable add runtime dependencies (generator runtime or RxJS)
- RTK simplifies store setup and reduces boilerplate significantly

---

## Performance

- **Thunk:** minimal overhead, but can lead to scattered async logic
- **Saga:** allows cancellation (`takeLatest`) preventing stale requests
- **Observable:** best for streaming optimization, debouncing, throttling
- Use **Redux DevTools** + **React Profiler**
- Memoize selectors with `reselect` or RTK `createSelector`
- Prefer **RTK Query** caching over manual middleware when possible
- Code splitting via `React.lazy` for route-level async loading

---

## Testing

- Thunk: test by mocking `dispatch` and API calls
- Saga: test generator step-by-step (`next().value`)
- Observable: test streams using RxJS `TestScheduler`

Example (Vitest + RTL):

```ts
import { vi } from "vitest";
import { fetchData } from "./store";

test("dispatches success", async () => {
  const dispatch = vi.fn();
  await fetchData()(dispatch, () => ({}));
  expect(dispatch).toHaveBeenCalled();
});
```

Commands:

```bash
npm install -D vitest @testing-library/react jsdom
```

---

## Ops & Deployment

- Avoid heavy middleware in client bundles if SSR (Next.js preferred)
- Use **RTK Query + caching layer + CDN**
- Saga/Observable increase bundle size → evaluate trade-off
- Use error boundaries for async failure handling
- Log side effects centrally (Sentry, Datadog)
- Prefer SSR/edge (Next.js App Router) for data-heavy apps

---

## Pitfalls

- Overusing Thunk → messy, unstructured async logic
- Saga complexity → steep learning curve and boilerplate
- Observable overkill for simple apps
- Mixing multiple async strategies in one codebase

## Question 2. How do you structure a large React project?

## Question 3. How do you implement authentication in React?

## Question 4. Explain JWT-based authentication in React apps

## Question 5. How do you implement route guards in React Router?

## Question 6. Explain Progressive Web App (PWA) features in React

## Question 7. How do you handle real-time updates in React apps (WebSocket, SSE)?

## Question 8. How do you prevent XSS attacks in React apps?

## Question 9. What is lazy loading of images in React?

## Question 10. How do you implement infinite scrolling in React?

## Question 11. Difference between controlled, uncontrolled, and hybrid components

## Question 12. How do you memoize expensive functions in React?

## Question 13. How do you manage React forms with Formik or React Hook Form?

## Question 14. How do you handle file uploads in React?

## Question 15. Explain reconciliation with keys and lists in React

## Question 16. How do you handle multi-language (i18n) in React apps?

## Question 17. How do you implement drag and drop functionality in React?

## Question 18. How do you monitor performance in React apps (React Profiler)?

## Question 19. How do you use web workers in React?

## Question 20. Explain the difference between hydration, SSR, and CSR in React ecosystem
