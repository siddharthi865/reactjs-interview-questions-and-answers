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

# Short answer

JWT-based authentication in React apps means the backend issues a **JSON Web Token (JWT)** after login, and the React app uses that token to **authenticate API requests and control access to protected routes**, typically via **Authorization headers or (preferably) HttpOnly cookies**.

---

# Explanation

## 1. What JWT is (in practice)

A **JWT (JSON Web Token)** is a signed token containing user identity + claims:

```text
header.payload.signature
```

Typical payload:

```json
{
  "sub": "userId123",
  "email": "user@example.com",
  "role": "admin",
  "exp": 1710000000
}
```

Important properties:

- **Stateless** (no session stored on server in basic setups)
- **Signed** (cannot be tampered with)
- **Time-limited** (expires via `exp`)

---

## 2. JWT authentication flow in React apps

### Step-by-step lifecycle

```text id="jwt-flow"
Login request
   ↓
Backend validates credentials
   ↓
Backend returns JWT (access token ± refresh token)
   ↓
React stores token (memory or cookie)
   ↓
React attaches token to API requests
   ↓
Backend verifies JWT signature
   ↓
Returns protected data
```

---

## 3. Where JWT is stored (critical decision)

### Option A: HttpOnly Cookies (Recommended)

- Token stored in cookie set by backend
- Not accessible via JavaScript

Pros:

- Protected from XSS
- Automatically sent with requests
- Industry best practice for browser apps

Cons:

- Requires CSRF protection (SameSite, CSRF tokens)

---

### Option B: localStorage (Less secure)

```ts
localStorage.setItem("token", jwt);
```

Pros:

- Simple

Cons:

- Vulnerable to XSS
- Manual header injection
- Harder to secure at scale

---

### Option C: In-memory storage

- Stored in React state or Context
- Lost on refresh

Pros:

- Safer than localStorage

Cons:

- Requires re-auth on refresh unless refresh token exists

---

## 4. Sending JWT in requests

### If using Authorization header:

```ts
fetch("/api/profile", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

### If using cookies (preferred):

```ts
fetch("/api/profile", {
  credentials: "include",
});
```

---

## 5. Backend verification (important context)

Backend must:

- Verify signature using secret/public key
- Check expiration (`exp`)
- Validate issuer/audience (optional but recommended)

If invalid:

```http
401 Unauthorized
```

---

## 6. Access token vs refresh token

Production systems usually use:

### Access token

- Short-lived (5–15 min)
- Used for API calls

### Refresh token

- Long-lived (days/weeks)
- Used to get new access tokens

Flow:

```text
Access token expires
   ↓
React calls refresh endpoint
   ↓
Backend issues new access token
```

---

## 7. React architecture for JWT auth

A scalable structure:

```text
src/
 ├── auth/
 │    ├── AuthProvider.tsx
 │    ├── useAuth.ts
 │    ├── authAPI.ts
 │    └── types.ts
 ├── api/
 │    ├── client.ts
 │    └── interceptors.ts
 ├── routes/
 │    ├── ProtectedRoute.tsx
 │    └── PublicRoute.tsx
```

---

## 8. Protected route example

```tsx id="protected-route"
import { Navigate } from "react-router-dom";
import { useAuth } from "../auth/useAuth";

export function ProtectedRoute({ children }: { children: JSX.Element }) {
  const { user } = useAuth();

  return user ? children : <Navigate to="/login" replace />;
}
```

---

## 9. Auth provider example (JWT stored in memory)

```tsx id="jwt-auth-provider"
import { createContext, useContext, useState } from "react";

type User = { id: string; email: string };

type AuthContextType = {
  user: User | null;
  login: (token: string) => Promise<void>;
  logout: () => void;
};

const AuthContext = createContext<AuthContextType | null>(null);

