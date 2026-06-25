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

# Short answer

A Progressive Web App (PWA) in React is a web application enhanced with **service workers, a web app manifest, and caching strategies** that make it behave like a native app—supporting **offline usage, installability, background sync, and fast loading**.

---

# Explanation

A **PWA is not a React feature**—it’s a set of browser capabilities that React apps can adopt.

In React, PWAs are implemented using:

- **Service Worker** → background caching, offline support
- **Web App Manifest** → installable “app-like” behavior
- **HTTPS** → required for security
- **Cache API / Workbox** → advanced caching strategies

---

## Core PWA Features in React Apps

### 1. Installability (Add to Home Screen)

A React PWA can be installed like a native app on:

- Mobile (Android/iOS Safari support varies)
- Desktop (Chrome, Edge)

This is controlled by:

```json id="manifest"
{
  "name": "My React PWA",
  "short_name": "ReactPWA",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#ffffff",
  "theme_color": "#000000",
  "icons": [
    {
      "src": "/icon-192.png",
      "sizes": "192x192",
      "type": "image/png"
    }
  ]
}
```

---

### 2. Offline Support (Service Workers)

Service workers act as a **proxy between your app and the network**.

They can:

- Cache assets (JS, CSS, images)
- Serve content offline
- Intercept network requests

Flow:

```text id="sw-flow"
React App → Service Worker → Cache / Network → Response
```

---

### 3. Fast Loading (Caching Strategies)

PWAs improve performance using caching:

- **Cache-first** (static assets)
- **Network-first** (API data)
- **Stale-while-revalidate** (fast + fresh data balance)

---

### 4. Background Sync (Advanced)

Allows retrying failed requests when network is restored.

Example use cases:

- Form submissions
- Offline actions queued

---

### 5. Push Notifications

Enable re-engagement via browser notifications:

- Requires service worker
- Requires user permission
- Often integrated with Firebase Cloud Messaging

---

# Example (React PWA using Vite)

## 1. Create project

```bash id="vite-pwa"
npm create vite@latest my-pwa -- --template react-ts
cd my-pwa
npm install
npm run dev
```

---

## 2. Add PWA plugin

```bash id="pwa-plugin"
npm install vite-plugin-pwa
```

---

## 3. Configure Vite

```ts id="vite-config"
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { VitePWA } from "vite-plugin-pwa";

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: "autoUpdate",
      manifest: {
        name: "React PWA App",
        short_name: "PWA",
        start_url: "/",
        display: "standalone",
        theme_color: "#ffffff",
        icons: [
          {
            src: "/pwa-192.png",
            sizes: "192x192",
            type: "image/png",
          },
        ],
      },
      workbox: {
        runtimeCaching: [
          {
            urlPattern: ({ request }) => request.destination === "document",
            handler: "NetworkFirst",
          },
          {
            urlPattern: ({ request }) =>
              request.destination === "script" ||
              request.destination === "style",
            handler: "StaleWhileRevalidate",
          },
        ],
      },
    }),
  ],
});
```

---

## 4. Register service worker

```tsx id="sw-register"
import { useEffect } from "react";
import { registerSW } from "virtual:pwa-register";

export default function App() {
  useEffect(() => {
    registerSW({ immediate: true });
  }, []);

  return <h1>React PWA</h1>;
}
```

---

# React 18 considerations

PWAs benefit from React 18 features:

- **Concurrent rendering** → smoother offline transitions
- **Suspense** → better loading UX for cached + network data
- **startTransition** → prevent UI blocking during hydration
- **Streaming SSR (in Next.js)** → faster first paint

Important:

- Avoid blocking UI on service worker registration
- Handle hydration carefully when cached data differs from server data

---

# Architecture in large React PWAs

```text id="pwa-arch"
UI Layer (React)
   ↓
State Layer (Redux Toolkit / Zustand / TanStack Query)
   ↓
Service Layer (API client)
   ↓
Service Worker (cache + offline)
   ↓
Network / Backend
```

