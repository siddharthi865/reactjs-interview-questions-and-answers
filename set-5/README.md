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

# Short answer

A large React project should be structured **by feature/domain rather than by file type**, with clear separation of UI, business logic, state management, API layer, shared utilities, and tests. Modern React projects typically use **Vite or Next.js**, **TypeScript**, **Redux Toolkit/Zustand/Context** (as appropriate), and colocate components, hooks, tests, and styles within each feature.

---

# Explanation

As applications grow, organizing everything under folders like `components`, `pages`, and `utils` quickly becomes difficult to maintain. A **feature-based architecture** scales better because everything related to a business domain lives together.

Example domains:

- Authentication
- Dashboard
- Users
- Products
- Orders
- Settings

Each feature owns:

- Components
- Hooks
- API calls
- Types
- State
- Tests

This reduces coupling and makes features easier to develop, test, and remove.

A common scalable structure is:

```text
src/
│
├── app/                    # App bootstrap
│   ├── App.tsx
│   ├── main.tsx
│   ├── routes.tsx
│   └── store.ts
│
├── features/
│   ├── auth/
│   │   ├── api/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── pages/
│   │   ├── types.ts
│   │   ├── authSlice.ts
│   │   └── index.ts
│   │
│   ├── users/
│   └── products/
│
├── components/             # Shared reusable UI
│   ├── Button/
│   ├── Modal/
│   ├── Input/
│   └── Loader/
│
├── hooks/
│   ├── useDebounce.ts
│   ├── useLocalStorage.ts
│   └── useWindowSize.ts
│
├── services/
│   ├── api.ts
│   ├── axios.ts
│   └── auth.ts
│
├── lib/
│   ├── formatDate.ts
│   ├── logger.ts
│   └── constants.ts
│
├── types/
├── assets/
├── styles/
└── tests/
```

---

## Layered Architecture

A large application generally has these layers:

```text
UI Components
      │
      ▼
Custom Hooks
      │
      ▼
Business Logic
      │
      ▼
API Layer
      │
      ▼
Backend
```

Keeping these responsibilities separate improves maintainability and testing.

---

## Component Organization

A reusable component should own everything it needs.

```text
Button/
├── Button.tsx
├── Button.module.css
├── Button.test.tsx
├── Button.stories.tsx
└── index.ts
```

Benefits:

- Easier refactoring
- Easier Storybook integration
- Better code ownership

---

## State Management

Choose state based on scope:

| State                  | Recommended Solution        |
| ---------------------- | --------------------------- |
| Local UI state         | `useState`                  |
| Shared component state | Context API                 |
| Complex global state   | Redux Toolkit or Zustand    |
| Server state           | RTK Query or TanStack Query |

Avoid putting all state into Redux. Server state and UI state have different lifecycles and should generally be managed separately.

---

## Routing Structure

For larger applications:

```text
routes/
├── public.tsx
├── private.tsx
├── admin.tsx
└── index.tsx
```

Lazy-load route modules:

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

This reduces the initial bundle size.

---

## API Layer

Avoid calling `fetch()` directly inside components.

Instead:

```text
services/
    users.ts
    auth.ts
    orders.ts
```

```ts
export async function getUsers() {
  return api.get("/users");
}
```

Components remain focused on rendering while services encapsulate networking concerns.

---

## Rendering Behavior (React 18)

React 18 introduced:

- Automatic batching of state updates
- Concurrent rendering capabilities
- `startTransition()` for non-urgent updates
- `Suspense` improvements

Large projects should take advantage of these features:

- Lazy-load feature routes
- Use `Suspense` boundaries
- Wrap expensive state updates in `startTransition`
- Keep components pure to allow React's scheduler to optimize rendering

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Feature structure