export function AuthProvider({ children }: any) {
  const [user, setUser] = useState<User | null>(null);

  const login = async (token: string) => {
    // decode token or fetch /me endpoint
    const res = await fetch("/api/me", {
      headers: { Authorization: `Bearer ${token}` },
    });

    const data = await res.json();
    setUser(data.user);
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

export const useAuth = () => useContext(AuthContext)!;
```

---

## 10. React 18 considerations

JWT auth systems must handle:

- **Concurrent rendering** → avoid duplicate login requests
- **Automatic batching** → multiple state updates (login + profile fetch) are batched
- **Suspense integration** → fetch `/me` during app initialization
- **startTransition** → avoid blocking UI during auth hydration

---

# Tooling & Setup

Modern stack:

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

Recommended tools:

- React Router v6
- Axios or fetch wrapper
- TanStack Query for `/me` and token refresh flows
- Optional: Redux Toolkit if global state is large

Why Vite:

- Fast ESM-based dev server
- Minimal configuration
- Better than CRA (deprecated)

---

# Performance

- Cache authenticated user state (`/me`) using **TanStack Query**
- Avoid re-parsing JWT on every render
- Use `React.memo` for auth-dependent UI (navbar, profile menu)
- Use lazy-loaded protected routes:

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

- Prevent redundant refresh token calls (single-flight pattern)

---

# Testing

Tools:

- Vitest + React Testing Library
- MSW (Mock Service Worker) for API mocking
- Playwright for E2E auth flows

Example:

```ts id="jwt-test"
import { render } from "@testing-library/react";

test("redirects when not authenticated", () => {
  render(<ProtectedRoute><div>Dashboard</div></ProtectedRoute>);
});
```

E2E example:

- login → store cookie/token → access protected route → logout

---

# Ops & Deployment

- Always use **HTTPS**
- Prefer **HttpOnly cookies** in production
- Rotate refresh tokens (prevent replay attacks)
- Implement rate limiting on login endpoints
- Add centralized logging (failed login attempts, token refresh failures)
- Use CDN for frontend, but secure API separately
- Add CORS configuration carefully when using cookies

---

# Pitfalls

- Storing JWT in `localStorage` → XSS risk
- Not handling token expiration → broken UX
- Mixing cookie + localStorage strategies inconsistently
- Allowing multiple refresh requests → race conditions

## Question 5. How do you implement route guards in React Router?

# Short answer

Route guards in React Router are implemented by wrapping routes with a **protective component** (e.g., `ProtectedRoute`) that checks authentication/authorization state and either renders the requested route or redirects the user using `<Navigate />`.

---

# Explanation

In React Router (v6+), there is no built-in “guard” feature like Angular. Instead, route protection is implemented declaratively using composition.

A **route guard is just a conditional render layer around routes** based on:

- Authentication state (logged in or not)
- Authorization (roles/permissions)
- Feature flags or app state

---

## Core patterns for route guarding

### 1. Protected Route (authentication guard)

Used for restricting access to logged-in users.

```text id="guard-flow"
User navigates route
   ↓
Guard checks auth state
   ↓
If authenticated → render page
If not → redirect to /login
```

---

### 2. Role-based Route Guard (authorization)

Used for admin-only or role-specific access.

Example:

- `/admin` → only admins
- `/dashboard` → authenticated users

---

### 3. Public-only Route

Prevents logged-in users from accessing login/register pages.

---

# Example

## 1. Setup (React Router v6 + Vite)

```bash id="setup-router"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install react-router-dom
npm run dev
```

---

## 2. Auth context (simplified)

```tsx id="auth-context"
import { createContext, useContext, useState } from "react";

type AuthContextType = {
  user: { id: string; role: string } | null;
};

const AuthContext = createContext<AuthContextType>({ user: null });

export const AuthProvider = ({ children }: any) => {
  const [user] = useState<{ id: string; role: string } | null>({
    id: "1",
    role: "user",
  });

  return (
    <AuthContext.Provider value={{ user }}>{children}</AuthContext.Provider>
  );
};

export const useAuth = () => useContext(AuthContext);
```

---

## 3. Protected Route (Auth Guard)

```tsx id="protected-route"
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../auth/AuthProvider";

export function ProtectedRoute() {
  const { user } = useAuth();

  if (!user) {
    return <Navigate to="/login" replace />;
  }

  return <Outlet />;
}
```

### Key idea:

- `Outlet` renders nested routes
- `Navigate` performs redirect

---

## 4. Role-based Route Guard

```tsx id="role-guard"
import { Navigate, Outlet } from "react-router-dom";
import { useAuth } from "../auth/AuthProvider";

export function RoleRoute({ allowedRoles }: { allowedRoles: string[] }) {
  const { user } = useAuth();

  if (!user) {
    return <Navigate to="/login" replace />;
  }

  if (!allowedRoles.includes(user.role)) {
    return <Navigate to="/unauthorized" replace />;
  }

  return <Outlet />;
}
```

---

## 5. Router configuration

```tsx id="router-config"
import { createBrowserRouter } from "react-router-dom";
import { ProtectedRoute } from "./guards/ProtectedRoute";
import { RoleRoute } from "./guards/RoleRoute";

import Home from "./pages/Home";
import Login from "./pages/Login";
import Dashboard from "./pages/Dashboard";
import Admin from "./pages/Admin";

export const router = createBrowserRouter([
  {
    path: "/",
    element: <Home />,
  },
  {
    path: "/login",
    element: <Login />,
  },

  // Protected routes
  {
    element: <ProtectedRoute />,
    children: [
      {
        path: "/dashboard",
        element: <Dashboard />,
      },
    ],
  },

  // Role-based routes
  {
    element: <RoleRoute allowedRoles={["admin"]} />,
    children: [
      {
        path: "/admin",
        element: <Admin />,
      },
    ],
  },
]);
```

---

## 6. App entry

```tsx id="app-entry"
import { RouterProvider } from "react-router-dom";
import { router } from "./router";
import { AuthProvider } from "./auth/AuthProvider";

export default function App() {
  return (
    <AuthProvider>
      <RouterProvider router={router} />
    </AuthProvider>
  );
}
```

---

# React 18 considerations

Route guards should work with:

- **Concurrent rendering** → auth state might initially be unknown
- **Suspense** → delay route rendering until auth state resolves
- **Automatic batching** → login/logout updates propagate efficiently

### Important improvement (real apps)

Instead of assuming `user` synchronously, use:

- `loading` state
- `/me` API hydration
- or TanStack Query caching

---

# Tooling & Setup

Modern routing stack:

- React Router v6.4+ (data routers)
- Vite + TypeScript (preferred)
- Optional: Next.js App Router for SSR-based route protection

Why React Router v6:

- Nested routes via `Outlet`
- Data APIs (loaders/actions)
- Better composition model than v5

---

# Performance

- Lazy-load protected routes:

```tsx id="lazy-route"
const Dashboard = React.lazy(() => import("./pages/Dashboard"));
```

- Wrap route groups in guards instead of per-route checks (reduces duplication)
- Avoid unnecessary re-renders by memoizing auth context
- Use **TanStack Query** or similar for caching `/me` request
- Split route bundles using dynamic imports

---

# Testing

Tools:

- Vitest + React Testing Library
- MSW for API mocking
- Playwright for end-to-end route validation

Example:

```tsx id="route-test"
import { render } from "@testing-library/react";

test("redirects unauthenticated users", () => {
  render(<ProtectedRoute />);
});
```

E2E test flow:

- Visit protected URL
- Assert redirect to login
- Login
- Assert access granted

Install:

```bash id="test-install"
npm install -D vitest @testing-library/react jsdom
```

---

# Ops & Deployment

- Ensure auth state is resolved before rendering routes (avoid flicker)
- Use error boundaries for route-level failures
- Log unauthorized access attempts for security monitoring
- Use HTTPS and secure cookies if using JWT/session-based auth
- Handle refresh token/session renewal before route evaluation
- In SSR frameworks (Next.js), enforce guards on server + client

---

# Common pitfalls and best practices

- ❌ Checking auth directly inside every page instead of centralized guards
- ❌ Not handling loading state → UI flicker or incorrect redirects
- ❌ Mixing role logic in components instead of route-level abstraction

Best practices:

- Use wrapper guard routes (`Outlet` pattern)
- Separate auth vs authorization guards
- Centralize auth state (Context/Zustand/Redux Toolkit)

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