Recommended:

- Use TanStack Query for server-state caching
- Use service worker only for asset caching, not complex business logic

---

# Performance

PWAs significantly improve performance through:

- **Pre-caching static assets**
- **Lazy loading routes (React.lazy)**
- **Code splitting via Vite/Next.js**
- **Cache-first strategy for JS/CSS**
- **Image optimization (WebP, lazy loading)**

Tools:

- Lighthouse (PWA audit)
- Chrome DevTools → Application tab → Service Workers
- React Profiler

Optimization tips:

- Avoid caching API responses blindly (stale data risk)
- Use `stale-while-revalidate` for balance
- Keep service worker logic simple and deterministic

---

# Testing

Tools:

- Vitest (unit tests)
- Playwright (offline behavior testing)
- Lighthouse CI (PWA compliance)

Example:

```bash id="pwa-test"
npx lighthouse http://localhost:5173 --view
```

Test cases:

- App works offline
- Install prompt appears
- Service worker caches assets
- UI loads from cache after refresh

---

# Ops & Deployment

- Must be served over **HTTPS**
- Ensure correct caching headers on CDN
- Version service workers to avoid stale caches
- Monitor cache size and invalidation strategy
- Use fallback page for offline navigation
- Deploy via Vercel, Netlify, or CDN (Cloudflare)

Important operational concerns:

- Service worker updates can cause “stuck old app” issues
- Always implement update prompts for users
- Clear cache on major version changes

---

# Common pitfalls

- Over-caching API responses → stale or incorrect data
- Complex logic inside service workers → hard to debug
- Not handling service worker updates → users stuck on old version

## Question 7. How do you handle real-time updates in React apps (WebSocket, SSE)?

# Short answer

Real-time updates in React are typically handled using **WebSockets (bi-directional, persistent connection)** or **Server-Sent Events (SSE, one-way streaming from server to client)**. React listens to these streams and updates state using hooks like `useEffect`, often combined with state libraries like **Redux Toolkit or TanStack Query** for scalability and caching.

---

# Explanation

## 1. Real-time architecture in React

React itself does not manage real-time communication—it only **reacts to incoming data**.

Typical flow:

```text id="realtime-flow"
Server (WebSocket / SSE)
        ↓
Event stream (messages)
        ↓
React listener (useEffect / hooks)
        ↓
State update (useState / Redux / Query cache)
        ↓
UI re-render
```

---

## 2. WebSockets vs SSE

### WebSockets (bi-directional)

- Full-duplex communication (client ↔ server)
- Best for:
  - Chat apps
  - Collaboration tools
  - Multiplayer games
  - Live dashboards

### SSE (Server-Sent Events)

- One-way stream (server → client only)
- Uses HTTP connection
- Best for:
  - Notifications
  - Live feeds
  - Stock updates (simple cases)

---

### Comparison

| Feature      | WebSockets          | SSE             |
| ------------ | ------------------- | --------------- |
| Direction    | Bi-directional      | Server → Client |
| Protocol     | WS / WSS            | HTTP            |
| Complexity   | Medium              | Low             |
| Reconnection | Manual              | Built-in        |
| Use case     | Chat, collaboration | Feeds, updates  |

---

# 3. WebSocket implementation in React

## Example setup (Vite + React + TS)

```bash id="ws-setup"
npm create vite@latest realtime-app -- --template react-ts
cd realtime-app
npm install
npm run dev
```

---

## WebSocket hook

