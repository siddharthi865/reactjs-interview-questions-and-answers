# Set 15

| S.No. | Question                                                                                                                                                                                                              |
| ----- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you implement protected routes with React Router v6?](#question-1-how-do-you-implement-protected-routes-with-react-router-v6)                                                                                 |
| 2.    | [How do you implement role-based access control in React apps?](#question-2-how-do-you-implement-role-based-access-control-in-react-apps)                                                                             |
| 3.    | [How do you integrate GraphQL with Apollo Client?](#question-3-how-do-you-integrate-graphql-with-apollo-client)                                                                                                       |
| 4.    | [How do you implement lazy-loaded routes with React Router?](#question-4-how-do-you-implement-lazy-loaded-routes-with-react-router)                                                                                   |
| 5.    | [How do you implement React Query for data fetching and caching?](#question-5-how-do-you-implement-react-query-for-data-fetching-and-caching)                                                                         |
| 6.    | [How do you implement state normalization for complex apps?](#question-6-how-do-you-implement-state-normalization-for-complex-apps)                                                                                   |
| 7.    | [How do you implement advanced performance optimization with useMemo and useCallback?](#question-7-how-do-you-implement-advanced-performance-optimization-with-usememo-and-usecallback)                               |
| 8.    | [How do you handle heavy computations without blocking UI using web workers?](#question-8-how-do-you-handle-heavy-computations-without-blocking-ui-using-web-workers)                                                 |
| 9.    | [How do you implement offline-first apps with service workers?](#question-9-how-do-you-implement-offline-first-apps-with-service-workers)                                                                             |
| 10.   | [How do you implement A/B testing in React applications?](#question-10-how-do-you-implement-ab-testing-in-react-applications)                                                                                         |
| 11.   | [How do you integrate analytics (Google Analytics, Segment) in React apps?](#question-11-how-do-you-integrate-analytics-google-analytics-segment-in-react-apps)                                                       |
| 12.   | [How do you implement micro-frontend architecture using React?](#question-12-how-do-you-implement-micro-frontend-architecture-using-react)                                                                            |
| 13.   | [How do you handle internationalization (i18n) for multilingual support?](#question-13-how-do-you-handle-internationalization-i18n-for-multilingual-support)                                                          |
| 14.   | [How do you implement drag-and-drop with React DnD for complex UI?](#question-14-how-do-you-implement-drag-and-drop-with-react-dnd-for-complex-ui)                                                                    |
| 15.   | [How do you implement virtualized lists for performance optimization?](#question-15-how-do-you-implement-virtualized-lists-for-performance-optimization)                                                              |
| 16.   | [How do you implement form state management with React Hook Form or Formik in large apps?](#question-16-how-do-you-implement-form-state-management-with-react-hook-form-or-formik-in-large-apps)                      |
| 17.   | [How do you monitor React app performance with Chrome DevOps and React Profiler?](#question-17-how-do-you-monitor-react-app-performance-with-chrome-devops-and-react-profiler)                                        |
| 18.   | [How do you integrate testing libraries (Jest, React Testing Library) for unit and integration tests?](#question-18-how-do-you-integrate-testing-libraries-jest-react-testing-library-for-unit-and-integration-tests) |
| 19.   | [How do you implement accessibility (ARIA) best practices in React apps?](#question-19-how-do-you-implement-accessibility-aria-best-practices-in-react-apps)                                                          |
| 20.   | [How do you optimize bundle size using tree-shaking, lazy loading, and dynamic imports?](#question-20-how-do-you-optimize-bundle-size-using-tree-shaking-lazy-loading-and-dynamic-imports)                            |

## Question 1. How do you implement protected routes with React Router v6?

# Short answer

In **React Router v6**, protected routes are typically implemented by creating a wrapper component (e.g., `ProtectedRoute`) that checks the user's authentication state. If the user is authenticated, it renders the requested route using `<Outlet />` (or `children`); otherwise, it redirects to the login page using `<Navigate />`.

For larger applications, authentication state is usually managed with **Context API**, **Redux Toolkit**, or libraries like **Auth0**, **Firebase Auth**, or **NextAuth** (for Next.js).

---

# Explanation

React Router v6 removed the old `render` and `component` props from routes. Instead, routes are composed using the `element` prop and nested routing.

A common architecture looks like this:

```
App
 ├── AuthProvider (Context/Redux)
 ├── BrowserRouter
 │     ├── Public Routes
 │     └── ProtectedRoute
 │             ├── Dashboard
 │             ├── Profile
 │             └── Settings
```

### Step 1: Store authentication state

Authentication state usually comes from:

- Context API
- Redux Toolkit
- Zustand
- React Query + backend session
- JWT stored in HttpOnly cookie (recommended)
- OAuth providers

Avoid storing sensitive JWTs in `localStorage` if possible due to XSS risks. HttpOnly cookies provide better security.

---

### Step 2: Create a ProtectedRoute

The component should:

- Check authentication
- Render children (or `<Outlet />`) if authenticated
- Redirect otherwise

Using `<Outlet />` is preferred because it supports nested routes cleanly.

---

### Step 3: Configure nested routes

Instead of protecting every page individually:

```text
ProtectedRoute
    ├── Dashboard
    ├── Profile
    └── Reports
```

This keeps routing maintainable.

---

### React 18 considerations

React 18 introduces:

- Automatic batching
- Concurrent rendering
- Suspense improvements

During initial authentication:

- Show a loading spinner while checking the session.
- Avoid redirecting before authentication is resolved.

Example flow:

```
loading -> spinner
authenticated -> protected page
unauthenticated -> login
```

Otherwise users may briefly see the login page before being redirected back.

---

### Authorization (Roles)

Authentication answers:

> Who are you?

Authorization answers:

> What can you access?

Example:

```
Admin
   ├── Users
   ├── Billing
   └── Reports

User
   ├── Dashboard
   └── Profile
```

Your protected route can also verify:

```tsx
user.role === "admin";
```

and redirect unauthorized users.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install react-router-dom
npm run dev
```

**ProtectedRoute.tsx**

```tsx
import { Navigate, Outlet } from "react-router-dom";

type Props = {
  isAuthenticated: boolean;
};

export default function ProtectedRoute({ isAuthenticated }: Props) {
  return isAuthenticated ? <Outlet /> : <Navigate to="/login" replace />;
}
```

**App.tsx**

```tsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

import ProtectedRoute from "./ProtectedRoute";

function Dashboard() {
  return <h1>Dashboard</h1>;
}

function Login() {
  return <h1>Login</h1>;
}

function Home() {
  return <h1>Home</h1>;
}

export default function App() {
  const isAuthenticated = true; // Replace with Context/Redux/Auth provider

  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/login" element={<Login />} />

        <Route element={<ProtectedRoute isAuthenticated={isAuthenticated} />}>
          <Route path="/dashboard" element={<Dashboard />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

The route hierarchy:

```
/
login
dashboard  --> ProtectedRoute --> Dashboard
```

If `isAuthenticated` is `false`, navigating to `/dashboard` redirects to `/login`.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript** for fast startup, native ESM support, and excellent developer experience. Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (powered by esbuild during development and Rollup for production builds).
- **Module system:** Prefer **ESM** (`import`/`export`) over CommonJS for modern React projects.
- **Framework options:** Use **Next.js App Router** if you need SSR, React Server Components, or edge rendering; **Remix** is a strong choice for nested routing and data loading.

Run locally:

```bash
npm install
npm run dev
```

---

# Performance

- **Memoization:** Use `React.memo`, `useMemo`, and `useCallback` to reduce unnecessary re-renders in authenticated layouts.
- **Code splitting:** Lazy-load protected sections using `React.lazy` and `Suspense` so users don't download admin or dashboard bundles until needed.
- **Profiling:** Use the React DevTools Profiler to identify render bottlenecks and verify memoization effectiveness.
- **Caching:** If authentication depends on server data, use libraries like TanStack Query to cache session/user information and reduce redundant network requests.
- **Avoid redirect flicker:** Keep an `isLoading` auth state and render a loading indicator until the authentication check completes.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example idea:

```tsx
it("redirects unauthenticated users to login", () => {
  // Render MemoryRouter with ProtectedRoute
  // Assert Login page is shown
});
```

For end-to-end authentication flows, use **Playwright** to verify login, logout, and route protection.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate UI failures; routing errors can also be handled with React Router's route-level error elements.
- Prefer **HttpOnly cookies** over `localStorage` for authentication tokens to reduce XSS exposure.
- Log authentication failures and redirect events with your observability platform (e.g., Sentry or OpenTelemetry) while avoiding sensitive user data.
- Choose **CSR** for purely client-side apps, or **SSR** (e.g., Next.js) if you need better SEO or faster initial loads. Remember that client-side protected routes are a UX feature—**the backend must always enforce authorization**.
- Optimize bundle size with route-based code splitting and serve assets through a CDN.

---

# Pitfalls

- **Redirecting before auth initialization completes**, causing users to briefly see the login page ("flash of unauthenticated content").
- **Trusting only client-side route guards**; always validate authentication and authorization on the server/API.
- **Protecting every route individually** instead of using nested routes with a shared `ProtectedRoute` and `<Outlet />`, leading to repetitive configuration.

## Question 2. How do you implement role-based access control in React apps?

# Short answer

**Role-Based Access Control (RBAC)** in React is implemented by storing the authenticated user's **roles** (e.g., `admin`, `editor`, `user`) and checking those roles before rendering routes, components, or actions. React should use RBAC only for **UI authorization**—the backend must always enforce the same permissions for APIs and sensitive operations.

---

# Explanation

RBAC determines **what an authenticated user is allowed to access**.

Typical flow:

```text
User logs in
      │
      ▼
Backend returns JWT / Session
      │
      ▼
User + Roles + Permissions
      │
      ▼
Store in Auth Context / Redux
      │
      ▼
Protected Routes
Protected Components
Protected API Calls
```

Example roles:

| Role    | Permissions    |
| ------- | -------------- |
| Admin   | Everything     |
| Manager | Users, Reports |
| Editor  | Edit Content   |
| User    | Read Content   |
| Guest   | Public Pages   |

---

## Authentication vs Authorization

Many interviews ask this.

**Authentication**

> Who are you?

Example:

```
Username + Password
```

**Authorization**

> What can you do?

Example:

```
Admin
Can delete users

User
Cannot delete users
```

---

## Architecture

A scalable React application usually has:

```text
src/
 ├── auth/
 │     ├── AuthContext.tsx
 │     ├── useAuth.ts
 │     └── permissions.ts
 │
 ├── routes/
 │     └── ProtectedRoute.tsx
 │
 ├── components/
 │     └── RequireRole.tsx
```

Auth information usually contains:

```ts
type User = {
  id: string;
  name: string;
  role: "admin" | "manager" | "user";
};
```

Larger applications often use permissions instead of a single role:

```text
users.read
users.write
users.delete

reports.view

billing.manage
```

This is more flexible than hardcoding roles.

---

## Route-level RBAC

Instead of only checking authentication:

```text
Authenticated?
```

Also check:

```text
Has required role?
```

Example:

```text
Admin
   Dashboard
   Users
   Billing

User
   Dashboard
```

If the user lacks permission:

```
Redirect -> /unauthorized
```

instead of rendering the page.

---

## Component-level RBAC

Routes are not enough.

Example:

```
Users Page

View Users      ✓
Delete Button   Admin only
Export          Manager only
```

Hide or disable UI elements based on permissions.

Example:

```tsx
{
  user.role === "admin" && <DeleteButton />;
}
```

or

```tsx
<RequireRole roles={["admin"]}>
  <DeleteButton />
</RequireRole>
```

---

## API Authorization

The frontend must **never** be the source of truth.

Even if React hides the Delete button:

```
POST /delete-user
```

must still verify:

```
JWT

↓

Role

↓

Permission

↓

Delete allowed?
```

Otherwise users can call APIs manually.

---

## React 18 considerations

With React 18:

- Automatic batching reduces unnecessary renders when auth state updates.
- Use a loading state while validating sessions or refreshing tokens to avoid redirect flicker.
- Combine RBAC with `Suspense` and lazy-loaded routes so unauthorized users don't download protected bundles unnecessarily.
- Store user data in Context, Redux Toolkit, Zustand, or TanStack Query depending on application complexity.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install react-router-dom
npm run dev
```

**RequireRole.tsx**

```tsx
import { Navigate, Outlet } from "react-router-dom";

type Role = "admin" | "manager" | "user";

interface Props {
  userRole: Role | null;
  allowedRoles: Role[];
}

export default function RequireRole({ userRole, allowedRoles }: Props) {
  if (!userRole) {
    return <Navigate to="/login" replace />;
  }

  return allowedRoles.includes(userRole) ? (
    <Outlet />
  ) : (
    <Navigate to="/unauthorized" replace />
  );
}
```

**App.tsx**

```tsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import RequireRole from "./RequireRole";

const Dashboard = () => <h2>Dashboard</h2>;
const Admin = () => <h2>Admin Panel</h2>;
const Login = () => <h2>Login</h2>;
const Unauthorized = () => <h2>Unauthorized</h2>;

export default function App() {
  const userRole: "admin" | "user" = "user";

  return (
    <BrowserRouter>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/unauthorized" element={<Unauthorized />} />
        <Route path="/dashboard" element={<Dashboard />} />

        <Route
          element={<RequireRole userRole={userRole} allowedRoles={["admin"]} />}
        >
          <Route path="/admin" element={<Admin />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

A regular user can access `/dashboard` but is redirected to `/unauthorized` when navigating to `/admin`.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **Vite** provides fast HMR, ESM-first development, and optimized production builds.
- **ESM** (`import`/`export`) is the standard module format for modern React applications. CommonJS is generally used only in older Node.js ecosystems.
- For SSR, authentication at the edge, or React Server Components, consider **Next.js App Router**. **Remix** is another excellent choice with nested routing and server-driven data loading.

Development commands:

```bash
npm install
npm run dev
```

---

# Performance

- **Memoization:** Memoize derived permission checks (e.g., `const canEdit = useMemo(...)`) if they are expensive or passed to memoized children. Use `React.memo`, `useMemo`, and `useCallback` where profiling indicates benefit.
- **React Profiler:** Verify that auth context updates are not causing unnecessary re-renders across the application. Split contexts if user profile and permissions change independently.
- **Code splitting:** Lazy-load admin dashboards and other privileged areas using `React.lazy` and `Suspense` to reduce the initial bundle size.
- **Caching:** Cache the authenticated user and permissions with TanStack Query or a similar library while respecting token expiration and refresh policies.

---

# Testing

Use **Vitest** and **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test scenarios:

```tsx
it("renders admin page for admin users", () => {
  // Render with admin role
});

it("redirects regular users to unauthorized", () => {
  // Render with user role
});
```

Use **Playwright** for end-to-end tests covering login, role changes, redirects, and permission-restricted actions.

---

# Ops & Deployment

- Keep access tokens in **HttpOnly cookies** when possible to mitigate XSS risks.
- Never rely solely on client-side RBAC; enforce authorization on every backend endpoint.
- Log authorization failures and suspicious access attempts using observability tools such as Sentry or OpenTelemetry, avoiding sensitive data in logs.
- Add **Error Boundaries** around protected layouts to isolate rendering failures.
- Deploy static assets through a CDN and use route-level code splitting to minimize the bundle delivered to non-admin users.

---

# Pitfalls

- **Using only roles instead of fine-grained permissions**, making the system difficult to evolve as requirements grow.
- **Hardcoding role checks throughout components** instead of centralizing authorization logic in reusable hooks or wrapper components.
- **Trusting client-side authorization** and failing to validate permissions on backend APIs.

## Question 3. How do you integrate GraphQL with Apollo Client?

# Short answer

**Apollo Client** is the most popular GraphQL client for React. It manages GraphQL queries, mutations, caching, pagination, optimistic updates, and error handling using hooks such as `useQuery`, `useMutation`, and `useSubscription`.

For production React apps, configure a single `ApolloClient` instance, wrap the app with `ApolloProvider`, and use generated TypeScript types (e.g., GraphQL Code Generator) for type-safe queries.

---

# Explanation

Apollo Client sits between your React application and the GraphQL server.

```text
React Components
       │
useQuery / useMutation
       │
Apollo Client
       │
InMemory Cache
       │
HTTP/WebSocket Link
       │
GraphQL Server
       │
Database
```

Unlike REST, GraphQL lets clients request exactly the fields they need:

```graphql
query {
  user(id: "1") {
    id
    name
    email
  }
}
```

Benefits include:

- Single endpoint (typically `/graphql`)
- Strongly typed schema
- Reduced over-fetching and under-fetching
- Built-in introspection
- Efficient client-side caching

---

## Apollo Client Architecture

A typical production setup includes:

```text
src/
 ├── apollo/
 │     ├── client.ts
 │     ├── links.ts
 │     └── cache.ts
 │
 ├── graphql/
 │     ├── queries/
 │     ├── mutations/
 │     └── generated/
 │
 ├── providers/
 │     └── ApolloProvider.tsx
 │
 └── components/
```

This separation keeps networking, caching, and GraphQL documents organized.

---

## Creating the Apollo Client

The client is configured once with:

- GraphQL endpoint
- Cache
- Authentication headers
- Error handling
- Retry policies
- Optional WebSocket link for subscriptions

Example:

```ts
const client = new ApolloClient({
  uri: "/graphql",
  cache: new InMemoryCache(),
});
```

For authenticated APIs, add an authorization link that injects a bearer token or relies on secure HttpOnly cookies.

---

## Fetching Data

Use `useQuery` to execute GraphQL queries.

It provides:

- `loading`
- `error`
- `data`
- `refetch`

Example lifecycle:

```text
Component mounts
       │
useQuery()
       │
Cache?
   │        │
 Yes      No
   │        │
Return   Fetch Server
   │        │
 Update Cache
```

Apollo checks the cache first (depending on the fetch policy), reducing unnecessary network requests.

---

## Updating Data

Use `useMutation` for:

- Create
- Update
- Delete

After a mutation you can:

- Update the Apollo cache manually
- Refetch affected queries
- Use optimistic updates for immediate UI feedback

Example flow:

```text
User clicks Like
      │
Optimistic UI
      │
Mutation
      │
Server Response
      │
Cache Updated
```

---

## Cache Management

`InMemoryCache` normalizes data by object identifiers (commonly `id` and `__typename`).

Advantages:

- Shared objects across queries
- Automatic UI updates
- Reduced network traffic

Customize cache behavior using **type policies** for pagination, custom merge logic, and key fields.

---

## React 18 Considerations

With React 18:

- Automatic batching minimizes re-renders from GraphQL state updates.
- Combine `React.lazy` and `Suspense` with route-level code splitting to reduce the initial bundle.
- Use background refetching and cache-first strategies for responsive UIs.
- Memoize expensive derived values rather than the raw Apollo results unless profiling indicates a need.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install @apollo/client graphql
npm run dev
```

**main.tsx**

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { ApolloClient, ApolloProvider, InMemoryCache } from "@apollo/client";
import App from "./App";

const client = new ApolloClient({
  uri: "https://example.com/graphql",
  cache: new InMemoryCache(),
});

ReactDOM.createRoot(document.getElementById("root")!).render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
);
```

**App.tsx**

```tsx
import { gql, useQuery } from "@apollo/client";

const GET_USERS = gql`
  query GetUsers {
    users {
      id
      name
      email
    }
  }
`;

type User = {
  id: string;
  name: string;
  email: string;
};

type GetUsersResponse = {
  users: User[];
};

export default function App() {
  const { loading, error, data } = useQuery<GetUsersResponse>(GET_USERS);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error loading users.</p>;

  return (
    <ul>
      {data?.users.map((user) => (
        <li key={user.id}>
          {user.name} ({user.email})
        </li>
      ))}
    </ul>
  );
}
```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (fast HMR, optimized production builds).
- **Module system:** Use **ESM** (`import`/`export`) for modern tooling.
- **Type safety:** Use **GraphQL Code Generator** to generate typed hooks and TypeScript models directly from your GraphQL schema and operations.
- **Frameworks:** For SSR and React Server Components, **Next.js App Router** integrates well with GraphQL. **Remix** is another solid option for server-driven data loading.

Run:

```bash
npm install
npm run dev
```

---

# Performance

- **Caching:** Choose appropriate Apollo fetch policies (`cache-first`, `cache-and-network`, `network-only`) based on freshness requirements.
- **Normalization:** Configure cache **type policies** for efficient object reuse and pagination.
- **Memoization:** Use `React.memo`, `useMemo`, and `useCallback` only where profiling shows unnecessary renders.
- **Profiling:** Use the React DevTools Profiler to identify rendering bottlenecks after GraphQL responses.
- **Code splitting:** Lazy-load GraphQL-heavy routes and components with `React.lazy` and `Suspense`.
- **Optimistic updates:** Improve perceived performance for create/update/delete operations without waiting for the server.

---

# Testing

Use **Vitest** and **React Testing Library** with Apollo's testing utilities.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { MockedProvider } from "@apollo/client/testing";

// Render component with mocked GraphQL responses
```

Use **Playwright** for end-to-end testing against a real or test GraphQL backend.

---

# Ops & Deployment

- Centralize authentication using Apollo Links to attach tokens or rely on secure HttpOnly cookies.
- Configure global error handling for GraphQL and network errors with an `ErrorLink`.
- Monitor request latency and failures using observability platforms such as Sentry or OpenTelemetry.
- Use persisted queries or automatic persisted queries (APQ) where supported to reduce payload sizes.
- Deploy static assets through a CDN and ensure GraphQL endpoints are protected with authentication, authorization, and rate limiting.

---

# Pitfalls

- **Refetching excessively** instead of leveraging Apollo's normalized cache and cache updates.
- **Ignoring loading and error states**, resulting in poor user experience and unhandled failures.
- **Skipping generated TypeScript types**, leading to runtime errors and weaker type safety.

## Question 4. How do you implement lazy-loaded routes with React Router?

# Short answer

Lazy-loaded routes in **React Router v6** are implemented using **`React.lazy()`** to dynamically import route components and **`<Suspense>`** to display a fallback UI while the component is loading. This enables **route-based code splitting**, reducing the initial JavaScript bundle and improving application startup time.

For larger applications, lazy-load feature modules (e.g., Dashboard, Admin, Settings) and preload critical routes when appropriate.

---

# Explanation

By default, all route components are bundled together:

```text
App Bundle
├── Home
├── Dashboard
├── Admin
├── Reports
└── Settings
```

Even if a user only visits the home page, they download every page.

With lazy loading:

```text
Initial Bundle
├── Home
└── Router

Dashboard Bundle (loaded on demand)

Admin Bundle (loaded on demand)

Reports Bundle (loaded on demand)
```

Only the code for the current route is downloaded, improving:

- Faster initial page load
- Smaller JavaScript bundles
- Better Core Web Vitals
- Reduced bandwidth usage

---

## How it works

### Step 1: Lazy-load the component

```tsx
const Dashboard = React.lazy(() => import("./pages/Dashboard"));
```

The component is downloaded only when it's first rendered.

---

### Step 2: Wrap routes with Suspense

`Suspense` displays a fallback while the dynamic import is in progress.

```tsx
<Suspense fallback={<Spinner />}>
  <Dashboard />
</Suspense>
```

---

### Step 3: Route navigation

```text
User clicks Dashboard
        │
        ▼
Download Dashboard chunk
        │
        ▼
Show loading spinner
        │
        ▼
Render Dashboard
```

The downloaded chunk is cached by the browser, so subsequent visits are typically instantaneous.

---

## React Router v6 Route Structure

A common production structure:

```text
App
│
├── Public Layout
│      ├── Home
│      └── About
│
└── Protected Layout
       ├── Dashboard
       ├── Reports
       └── Settings
```

Lazy-load entire feature areas instead of individual small components to avoid creating too many tiny network requests.

---

## React 18 Considerations

React 18 improves the lazy-loading experience with concurrent rendering.

Best practices include:

- Use `Suspense` boundaries around layouts or feature groups rather than every small component.
- Combine lazy loading with protected routes so unauthorized users don't download restricted bundles.
- Use automatic batching to reduce re-renders during navigation.
- Consider prefetching commonly visited routes after login or during idle time.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install react-router-dom
npm run dev
```

**App.tsx**

```tsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";
import { Suspense, lazy } from "react";

const Home = lazy(() => import("./pages/Home"));
const Dashboard = lazy(() => import("./pages/Dashboard"));
const About = lazy(() => import("./pages/About"));

export default function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link> | <Link to="/dashboard">Dashboard</Link> |{" "}
        <Link to="/about">About</Link>
      </nav>

      <Suspense fallback={<p>Loading page...</p>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/dashboard" element={<Dashboard />} />
          <Route path="/about" element={<About />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

**pages/Home.tsx**

```tsx
export default function Home() {
  return <h1>Home</h1>;
}
```

**pages/Dashboard.tsx**

```tsx
export default function Dashboard() {
  return <h1>Dashboard</h1>;
}
```

Each page is compiled into a separate JavaScript chunk and downloaded only when the user navigates to it.

> **Note:** React Router also supports route-level `lazy` APIs (introduced with the Data Router APIs in v6.4+), which allow lazy-loading route modules containing the component, loader, and action together. This is the preferred approach when using `createBrowserRouter`.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (esbuild for development, Rollup for production).
- **Module system:** Use **ESM** (`import`/`export`) to enable efficient code splitting.
- **Frameworks:** If using **Next.js App Router**, route-based code splitting is automatic. **Remix** also provides route-level splitting and server-driven data loading.
- **Dev server:** Start with:

```bash
npm install
npm run dev
```

---

# Performance

- **Code splitting:** Split bundles by feature or route instead of by every component to balance bundle size and network overhead.
- **React Profiler:** Measure whether lazy-loaded routes reduce initial render time and whether fallback UIs are displayed appropriately.
- **Memoization:** Use `React.memo`, `useMemo`, and `useCallback` only after profiling identifies unnecessary re-renders.
- **Prefetching:** Preload frequently visited routes (e.g., after login or on link hover) to make navigation feel instantaneous.
- **Caching:** Browser caching ensures downloaded chunks are reused on subsequent visits. Use long-lived cache headers with hashed filenames for production builds.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test ideas:

```tsx
it("shows loading fallback while route loads", () => {
  // Render app and assert the fallback is visible.
});

it("renders dashboard after lazy import resolves", () => {
  // Mock the lazy-loaded component and verify rendering.
});
```

Use **Playwright** for end-to-end tests to verify navigation, loading indicators, and protected lazy-loaded routes.

---

# Ops & Deployment

- Analyze bundle sizes with tools like `rollup-plugin-visualizer` or `vite-bundle-visualizer`.
- Serve hashed JavaScript chunks through a CDN with appropriate cache headers.
- Wrap route trees with **Error Boundaries** to handle failed chunk downloads gracefully.
- Monitor lazy-loading failures (e.g., network errors) using Sentry or similar observability tools.
- Combine lazy loading with SSR (e.g., Next.js) where appropriate to improve SEO and initial page rendering.

---

# Pitfalls

- **Wrapping every small component in `React.lazy`**, creating excessive network requests instead of splitting by feature or route.
- **Forgetting to use `Suspense`**, which causes runtime errors when rendering lazy-loaded components.
- **Displaying poor fallback UIs**, leading to layout shifts or a confusing loading experience.

## Question 5. How do you implement React Query for data fetching and caching?

# Short answer

**React Query (TanStack Query)** is a server-state management library that simplifies **data fetching, caching, synchronization, background refetching, pagination, optimistic updates, and mutations**. It eliminates much of the boilerplate associated with `useEffect` + `fetch` and provides hooks like `useQuery`, `useMutation`, `useInfiniteQuery`, and `useQueryClient`.

For modern React apps, wrap your app with `QueryClientProvider`, use `useQuery` for reads, `useMutation` for writes, and let React Query manage the cache and background synchronization.

---

# Explanation

React Query is designed for **server state**, not client UI state.

- **Client state:** Theme, modal visibility, selected tab, form inputs (use `useState`, Context, Redux, Zustand, etc.)
- **Server state:** Users, products, orders, API responses (use React Query)

Architecture:

```text
React Components
        │
useQuery / useMutation
        │
TanStack Query
        │
Query Cache
        │
Background Refetch
        │
REST / GraphQL API
        │
Database
```

Unlike a manual `useEffect` approach:

```tsx
useEffect(() => {
  fetch(...)
}, []);
```

React Query automatically handles:

- Caching
- Loading states
- Error states
- Background refetching
- Request deduplication
- Automatic retries
- Cache invalidation
- Stale data management

---

## Setting up QueryClient

Create a single `QueryClient` for the application:

```tsx
const queryClient = new QueryClient();
```

Wrap the application:

```text
App
 └── QueryClientProvider
        ├── Users
        ├── Products
        └── Dashboard
```

All components share the same cache.

---

## Fetching Data with `useQuery`

`useQuery` requires:

- A unique **query key**
- A **query function**

Example:

```tsx
useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

It returns:

- `data`
- `isLoading`
- `isError`
- `error`
- `isFetching`
- `refetch`

Lifecycle:

```text
Component Mount
       │
       ▼
Cache?
  │        │
Yes       No
 │         │
Return   Fetch API
 │         │
Background Cache Update
```

---

## Mutations with `useMutation`

Mutations are used for:

- Create
- Update
- Delete

Typical flow:

```text
Button Click
      │
Mutation
      │
Server Response
      │
Invalidate Query
      │
Fresh Data
```

Example:

```tsx
queryClient.invalidateQueries({
  queryKey: ["users"],
});
```

This refetches affected data after a successful mutation.

---

## Caching

Each query is identified by a **query key**.

Examples:

```text
["users"]

["users", userId]

["products", category]

["orders", customerId]
```

Benefits:

- No duplicate requests
- Shared cache
- Background synchronization
- Automatic refetching when needed

---

## React 18 Considerations

React 18 works well with React Query:

- Automatic batching reduces re-renders from query state updates.
- Combine `Suspense` (optional) with React Query for declarative loading experiences.
- Use `startTransition` for non-urgent UI updates triggered by user interactions.
- Avoid copying query data into local component state unless you need an editable draft.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install @tanstack/react-query
npm run dev
```

**main.tsx**

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import App from "./App";

const queryClient = new QueryClient();

ReactDOM.createRoot(document.getElementById("root")!).render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>,
);
```

**App.tsx**

```tsx
import { useQuery } from "@tanstack/react-query";

type User = {
  id: number;
  name: string;
  email: string;
};

async function fetchUsers(): Promise<User[]> {
  const response = await fetch("https://jsonplaceholder.typicode.com/users");

  if (!response.ok) {
    throw new Error("Failed to fetch users");
  }

  return response.json();
}

export default function App() {
  const { data, isLoading, isError, error } = useQuery({
    queryKey: ["users"],
    queryFn: fetchUsers,
  });

  if (isLoading) return <p>Loading...</p>;

  if (isError) {
    return <p>{(error as Error).message}</p>;
  }

  return (
    <ul>
      {data?.map((user) => (
        <li key={user.id}>
          {user.name} - {user.email}
        </li>
      ))}
    </ul>
  );
}
```

This example caches the user list, avoids duplicate requests, and automatically manages loading and error states.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite (fast HMR, optimized production builds).
- **Module system:** Use **ESM** (`import`/`export`) for tree-shaking and modern tooling.
- **DevTools:** Install **@tanstack/react-query-devtools** during development to inspect queries, cache entries, retries, and invalidations.
- **Frameworks:** React Query integrates well with Vite, Next.js (App Router), and Remix. For SSR frameworks, hydrate prefetched queries to avoid duplicate client-side requests.

Run:

```bash
npm install
npm run dev
```

---

# Performance

- **Cache tuning:** Configure `staleTime` and `gcTime` (formerly `cacheTime`) to balance freshness and network usage.
- **Background refetching:** Enable or disable refetch-on-window-focus based on application needs.
- **Prefetching:** Use `queryClient.prefetchQuery()` for routes or data users are likely to visit next.
- **Pagination:** Use `useInfiniteQuery` or paginated queries with `keepPreviousData` for smooth page transitions.
- **Memoization:** Avoid unnecessary `useMemo` around query results; React Query preserves referential stability where possible. Use the React Profiler to identify actual render bottlenecks.

---

# Testing

Use **Vitest** and **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Testing approach:

```tsx
// Wrap the component with QueryClientProvider
// Create a fresh QueryClient per test
// Mock fetch or API responses
// Assert loading, success, and error states
```

Use **Playwright** for end-to-end testing of real API interactions, optimistic updates, pagination, and retry behavior.

---

# Ops & Deployment

- Configure global retry and error handling through `QueryClient` defaults.
- Integrate logging/monitoring (e.g., Sentry, OpenTelemetry) for failed queries and mutations.
- For SSR (e.g., Next.js), prefetch data on the server and hydrate it on the client using `dehydrate`/`HydrationBoundary`.
- Use CDN caching for static assets while letting React Query manage dynamic API caching.
- Avoid storing sensitive server responses in persistent client-side caches unless required and secured.

---

# Pitfalls

- **Using React Query for client UI state** instead of server state.
- **Creating unstable query keys** (e.g., using non-serializable objects), which leads to cache misses and unnecessary refetches.
- **Invalidating too many queries** after mutations instead of targeting the specific affected query keys.

## Question 6. How do you implement state normalization for complex apps?

## Question 7. How do you implement advanced performance optimization with useMemo and useCallback?

## Question 8. How do you handle heavy computations without blocking UI using web workers?

## Question 9. How do you implement offline-first apps with service workers?

## Question 10. How do you implement A/B testing in React applications?

## Question 11. How do you integrate analytics (Google Analytics, Segment) in React apps?

## Question 12. How do you implement micro-frontend architecture using React?

## Question 13. How do you handle internationalization (i18n) for multilingual support?

## Question 14. How do you implement drag-and-drop with React DnD for complex UI?

## Question 15. How do you implement virtualized lists for performance optimization?

## Question 16. How do you implement form state management with React Hook Form or Formik in large apps?

## Question 17. How do you monitor React app performance with Chrome DevOps and React Profiler?

## Question 18. How do you integrate testing libraries (Jest, React Testing Library) for unit and integration tests?

## Question 19. How do you implement accessibility (ARIA) best practices in React apps?

## Question 20. How do you optimize bundle size using tree-shaking, lazy loading, and dynamic imports?