```tsx
// src/features/users/components/UserList.tsx
import { useEffect, useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function UserList() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/users")
      .then((r) => r.json())
      .then(setUsers);
  }, []);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

---

# Tooling & Setup

**Preferred stack**

- **Vite + React + TypeScript** for fast development and ESM-first tooling.
- **Next.js App Router** when SSR, streaming, SEO, or React Server Components are required.
- **Remix** is another strong option for data-driven applications.

Avoid **Create React App (CRA)** because it is deprecated.

**Bundler notes**

- **ESM** (used by Vite) enables faster dependency analysis and native module support.
- **Vite** uses native ES modules in development and Rollup for production builds.
- **Next.js** uses Turbopack (development) or Webpack depending on configuration.

---

# Performance

For large projects:

- Split code by routes using `React.lazy()` and `Suspense`.
- Use `React.memo` for expensive presentational components.
- Use `useMemo` for costly derived values.
- Use `useCallback` for stable callback references passed to memoized children.
- Use virtualization (e.g., `react-window`) for very large lists.
- Profile with the React DevTools Profiler before optimizing.
- Cache server data with RTK Query or TanStack Query instead of manual fetching.

---

# Testing

Recommended tools:

- **Vitest** for unit tests.
- **React Testing Library** for component and integration tests.
- **Playwright** for end-to-end testing.

Install:

```bash
npm install -D vitest @testing-library/react jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import UserList from "./UserList";