```tsx id="use-websocket"
import { useEffect, useRef, useState } from "react";

type Message = {
  id: string;
  text: string;
};

export function useWebSocket(url: string) {
  const wsRef = useRef<WebSocket | null>(null);
  const [messages, setMessages] = useState<Message[]>([]);

  useEffect(() => {
    const ws = new WebSocket(url);
    wsRef.current = ws;

    ws.onopen = () => {
      console.log("Connected");
    };

    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setMessages((prev) => [...prev, data]);
    };

    ws.onerror = (err) => {
      console.error("WebSocket error", err);
    };

    ws.onclose = () => {
      console.log("Disconnected");
    };

    return () => {
      ws.close();
    };
  }, [url]);

  const sendMessage = (msg: string) => {
    wsRef.current?.send(JSON.stringify({ text: msg }));
  };

  return { messages, sendMessage };
}
```

---

## Component usage

```tsx id="ws-component"
import { useWebSocket } from "./useWebSocket";

export default function Chat() {
  const { messages, sendMessage } = useWebSocket("wss://example.com/ws");

  return (
    <div>
      <h1>Chat</h1>

      <button onClick={() => sendMessage("Hello")}>Send</button>

      <ul>
        {messages.map((m) => (
          <li key={m.id}>{m.text}</li>
        ))}
      </ul>
    </div>
  );
}
```

---

# 4. SSE implementation in React

## SSE hook

```tsx id="use-sse"
import { useEffect, useState } from "react";

type EventData = {
  id: string;
  message: string;
};

export function useSSE(url: string) {
  const [events, setEvents] = useState<EventData[]>([]);

  useEffect(() => {
    const eventSource = new EventSource(url);

    eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setEvents((prev) => [...prev, data]);
    };

    eventSource.onerror = () => {
      console.error("SSE error");
      eventSource.close();
    };

    return () => {
      eventSource.close();
    };
  }, [url]);

  return { events };
}
```

---

## SSE component

```tsx id="sse-component"
import { useSSE } from "./useSSE";

export default function Notifications() {
  const { events } = useSSE("/api/events");

  return (
    <div>
      <h2>Notifications</h2>
      {events.map((e) => (
        <p key={e.id}>{e.message}</p>
      ))}
    </div>
  );
}
```

---

# 5. React 18 considerations

Real-time systems must consider:

### Concurrent rendering

- Frequent updates can cause re-renders
- Use batching and throttling

### Automatic batching

- Multiple messages in same tick are batched automatically

### startTransition

Useful for non-urgent updates:

```ts id="transition"
import { startTransition } from "react";

ws.onmessage = (event) => {
  const data = JSON.parse(event.data);

  startTransition(() => {
    setMessages((prev) => [...prev, data]);
  });
};
```

### Suspense integration (advanced)

- Can combine with streaming frameworks or React Query for cache sync

---

# 6. Scalable architecture (production-grade)

Instead of raw WebSockets everywhere:

```text id="arch"
WebSocket/SSE Layer
        ↓
Event Bus / Middleware
        ↓
State Layer (Redux Toolkit / Zustand)
        ↓
UI Layer (React Components)
```

Recommended tools:

- TanStack Query for syncing server state
- Redux Toolkit for global event-driven state
- Zustand for lightweight real-time apps

---

## Advanced pattern: WebSocket + Redux Toolkit

```ts id="ws-redux"
dispatch({ type: "messageReceived", payload: data });
```

This avoids prop drilling and centralizes updates.

---

# 7. Performance optimization

Real-time apps are performance-sensitive:

### Key strategies:

- Throttle high-frequency events (e.g., 60fps updates → throttle to 100–200ms)
- Use `useMemo` for expensive derived UI
- Use `React.memo` for message components
- Virtualize large lists (`react-window`)
- Avoid full state replacement; use incremental updates

---

## WebSocket optimization tips

- Reuse single connection (avoid multiple hooks creating sockets)
- Implement heartbeat/ping to keep connection alive
- Handle reconnection with exponential backoff
- Batch incoming messages before updating state

---

# 8. Testing real-time features

## Tools:

- Vitest (unit tests)
- MSW (mock WebSocket/SSE)
- Playwright (E2E real-time flows)

## Example concept test:

```ts id="ws-test"
test("receives message", () => {
  // mock WebSocket and assert state updates
});
```

## E2E:

- Open 2 browser tabs
- Send message in one
- Verify update in another

---

# 9. Ops & deployment considerations

- Use **WSS (secure WebSocket)** in production
- Ensure load balancer supports sticky sessions (or use stateless pub/sub like Redis)
- Use message brokers (Kafka / Redis PubSub) for scaling WebSockets
- Handle reconnection gracefully in UI
- Monitor connection count + memory usage
- Add fallback (polling) if WS/SSE fails

---

# 10. Common pitfalls

- ❌ Creating multiple WebSocket connections per component
- ❌ Not cleaning up connections on unmount
- ❌ Updating state too frequently → performance degradation
- ❌ No reconnection strategy
- ❌ Storing real-time state only in component state instead of centralized store

## Question 8. How do you prevent XSS attacks in React apps?

# Short answer

XSS (Cross-Site Scripting) in React apps is primarily prevented by **React’s automatic escaping of JSX**, but real-world protection requires **secure rendering practices, safe HTML handling, Content Security Policy (CSP), and avoiding unsafe DOM APIs like `dangerouslySetInnerHTML` unless strictly sanitized**.

---

# Explanation

## 1. What XSS is (in React context)

XSS happens when an attacker injects malicious JavaScript into your app, usually via:

- User input (comments, forms)
- URL parameters
- API responses
- Third-party content

Example attack:

```html
<script>
  alert("hacked");
</script>
```

If rendered unsafely, it executes in the browser context of your app.

---

## 2. Why React is _mostly safe by default_

React escapes values inside JSX:

```tsx
const name = "<script>alert(1)</script>";

return <h1>{name}</h1>;
```

React renders:

```html
<h1>&lt;script&gt;alert(1)&lt;/script&gt;</h1>
```

So it **does NOT execute scripts by default**.

---

## 3. Main XSS risks in React apps

React is safe unless you bypass its protections:

### ❌ Dangerous patterns

### 1. `dangerouslySetInnerHTML`

```tsx
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

If `userInput` is untrusted → XSS risk.

---

### 2. Direct DOM manipulation

```ts
document.innerHTML = userInput;
```

Completely bypasses React sanitization.

---

### 3. Unsafe URL injection

```tsx
<a href={userInput}>Click</a>
```

Risk:

```text
javascript:alert(1)
```

---

### 4. Unsanitized third-party data

API responses or CMS content can contain scripts.

---

# 4. How to prevent XSS in React (best practices)

---

## 1. Rely on React’s default escaping

Always prefer:

```tsx
<p>{userInput}</p>
```

Never manually insert HTML unless required.

---

## 2. Sanitize HTML when using `dangerouslySetInnerHTML`

Use a trusted sanitizer like:

- DOMPurify

```bash id="dompurify-install"
npm install dompurify
```

```tsx id="sanitize-html"
import DOMPurify from "dompurify";

const clean = DOMPurify.sanitize(userInput);

return <div dangerouslySetInnerHTML={{ __html: clean }} />;
```

---

## 3. Validate and sanitize inputs (backend + frontend)

Frontend validation helps UX, but **backend validation is critical**.

Example:

- Strip scripts
- Restrict allowed HTML tags
- Encode output properly

---

## 4. Secure URL handling

Prevent `javascript:` and unsafe schemes:

```tsx id="safe-link"
function safeUrl(url: string) {
  try {
    const parsed = new URL(url);
    return parsed.protocol === "http:" || parsed.protocol === "https:"
      ? url
      : "#";
  } catch {
    return "#";
  }
}