test("renders users list", () => {
  render(<UserList />);
  expect(screen.getByRole("list")).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Centralize logging with tools such as Sentry or Datadog.
- Configure path aliases (e.g., `@/features`) to avoid deep relative imports.
- Analyze bundle size with tools like `rollup-plugin-visualizer` or `webpack-bundle-analyzer`.
- Deploy static Vite builds behind a CDN, or use Next.js with edge/server rendering where appropriate.
- Apply tree-shaking and dynamic imports to minimize JavaScript shipped to users.

---

# Pitfalls

- Organizing folders only by file type (`components`, `hooks`, `utils`) instead of by feature.
- Allowing business logic and API calls to accumulate inside UI components.
- Storing all application state in Redux or Context, including server state that is better handled by a dedicated data-fetching library.

## Question 3. How do you implement authentication in React?

# Short answer

Authentication in React is typically implemented by combining:

- **An authentication provider** (JWT, OAuth, OpenID Connect, or session cookies)
- **Protected routes** (e.g., with React Router v6)
- **Global auth state** (Context API, Redux Toolkit, or Zustand)
- **Secure token handling** (prefer **HttpOnly cookies** for web apps over storing JWTs in `localStorage`)
- **Automatic token refresh** and logout handling

For production applications, React should focus on UI and routing, while the backend or identity provider handles credential verification and token issuance.

---

# Explanation

Authentication is not just a login page—it encompasses the entire lifecycle of a user's identity.

A typical flow is:

```text
Login Form
      │
      ▼
Backend Authentication
      │
      ▼
Access Token / Session Cookie
      │
      ▼
Save Authentication State
      │
      ▼
Protected Routes
      │
      ▼
Authenticated API Requests
```

---

## Authentication Flow

1. User submits email/password.
2. Backend validates credentials.
3. Backend returns:
   - Session cookie (recommended for web apps), or
   - JWT access token (often paired with a refresh token).

4. React updates global authentication state.
5. Protected routes become accessible.
6. API requests include credentials (cookies automatically or an authorization header for tokens).
7. On expiration, refresh the session/token or redirect to login.

---

## Global Authentication State

Keep only the necessary information globally.

Example:

```ts
type AuthState = {
  user: User | null;
  isAuthenticated: boolean;
  loading: boolean;
};
```

Possible state management choices:

- **Context API** – Small to medium applications
- **Redux Toolkit** – Large applications
- **Zustand** – Lightweight alternative

---

## Protected Routes

With React Router v6:

```tsx
function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { isAuthenticated } = useAuth();

  return isAuthenticated ? children : <Navigate to="/login" replace />;
}
```

This prevents unauthenticated users from accessing protected pages.

---

## Token Storage

### ✅ Recommended (Web Applications)

Use:

- **HttpOnly Secure Cookies**

Advantages:

- JavaScript cannot access them
- Better protection against XSS
- Automatically sent with requests
- Can use `SameSite` and `Secure` attributes

---

### Less Recommended

`localStorage`

Pros:

- Easy to implement

Cons:

- Accessible via JavaScript
- Vulnerable to XSS attacks
- Manual expiration handling

Many production applications avoid storing long-lived JWTs in `localStorage`.

---

## API Integration

Create a centralized API client.

```ts
api.interceptors.request.use((config) => {
  config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

Or, if using cookie-based authentication:

```ts
fetch("/api/profile", {
  credentials: "include",
});
```

This keeps authentication concerns out of UI components.

---

## React 18 Rendering Considerations

Authentication state updates benefit from:

- Automatic batching
- Suspense for loading user/session data
- Lazy-loaded protected routes
- Concurrent rendering for responsive navigation

Avoid triggering multiple independent authentication requests during initial app startup.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### Authentication Context

```tsx
import { createContext, useContext, useState, ReactNode } from "react";

type User = {
  name: string;
};

type AuthContextType = {
  user: User | null;
  login: () => void;
  logout: () => void;
};

const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }: { children: ReactNode }) {
  const [user, setUser] = useState<User | null>(null);

  const login = () => {
    setUser({ name: "John" });
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <AuthContext.Provider value={{ user, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export function useAuth() {
  return useContext(AuthContext)!;
}
```

Protected route:

```tsx
import { Navigate } from "react-router-dom";

function ProtectedRoute({ children }: { children: JSX.Element }) {
  const { user } = useAuth();

  return user ? children : <Navigate to="/login" replace />;
}
```

---

# Tooling & Setup

**Preferred stack**

- **Vite + React + TypeScript** for client-rendered applications.
- **Next.js App Router** when SSR, React Server Components, or SEO are required.
- Avoid **Create React App (CRA)** because it is deprecated.

**Authentication libraries**

- Use the provider's official SDK where possible (e.g., OAuth/OIDC providers).
- For custom backends, use `fetch` or libraries like Axios with centralized interceptors.

**ESM vs CommonJS**

- Prefer ESM-based tooling (Vite).
- Vite provides fast HMR and Rollup-optimized production builds.
- Next.js supports ESM while handling bundling internally.

---

# Performance

- Lazy-load authenticated routes with `React.lazy` and `Suspense`.
- Cache authenticated user data with RTK Query or TanStack Query.
- Memoize authentication context values to avoid unnecessary re-renders.
- Profile authentication flows with the React DevTools Profiler.
- Avoid repeated session validation by caching user information and refreshing it only when necessary.

---

# Testing

Use:

- **Vitest** + **React Testing Library** for unit/integration tests.
- **Playwright** for end-to-end authentication flows.

Install:

```bash
npm install -D vitest @testing-library/react jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";

test("redirects unauthenticated users", () => {
  render(
    <ProtectedRoute>
      <div>Dashboard</div>
    </ProtectedRoute>,
  );
  expect(screen.queryByText("Dashboard")).not.toBeInTheDocument();
});
```

For end-to-end tests, verify login, logout, token/session expiration, and protected route access.

---

# Ops & Deployment

- Prefer **HttpOnly, Secure, SameSite cookies** for session management in browser-based apps.
- Use HTTPS in production.
- Handle expired sessions by redirecting users gracefully.
- Implement refresh-token rotation when using token-based authentication.
- Wrap protected sections with Error Boundaries to handle failures cleanly.
- Centralize authentication logging and monitoring (e.g., failed logins, refresh failures).
- Deploy behind a CDN or edge infrastructure while ensuring authenticated API endpoints remain protected.

---

# Pitfalls

- Storing long-lived JWTs in `localStorage`, increasing exposure to XSS.
- Performing authentication checks separately in every component instead of using a centralized provider and protected routes.
- Forgetting to handle token/session expiration, refresh logic, and logout consistently.

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