<a href={safeUrl(userInput)}>Visit</a>;
```

---

## 5. Use Content Security Policy (CSP)

CSP is one of the strongest defenses.

Example header:

```http id="csp-header"
Content-Security-Policy: default-src 'self'; script-src 'self';
```

Benefits:

- Blocks inline scripts
- Prevents unauthorized script execution
- Mitigates injected payloads

---

## 6. Avoid inline scripts and inline event handlers

❌ Bad:

```html
<button onclick="alert(1)">Click</button>
```

❌ In React (rare but risky patterns):

```tsx
<div onClick={() => eval(userCode)} />
```

---

## 7. Secure API handling

Never trust API data blindly:

- Sanitize rich text from CMS
- Validate JSON structure
- Strip script tags server-side

---

## 8. Use trusted libraries for rendering HTML

Examples:

- Markdown → use safe renderers (e.g. `remark`, `markdown-it` with sanitization)
- Rich text editors → sanitize output before saving/rendering

---

# 5. React 18 considerations

React 18 doesn’t change XSS protection directly, but:

### Concurrent rendering risk

- Multiple renders can expose inconsistent unsafe state if sanitization is skipped

### Streaming SSR (Next.js)

- Server-rendered HTML must also be sanitized
- XSS can happen before hydration if server output is unsafe

### Suspense boundaries

- Ensure fallback UI does not render unsafe content

---

# 6. Architecture-level defense

In production React apps:

```text id="xss-arch"
User Input
   ↓
Frontend validation (basic)
   ↓
Backend validation + sanitization (critical)
   ↓
Stored safe data
   ↓
React rendering (escaped by default)
   ↓
Optional client-side sanitization (DOMPurify)
```

Key principle:

> **Never rely only on frontend sanitization**

---

# 7. Performance considerations

Security vs performance trade-offs:

- DOMPurify adds small overhead → acceptable for most apps
- Avoid sanitizing on every render → memoize result:

```tsx id="memo-sanitize"
const clean = useMemo(() => DOMPurify.sanitize(html), [html]);
```

- Use server-side sanitization for large-scale content systems

---

# 8. Testing XSS protection

Tools:

- OWASP ZAP (security scanning)
- Burp Suite
- Jest security test cases

Example test:

```ts id="xss-test"
test("does not render script tags", () => {
  const input = "<script>alert(1)</script>";
  expect(input.includes("<script>")).toBe(true); // raw input
});
```

E2E:

- Inject payloads into forms
- Verify no script execution occurs

---

# 9. Ops & deployment

- Enforce CSP headers at CDN (Cloudflare, Vercel, Nginx)
- Sanitize server-rendered content in SSR apps
- Monitor security logs for injection attempts
- Avoid exposing raw user-generated HTML in APIs
- Use dependency scanning (npm audit, Snyk)

---

# 10. Common pitfalls

- ❌ Assuming React automatically protects everything (it only protects JSX rendering)
- ❌ Using `dangerouslySetInnerHTML` without sanitization
- ❌ Trusting API or CMS content without validation
- ❌ Weak or missing CSP headers

## Question 9. What is lazy loading of images in React?

# Short answer

Lazy loading of images in React means **deferring the loading of images until they are about to enter the viewport**, instead of loading all images at once. This improves **initial page load time, bandwidth usage, and performance**, especially in image-heavy applications.

---

# Explanation

## 1. Why lazy loading is needed

By default, browsers load all images immediately:

```text id="img-load"
Page Load → Download ALL images → Slow initial render
```

In large React apps (e-commerce, feeds, dashboards), this causes:

- Slow First Contentful Paint (FCP)
- High bandwidth usage
- Poor mobile performance

Lazy loading changes this behavior:

```text id="lazy-load"
Page Load → Load visible images only → Load others on scroll
```

---

## 2. Native browser lazy loading

Modern browsers support it via the `loading="lazy"` attribute:

```tsx id="native-lazy"
<img src="/image.jpg" alt="example" loading="lazy" />
```

### Pros:

- No JavaScript needed
- Simple and fast
- Works in most modern browsers

### Cons:

- Limited control
- No advanced placeholder logic
- Not ideal for complex UX (blur, skeletons)

---

## 3. Lazy loading in React (Intersection Observer)

React apps often use the **Intersection Observer API** for better control.

---

### How it works

```text id="io-flow"
Image enters viewport → Observer triggers → Image src is set → Image loads
```

---

## 4. Example: Custom lazy image component

```tsx id="lazy-image"
import { useEffect, useRef, useState } from "react";

type Props = {
  src: string;
  alt: string;
};

export function LazyImage({ src, alt }: Props) {
  const imgRef = useRef<HTMLImageElement | null>(null);
  const [isVisible, setIsVisible] = useState(false);

  useEffect(() => {
    const observer = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          setIsVisible(true);
          observer.disconnect();
        }
      });
    });

    if (imgRef.current) {
      observer.observe(imgRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <img
      ref={imgRef}
      src={isVisible ? src : undefined}
      alt={alt}
      style={{ minHeight: "200px", background: "#eee" }}
    />
  );
}
```

---

## 5. Using a library (recommended in production)

Instead of custom logic, use a battle-tested library:

### Example: `react-lazy-load-image-component`

```bash id="lazy-lib"
npm install react-lazy-load-image-component
```

```tsx id="lazy-lib-example"
import { LazyLoadImage } from "react-lazy-load-image-component";

export default function Gallery() {
  return <LazyLoadImage src="/image.jpg" alt="gallery" effect="blur" />;
}
```

---

## 6. React 18 considerations

- Concurrent rendering improves UI responsiveness while images load
- `Suspense` is not used directly for images (but can be for image-heavy routes)
- Avoid layout shifts using `width`/`height` or `aspect-ratio`
- Combine with `startTransition` for smooth scroll-heavy updates

---

## 7. Preventing layout shift (important)

Without dimensions:

```text id="shift-bad"
Image loads → layout jumps
```

Fix it:

```tsx id="fixed-layout"
<img src={src} alt="image" width={300} height={200} />
```

or CSS:

```css id="css-aspect"
img {
  aspect-ratio: 3 / 2;
}
```

---

## 8. Performance benefits

Lazy loading improves:

- **Initial load time**
- **Time to Interactive (TTI)**
- **Memory usage**
- **Network consumption**

Best practices:

- Combine with image compression (WebP/AVIF)
- Use responsive images (`srcSet`)
- Use CDN (Cloudinary, ImageKit, etc.)
- Preload above-the-fold images only

---

## 9. Testing lazy loading

Tools:

- Chrome DevTools → Network tab (disable cache)
- Lighthouse performance audit
- Playwright for scroll-based testing

Example test scenario:

- Verify images below fold are not requested initially
- Scroll and confirm they load dynamically

---

## 10. Ops & deployment considerations

- Use CDN for image delivery
- Enable caching headers (`Cache-Control`)
- Optimize image formats (WebP/AVIF)
- Use adaptive image sizing
- Monitor Core Web Vitals (LCP, CLS)

---

## 11. Common pitfalls

- ❌ Loading all images at once (no lazy loading)
- ❌ Forgetting width/height → layout shift (CLS issues)
- ❌ Using IntersectionObserver without cleanup
- ❌ Not optimizing image formats (JPEG only in modern apps)

## Question 10. How do you implement infinite scrolling in React?

# Short answer

Infinite scrolling in React is implemented by **loading additional data automatically when the user reaches the bottom (or near the bottom) of a list**. The recommended modern approach is to use the **Intersection Observer API** to detect when a sentinel element becomes visible, then fetch the next page of data. This is more efficient than listening to scroll events.

---

# Explanation

Infinite scrolling improves the user experience by loading content incrementally instead of rendering everything upfront.

Typical flow:

```text
Initial Load
      ↓
Fetch Page 1
      ↓
Render List
      ↓
Sentinel enters viewport
      ↓
Fetch Page 2
      ↓
Append new items
      ↓
Repeat until no more data
```

### Common implementation approaches

1. **Intersection Observer (Recommended)** ✅
   - Efficient
   - Browser optimized
   - Doesn't fire continuously like scroll events

2. **Scroll event listeners**
   - Works everywhere
   - Requires throttling/debouncing
   - More CPU intensive

3. **Libraries**
   - `@tanstack/react-query` (`useInfiniteQuery`)
   - `react-intersection-observer`
   - `react-infinite-scroll-component`

For production applications, combining **Intersection Observer + React Query** provides excellent performance, caching, retries, and pagination support.

---

# Example

### Scaffold with Vite

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### Infinite scroll using Intersection Observer (React + TypeScript)

```tsx
import { useEffect, useRef, useState } from "react";

type Post = {
  id: number;
  title: string;
};

export default function InfinitePosts() {
  const [posts, setPosts] = useState<Post[]>([]);
  const [page, setPage] = useState(1);

  const loaderRef = useRef<HTMLDivElement | null>(null);

  useEffect(() => {
    async function fetchPosts() {
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/posts?_page=${page}&_limit=10`,
      );
      const data = await res.json();
      setPosts((prev) => [...prev, ...data]);
    }

    fetchPosts();
  }, [page]);

  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting) {
          setPage((prev) => prev + 1);
        }
      },
      { threshold: 1 },
    );

    if (loaderRef.current) {
      observer.observe(loaderRef.current);
    }

    return () => observer.disconnect();
  }, []);

  return (
    <>
      {posts.map((post) => (
        <div key={post.id}>
          <h3>{post.title}</h3>
        </div>
      ))}

      <div ref={loaderRef}>Loading more...</div>
    </>
  );
}
```

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript

Why Vite?

- Fast dev server (ESM-based)
- Lightning-fast HMR
- Modern build pipeline
- Recommended over the deprecated Create React App

**Alternative frameworks**

- **Next.js**: SSR, App Router, SEO
- **Remix**: Nested routing and data loading
- **Turbopack**: Fast bundler for Next.js development

### ESM vs CommonJS

- Vite uses **ES Modules (ESM)** by default.
- Prefer `import`/`export`.
- Use CommonJS only when integrating legacy Node packages.

---

# Performance

Infinite scrolling can introduce rendering and memory issues if not handled carefully.

### Virtualization

For thousands of items, use:

- `@tanstack/react-virtual`
- `react-window`
- `react-virtualized`

Only visible rows are rendered.

---

### Cache pages

Instead of refetching:

```tsx
const pages = [...cachedPages];
```

React Query handles this automatically.

---

### Memoization

Avoid unnecessary re-renders:

```tsx
const PostItem = React.memo(Post);
```

Use:

- `React.memo`
- `useMemo`
- `useCallback`

when rendering expensive list items.

---

### Automatic batching (React 18)

React 18 batches state updates from async operations, reducing unnecessary renders during pagination.

---

### Profile performance

Use **React DevTools Profiler** to identify:

- Excessive list re-renders
- Expensive item components
- Slow commits

---

### Code splitting

Lazy load heavy list item components:

```tsx
const ProductCard = React.lazy(() => import("./ProductCard"));
```

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example idea:

```tsx
expect(screen.getByText(/Loading more/i)).toBeInTheDocument();
```

For end-to-end testing with **Playwright**:

- Scroll to the bottom
- Verify additional items appear
- Mock paginated API responses

---

# Ops & Deployment

Production considerations:

- Handle API failures gracefully
- Prevent duplicate requests while a fetch is in progress
- Stop observing when no more pages exist
- Use loading placeholders/skeletons
- Cancel stale requests with `AbortController`
- Serve paginated APIs behind a CDN when appropriate
- Log pagination errors to monitoring tools (e.g., Sentry)

For SSR frameworks (Next.js), load the initial page on the server and continue pagination on the client for a balance of SEO and performance.

---

# Pitfalls

- ❌ Using scroll event listeners without throttling/debouncing.
- ❌ Triggering multiple concurrent API requests because loading state isn't checked.
- ❌ Rendering thousands of DOM nodes instead of using virtualization.

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
