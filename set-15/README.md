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

# Short answer

**State normalization** is the practice of storing relational or nested data in a **flat, entity-based structure** instead of deeply nested objects. Each entity type is stored in a dictionary keyed by ID, while relationships are maintained using arrays of IDs.

Normalization improves update performance, prevents duplicated data, simplifies cache management, and reduces unnecessary React re-renders. In React, it is commonly implemented using **Redux Toolkit's `createEntityAdapter`**, **TanStack Query/Apollo Client caches**, or manually with normalized entity maps.

---

# Explanation

Without normalization, data is often duplicated across the application.

**Non-normalized state**

```text
Posts
 ├── Post A
 │     ├── Author (John)
 │     └── Comments
 │            ├── User (Alice)
 │            └── User (Bob)
 │
 └── Post B
       ├── Author (John)
       └── Comments
```

The same user (`John`) appears multiple times. Updating the user's name requires updating every copy.

---

## Normalized State

Instead, store entities once and reference them by ID.

```text
entities
│
├── users
│     ├── 1 → John
│     ├── 2 → Alice
│     └── 3 → Bob
│
├── posts
│     ├── 10 → { authorId: 1 }
│     └── 11 → { authorId: 1 }
│
└── comments
      ├── 100 → { userId: 2 }
      └── 101 → { userId: 3 }
```

Relationships are maintained through IDs rather than duplicated objects.

---

## Benefits

### 1. Single Source of Truth

Each entity exists only once.

```text
users[1]
```

Updating a user automatically reflects everywhere that references the same ID.

---

### 2. Faster Updates

Without normalization:

```text
Find every post

↓

Find every comment

↓

Update author
```

With normalization:

```text
users[id]

↓

Update once
```

Updates are typically O(1) lookups by ID.

---

### 3. Reduced Re-renders

Because entities are isolated:

```text
users changed

↓

Only components using users rerender
```

instead of rerendering entire nested trees.

---

### 4. Easier Caching

Libraries such as:

- Redux Toolkit
- TanStack Query
- Apollo Client

internally benefit from normalized or entity-based storage.

Apollo's `InMemoryCache`, for example, normalizes GraphQL objects using `id` and `__typename`.

---

## Typical Architecture

```text
Store
│
├── entities
│      ├── users
│      ├── products
│      ├── orders
│      └── comments
│
└── UI State
       ├── filters
       ├── modal
       └── theme
```

Separate **server entities** from **UI state**.

---

## Redux Toolkit's `createEntityAdapter`

For Redux applications, `createEntityAdapter` is the recommended way to normalize collections.

It provides:

- Normalized storage (`ids` + `entities`)
- CRUD reducers
- Memoized selectors
- Efficient updates

Resulting shape:

```text
users
│
├── ids
│      [1,2,3]
│
└── entities
       1 -> John
       2 -> Alice
       3 -> Bob
```

---

## React 18 Considerations

React 18's automatic batching helps when multiple normalized entities are updated together.

Additional best practices:

- Use memoized selectors (`createSelector`) to avoid recomputing derived data.
- Keep server state in TanStack Query or Apollo Client, and normalize client-managed entities only when necessary.
- Avoid denormalizing data into component state unless creating a temporary editable copy.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install @reduxjs/toolkit react-redux
npm run dev
```

**usersSlice.ts**

```tsx
import { createEntityAdapter, createSlice } from "@reduxjs/toolkit";

type User = {
  id: number;
  name: string;
};

const usersAdapter = createEntityAdapter<User>();

const usersSlice = createSlice({
  name: "users",
  initialState: usersAdapter.getInitialState(),
  reducers: {
    usersLoaded: usersAdapter.setAll,
    userUpdated: usersAdapter.updateOne,
  },
});

export const { usersLoaded, userUpdated } = usersSlice.actions;

export default usersSlice.reducer;

export const usersSelectors = usersAdapter.getSelectors(
  (state: { users: ReturnType<typeof usersSlice.reducer> }) => state.users,
);
```

Example normalized state:

```text
users
├── ids: [1, 2]
└── entities
    ├── 1 → { id: 1, name: "Alice" }
    └── 2 → { id: 2, name: "Bob" }
```

Updating a user only modifies one entity, and selectors return memoized results.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **State management:** Redux Toolkit with `createEntityAdapter` is the recommended solution for normalized client state.
- **Server state:** Prefer **TanStack Query** or **Apollo Client** instead of duplicating API data in Redux.
- **Module system:** Use **ESM** (`import`/`export`) with Vite for efficient tree-shaking and fast development.
- **Frameworks:** Next.js and Remix work well with normalized client state while handling SSR and routing.

Run:

```bash
npm install
npm run dev
```

---

# Performance

- Use **memoized selectors** (`createSelector`) to avoid recomputing joins between normalized entities.
- Profile with the **React DevTools Profiler** to verify that only components consuming updated entities re-render.
- Keep entity updates immutable so Redux Toolkit can efficiently detect changes.
- Use route-level code splitting (`React.lazy`) to reduce initial bundle size; normalization complements but does not replace code splitting.
- Cache server data with TanStack Query or Apollo Client instead of maintaining duplicate normalized copies in Redux unless offline editing or complex client-side workflows require it.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Typical tests:

```tsx
it("adds users in normalized format", () => {
  // Dispatch usersLoaded()
  // Assert ids and entities are populated correctly
});

it("updates only one entity", () => {
  // Dispatch userUpdated()
  // Verify only the targeted user changed
});
```

Use **Playwright** for end-to-end testing of CRUD flows that update multiple related entities.

---

# Ops & Deployment

- Keep normalized entity slices separate from transient UI state.
- Log entity update failures and API synchronization issues using tools like Sentry or OpenTelemetry.
- For SSR applications (e.g., Next.js), hydrate normalized state carefully to avoid mismatches between server and client.
- Monitor bundle size; Redux Toolkit is lightweight, but avoid storing large duplicated datasets.

---

# Pitfalls

- **Normalizing small or simple state**, adding unnecessary complexity where nested objects are sufficient.
- **Duplicating the same server data** in both TanStack Query/Apollo Client and Redux, leading to synchronization issues.
- **Performing joins directly inside components** instead of using memoized selectors, causing unnecessary recomputation and re-renders.

## Question 7. How do you implement advanced performance optimization with useMemo and useCallback?

# Short answer

`useMemo` and `useCallback` are **memoization hooks** used to optimize React performance by avoiding unnecessary computations and function recreations.

- **`useMemo`** memoizes the **result of an expensive calculation**.
- **`useCallback`** memoizes a **function reference**.

They should be used **only after profiling** with the React DevTools Profiler. Overusing them can increase memory usage and add unnecessary complexity without improving performance.

---

# Explanation

React re-renders a component whenever its state or props change. During each render:

- Variables are recreated.
- Functions are recreated.
- Calculations are executed again.

Example:

```text
Parent Render
      │
      ├── Function recreated
      ├── Array recreated
      ├── Object recreated
      └── Expensive calculation repeated
```

Memoization allows React to reuse previous values when dependencies have not changed.

---

## `useMemo`

`useMemo` caches the **computed value**.

Without `useMemo`:

```tsx
const sortedUsers = users.sort(sortFn);
```

The sorting runs on every render.

With `useMemo`:

```tsx
const sortedUsers = useMemo(() => {
  return [...users].sort(sortFn);
}, [users]);
```

Flow:

```text
Render
   │
Dependencies changed?
   │
 ┌─Yes──────────────┐
 │ Recalculate      │
 └──────────┬───────┘
            │
          No│
            ▼
Return cached value
```

Common use cases:

- Sorting large lists
- Filtering/searching
- Data transformations
- Complex calculations
- Building lookup maps

---

## `useCallback`

`useCallback` caches a **function reference**.

Without it:

```tsx
<Child onClick={() => save()} />
```

A new function is created on every render.

With it:

```tsx
const handleSave = useCallback(() => {
  save();
}, [save]);
```

Flow:

```text
Render
   │
Dependencies changed?
   │
 ┌─Yes──────────────┐
 │ Create function  │
 └──────────┬───────┘
            │
          No│
            ▼
Reuse previous function
```

This is useful when passing callbacks to memoized children.

---

## Combining with `React.memo`

The biggest benefit comes from combining `useCallback` with `React.memo`.

Without memoization:

```text
Parent Render
      │
New Function
      │
Child Props Changed
      │
Child Re-renders
```

With memoization:

```text
Parent Render
      │
Same Function Reference
      │
React.memo
      │
Child Skips Render
```

Example:

```tsx
const Child = React.memo(({ onSave }: Props) => {
  return <button onClick={onSave}>Save</button>;
});
```

---

## Memoizing Derived Data

Instead of storing derived state:

❌

```tsx
const [filteredUsers, setFilteredUsers] = useState([]);
```

Prefer:

```tsx
const filteredUsers = useMemo(() => {
  return users.filter((u) => u.name.includes(search));
}, [users, search]);
```

This avoids duplicated state and synchronization issues.

---

## Common Optimization Pattern

```text
API Data
    │
    ▼
useMemo
(Filter / Sort)
    │
    ▼
React.memo Child
    │
    ▼
useCallback Handlers
```

This minimizes both computation and rendering.

---

## React 18 Considerations

React 18 introduced:

- Automatic batching
- Concurrent rendering
- `startTransition`
- `useDeferredValue`

These reduce many rendering costs automatically, so **memoization is no longer the first optimization step**.

Use `useMemo` and `useCallback` when:

- Expensive calculations are measurable.
- Stable references are required for `React.memo`.
- Dependency arrays in custom hooks need stable callbacks.
- Third-party libraries compare function references.

Avoid using them around trivial computations or every event handler by default.

---

# Example

**Scaffold with Vite (React + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**App.tsx**

```tsx
import { memo, useCallback, useMemo, useState } from "react";

type User = {
  id: number;
  name: string;
};

const UserList = memo(
  ({ users, onSelect }: { users: User[]; onSelect: (id: number) => void }) => (
    <ul>
      {users.map((user) => (
        <li key={user.id}>
          <button onClick={() => onSelect(user.id)}>{user.name}</button>
        </li>
      ))}
    </ul>
  ),
);

export default function App() {
  const [search, setSearch] = useState("");

  const users: User[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
    { id: 3, name: "Charlie" },
  ];

  const filteredUsers = useMemo(() => {
    return users.filter((u) =>
      u.name.toLowerCase().includes(search.toLowerCase()),
    );
  }, [users, search]);

  const handleSelect = useCallback((id: number) => {
    console.log("Selected:", id);
  }, []);

  return (
    <>
      <input
        value={search}
        onChange={(e) => setSearch(e.target.value)}
        placeholder="Search users"
      />

      <UserList users={filteredUsers} onSelect={handleSelect} />
    </>
  );
}
```

In this example:

- `filteredUsers` is recomputed only when `users` or `search` changes.
- `handleSelect` keeps the same function reference across renders.
- `UserList` skips unnecessary re-renders thanks to `React.memo`.

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**. Avoid **Create React App (CRA)** because it is deprecated.

- **Bundler:** Vite provides fast HMR and optimized production builds.
- **Module system:** Use **ESM** (`import`/`export`) for modern tooling and tree-shaking.
- **Frameworks:** Next.js App Router and Remix also benefit from memoization, but remember that React Server Components reduce the amount of client-side JavaScript, changing where optimizations are most valuable.

Run:

```bash
npm install
npm run dev
```

---

# Performance

- **Profile first:** Use the **React DevTools Profiler** to identify expensive renders before adding memoization.
- **Use `React.memo` strategically:** `useCallback` is most effective when passed to memoized child components.
- **Memoize expensive calculations:** Use `useMemo` for sorting, filtering, grouping, or computationally intensive work—not simple expressions.
- **Code splitting:** Combine memoization with route-level code splitting (`React.lazy`) to reduce both rendering cost and bundle size.
- **Cache server data:** For API responses, prefer **TanStack Query** or **Apollo Client** caching instead of using `useMemo` as a data cache.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Testing ideas:

```tsx
it("renders filtered users", () => {
  // Update search input
  // Verify only matching users are displayed
});

it("does not re-render memoized child unnecessarily", () => {
  // Track render count with a mock or counter
});
```

Use **Playwright** to measure perceived responsiveness during interactions with large datasets.

---

# Ops & Deployment

- Measure render frequency and commit times using the React DevTools Profiler before and after optimization.
- Use logging and monitoring tools (e.g., Sentry, OpenTelemetry) to identify performance bottlenecks in production.
- Pair memoization with bundle analysis (`rollup-plugin-visualizer` for Vite) and CDN delivery for optimal application performance.
- Add **Error Boundaries** around complex UI sections to isolate failures without affecting the rest of the application.

---

# Pitfalls

- **Using `useMemo` or `useCallback` everywhere**, increasing memory usage and code complexity without measurable gains.
- **Providing incorrect dependency arrays**, causing stale values or stale closures.
- **Expecting `useCallback` alone to prevent re-renders**; it is effective only when consumers compare function references (e.g., `React.memo`).

## Question 8. How do you handle heavy computations without blocking UI using web workers?

# Short answer

Use **Web Workers** to move CPU-intensive work (parsing large files, image processing, complex calculations, data transformations) off the main UI thread. In React, communicate with the worker using `postMessage`/`onmessage`, manage worker lifecycle with `useEffect`, and keep UI responsive while React 18 continues rendering concurrently. Use Vite's built-in worker support for a modern setup.

---

# Explanation

React runs on the browser's **main thread**, which is also responsible for:

- Rendering the UI
- Handling user interactions
- Running JavaScript
- Layout and painting

If you execute expensive synchronous work like:

- Sorting 1 million records
- Processing CSV/Excel files
- Image manipulation
- PDF generation
- Encryption
- Machine learning inference

the browser cannot repaint until JavaScript finishes, causing:

- Frozen UI
- Delayed clicks
- Janky animations
- Slow rendering

A **Web Worker** runs JavaScript on a separate thread.

Architecture:

```
Main Thread (React)
        │
 postMessage()
        │
────────▼────────
   Web Worker
 Heavy computation
        │
 postMessage(result)
────────▲────────
        │
 React updates state
```

### React integration

A common production pattern is:

- Create worker once with `useEffect`
- Send work using `worker.postMessage()`
- Receive result with `worker.onmessage`
- Store result in React state
- Cleanup worker on unmount using `terminate()`

```text
Component mounts
      ↓
Worker created
      ↓
User clicks
      ↓
Message sent
      ↓
Worker computes
      ↓
Returns result
      ↓
React re-renders
```

### React 18 considerations

React 18 introduced:

- Concurrent Rendering
- Automatic batching
- Transitions

These improve rendering responsiveness but **do not make JavaScript calculations faster**.

Example:

```tsx
startTransition(() => {
  setItems(bigList);
});
```

React schedules rendering better.

But:

```ts
const result = expensiveCalculation();
```

still blocks the thread.

Web Workers solve a different problem:

- React concurrency → rendering optimization
- Web Worker → computation optimization

Often both are used together.

---

# Example

### Create project

```bash
npm create vite@latest react-worker-demo -- --template react-ts
cd react-worker-demo
npm install
npm run dev
```

### `src/workers/sumWorker.ts`

```ts
self.onmessage = (event: MessageEvent<number>) => {
  const limit = event.data;

  let sum = 0;

  for (let i = 0; i < limit; i++) {
    sum += i;
  }

  self.postMessage(sum);
};

export {};
```

---

### `App.tsx`

```tsx
import { useEffect, useRef, useState } from "react";

export default function App() {
  const workerRef = useRef<Worker>();
  const [result, setResult] = useState<number>();
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    workerRef.current = new Worker(
      new URL("./workers/sumWorker.ts", import.meta.url),
      { type: "module" },
    );

    workerRef.current.onmessage = (event: MessageEvent<number>) => {
      setResult(event.data);
      setLoading(false);
    };

    return () => workerRef.current?.terminate();
  }, []);

  const calculate = () => {
    setLoading(true);
    workerRef.current?.postMessage(500_000_000);
  };

  return (
    <div>
      <button onClick={calculate}>Calculate Huge Sum</button>

      {loading ? <p>Calculating...</p> : <p>{result}</p>}
    </div>
  );
}
```

The UI remains responsive while the worker performs the heavy calculation.

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript**
- React 18+
- Native ES Modules

Avoid **Create React App**, as it is deprecated.

### Why Vite?

- Native worker support
- Fast HMR
- ESM-first architecture
- Excellent TypeScript integration
- Optimized production builds with Rollup

Creating workers:

```ts
new Worker(new URL("./worker.ts", import.meta.url), { type: "module" });
```

### ESM vs CommonJS

- Browser workers use **ES Modules**
- Modern React projects should use ESM
- Vite handles module resolution and bundling automatically

---

# Performance

### When to use Web Workers

✔ Large JSON parsing

✔ CSV parsing

✔ Excel import

✔ Image resizing

✔ Face detection

✔ ML inference

✔ Encryption

✔ Financial calculations

✔ Graph algorithms

### When NOT to use them

Tiny calculations:

```ts
items.filter(...)
```

Simple sorting:

```ts
users.sort(...)
```

Small array operations

Worker communication has overhead, so lightweight tasks may become slower.

### Combine with React optimizations

Use:

- `React.memo`
- `useMemo`
- `useCallback`
- `useTransition`
- `useDeferredValue`

Example:

```tsx
const expensiveValue = useMemo(() => transform(data), [data]);
```

Keep rendering efficient while workers handle CPU-bound tasks.

### Use Transferable Objects

Instead of copying large binary data:

```ts
worker.postMessage(buffer, [buffer]);
```

Ownership transfers to the worker, avoiding expensive cloning.

### SharedArrayBuffer

For advanced scenarios:

- real-time processing
- video editors
- collaborative tools
- games

Shared memory avoids serialization costs but requires appropriate browser security headers.

### Profiling

Use:

- React DevTools Profiler
- Chrome Performance tab
- Chrome Memory tab
- Lighthouse
- Performance Insights

Verify that long tasks disappear from the main thread after moving work to a worker.

---

# Testing

Unit test computation logic separately from the worker transport layer.

Example using **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react jsdom
```

Test the pure computation function independently, then mock the `Worker` API in component tests to verify message passing and state updates. Use Playwright for end-to-end testing if you need to validate responsiveness during long-running operations.

---

# Ops & Deployment

Production considerations:

- Add an Error Boundary for UI failures (workers won't be caught by it, so also handle `worker.onerror`).
- Handle worker errors:

```ts
worker.onerror = (err) => {
  console.error(err);
};
```

- Lazy-load workers only when needed to reduce initial bundle size.
- Cache worker scripts using CDN/browser caching.
- Offload only CPU-intensive work; network requests generally don't require workers.
- For SSR frameworks like Next.js, create workers only on the client (e.g., inside `useEffect` or client components) because workers rely on browser APIs.

---

# Pitfalls

- **Creating a new worker on every render** instead of reusing a single instance and terminating it on unmount.
- **Passing huge objects repeatedly**, which incurs structured clone overhead; prefer Transferable Objects for binary data.
- **Using workers for trivial computations**, where messaging overhead outweighs any performance benefit.

## Question 9. How do you implement offline-first apps with service workers?

# Short answer

An **offline-first** React app continues to function without a network connection by caching application assets and data locally. The standard approach is to use a **Service Worker** to cache static files, runtime API responses, and assets, often with **Workbox**. In React, pair a Service Worker with local storage (IndexedDB), background synchronization, and data-fetching libraries like React Query to provide a resilient user experience.

---

# Explanation

A **Service Worker** is a background JavaScript process that sits between your React application and the network.

It can:

- Cache HTML, CSS, JavaScript, fonts, and images
- Cache API responses
- Serve cached content when offline
- Synchronize queued requests when connectivity returns
- Receive push notifications
- Enable Progressive Web App (PWA) capabilities

Architecture:

```text
                Internet
                    ▲
                    │
            fetch()
                    │
       ┌────────────┴────────────┐
       │     Service Worker      │
       │  Cache / Network Logic  │
       └────────────┬────────────┘
                    │
          React Application
                    │
         React Query / IndexedDB
```

### Offline-first request flow

```text
User requests page
        │
        ▼
Service Worker intercepts request
        │
        ├── Cache available?
        │        │
        │       Yes
        │        │
        ▼        ▼
Return cached response
        │
        No
        │
Fetch from network
        │
Store in cache
        │
Return response
```

---

## Common caching strategies

### 1. Cache First

Best for:

- Images
- Fonts
- JavaScript bundles
- CSS

```text
Cache → Network (fallback)
```

Fastest loading.

---

### 2. Network First

Best for:

- User profile
- Dashboard
- Frequently changing data

```text
Network → Cache (fallback)
```

Keeps data fresh.

---

### 3. Stale While Revalidate

Best for:

- Product lists
- Blog posts
- News feeds

```text
Return cache immediately
        ↓
Fetch latest
        ↓
Update cache
```

Provides both speed and freshness.

---

### 4. Network Only

For:

- Authentication
- Payments
- Sensitive operations

Never cache these requests.

---

### 5. Cache Only

Useful for immutable assets packaged with the application.

---

## React architecture

A production-ready offline-first app typically includes:

- **React UI**
- **Service Worker** (asset and request caching)
- **IndexedDB** (persistent structured storage)
- **React Query** (query caching and synchronization)
- **Background Sync** (retry failed mutations)
- **Push Notifications** (optional)

```text
React
 │
 ▼
React Query
 │
 ▼
IndexedDB
 │
 ▼
Service Worker
 │
 ▼
Network
```

---

# Example

## Create project

```bash
npm create vite@latest offline-demo -- --template react-ts
cd offline-demo
npm install
npm install vite-plugin-pwa
npm run dev
```

---

## `vite.config.ts`

```ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import { VitePWA } from "vite-plugin-pwa";

export default defineConfig({
  plugins: [
    react(),
    VitePWA({
      registerType: "autoUpdate",
      workbox: {
        globPatterns: ["**/*.{js,css,html,png,svg,ico}"],
      },
    }),
  ],
});
```

The plugin generates and registers a production-ready Service Worker using **Workbox**.

---

## Register the Service Worker

```tsx
import { registerSW } from "virtual:pwa-register";

registerSW({
  immediate: true,
});
```

---

## React component

```tsx
import { useEffect, useState } from "react";

export default function App() {
  const [online, setOnline] = useState(navigator.onLine);

  useEffect(() => {
    const update = () => setOnline(navigator.onLine);

    window.addEventListener("online", update);
    window.addEventListener("offline", update);

    return () => {
      window.removeEventListener("online", update);
      window.removeEventListener("offline", update);
    };
  }, []);

  return (
    <div>
      <h2>{online ? "🟢 Online" : "🔴 Offline"}</h2>
      <p>The app continues working using cached assets.</p>
    </div>
  );
}
```

This example detects connectivity changes while the Service Worker serves cached resources when the network is unavailable.

---

# Tooling & Setup

### Recommended stack

- **Vite + React + TypeScript**
- **vite-plugin-pwa**
- **Workbox**
- **React Query**
- **IndexedDB** (via libraries such as `idb` or `Dexie`)

Avoid **Create React App**, as it is deprecated.

### Why Vite?

- Fast development server
- Native ESM support
- Excellent PWA integration
- Optimized production builds
- Automatic Service Worker generation through plugins

### ESM vs CommonJS

- Modern React applications should use **ES Modules (ESM)**.
- Service Worker code is bundled as ESM-compatible output by Vite.
- Rollup handles production bundling and asset hashing automatically.

---

# Performance

### Cache static assets

- JavaScript
- CSS
- Fonts
- Images
- Icons

Avoid repeated downloads.

---

### Cache API responses

Example strategy:

```text
Products → Stale While Revalidate

User profile → Network First

Settings → Cache First
```

---

### Use IndexedDB for offline data

Store:

- Draft forms
- Todo items
- Messages
- Search history
- Recently viewed content

IndexedDB is asynchronous and suitable for large structured datasets, unlike `localStorage`.

---

### Queue failed mutations

If a user submits data while offline:

```text
User submits form
        │
Offline
        │
Store request
        │
Reconnect
        │
Replay request
```

This is often implemented using Workbox Background Sync or custom retry logic.

---

### React Query integration

React Query complements Service Workers by:

- Serving cached query data immediately
- Refetching when the network returns
- Retrying failed requests
- Persisting cache to IndexedDB with persistence plugins

---

### Code splitting

Use lazy loading to minimize the initial download:

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

---

### Profiling

Use:

- React DevTools Profiler
- Chrome DevTools → Application → Cache Storage
- Chrome DevTools → Network (Offline mode)
- Lighthouse PWA audit

Verify offline behavior by simulating network loss in DevTools.

---

# Testing

Use **Vitest + React Testing Library** for component testing:

```bash
npm install -D vitest @testing-library/react jsdom
```

Test:

- Offline banners
- Cached UI rendering
- Retry logic after reconnecting
- React Query cache hydration

For end-to-end testing, use **Playwright** to emulate offline mode and verify that cached routes and assets continue to work.

---

# Ops & Deployment

Production considerations:

- Serve over **HTTPS**, as Service Workers require secure contexts (except on `localhost` during development).
- Configure cache versioning and cleanup to remove outdated assets after deployments.
- Use hashed filenames to ensure clients receive updated bundles.
- Handle Service Worker updates gracefully, prompting users to refresh when a new version is available.
- Use a CDN for static assets while allowing the Service Worker to manage client-side caching.
- In SSR frameworks like Next.js, register the Service Worker only on the client after hydration.

---

# Pitfalls

- **Caching authenticated or sensitive responses**, which can expose stale or private data. Prefer network-only strategies for these endpoints.
- **Forgetting cache invalidation**, causing users to remain on outdated application versions after deployment.
- **Relying solely on the browser cache**, instead of combining Service Workers with IndexedDB and robust synchronization for a true offline-first experience.

## Question 10. How do you implement A/B testing in React applications?

# Short answer

A/B testing in React is typically implemented using a **feature flag/experimentation platform** (e.g., LaunchDarkly, Optimizely, GrowthBook, Statsig, PostHog) or an in-house solution. The application determines a user's experiment variant, stores it consistently, renders the corresponding UI, and tracks exposure and conversion events. Keep experiment logic separate from business logic, ensure users remain in the same variant across sessions, and remove stale experiments once they conclude.

---

# Explanation

A/B testing compares two or more UI or behavior variants to determine which performs better against a defined metric (conversion, engagement, retention, etc.).

Example:

```text
Experiment: Checkout Button

Variant A (Control)
-------------------
Blue button

Variant B (Treatment)
---------------------
Green button

Goal:
Increase checkout completion rate
```

## Architecture

```text
              User
                │
                ▼
      Feature Flag Service
                │
      Variant Assignment
                │
         A / B / C
                │
                ▼
        React Application
                │
      Conditional Rendering
                │
                ▼
     Analytics / Event Tracking
```

The assignment should be deterministic (sticky) so the same user consistently sees the same variant.

---

## Typical flow

```text
User visits app
      │
      ▼
Identify user
      │
      ▼
Fetch experiment assignments
      │
      ▼
Render assigned variant
      │
      ▼
Track exposure event
      │
      ▼
User interacts
      │
      ▼
Track conversion metrics
```

---

## Feature flags vs A/B testing

Feature flags:

- Enable or disable functionality
- Gradual rollouts
- Operational control

A/B testing:

- Randomized user assignment
- Statistical comparison
- Measures business impact

Many platforms support both capabilities.

---

## React implementation

A common approach:

- Initialize the experimentation SDK at app startup.
- Load user identity and experiment assignments.
- Expose assignments via Context or a custom hook.
- Conditionally render UI.
- Track exposure when the experiment is viewed.
- Track conversion events separately.

---

# Example

## Create project

```bash
npm create vite@latest react-ab-demo -- --template react-ts
cd react-ab-demo
npm install
npm run dev
```

---

## Experiment Context

```tsx
import { createContext, useContext, useMemo, type ReactNode } from "react";

type Variant = "A" | "B";

const ExperimentContext = createContext<Variant>("A");

export function ExperimentProvider({ children }: { children: ReactNode }) {
  // Replace with SDK result (LaunchDarkly, GrowthBook, etc.)
  const variant = useMemo<Variant>(() => (Math.random() > 0.5 ? "A" : "B"), []);

  return (
    <ExperimentContext.Provider value={variant}>
      {children}
    </ExperimentContext.Provider>
  );
}

export function useExperiment() {
  return useContext(ExperimentContext);
}
```

---

## Component

```tsx
import { useExperiment } from "./ExperimentContext";

export default function CheckoutButton() {
  const variant = useExperiment();

  return <button>{variant === "A" ? "Buy Now" : "Complete Purchase"}</button>;
}
```

> **Note:** This example uses `Math.random()` only for demonstration. In production, use a server-side or SDK-provided assignment to ensure users consistently receive the same variant across sessions.

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript**
- React 18+
- ES Modules

Avoid **Create React App**, as it is deprecated.

### Common experimentation platforms

- **LaunchDarkly** – Feature flags and experimentation
- **Optimizely** – Enterprise experimentation
- **GrowthBook** – Open-source feature flags and A/B testing
- **Statsig** – Feature management and experimentation
- **PostHog** – Product analytics with experiments

### Why Vite?

- Fast development server
- Native ESM support
- Optimized production builds
- Excellent TypeScript support

### ESM vs CommonJS

Modern React applications should use **ES Modules (ESM)**. Vite bundles dependencies with Rollup for production while providing a fast ESM-based development experience.

---

# Performance

### Avoid unnecessary re-renders

Memoize expensive experiment-dependent calculations:

```tsx
const config = useMemo(() => buildConfig(variant), [variant]);
```

Memoize callbacks passed to children:

```tsx
const handleClick = useCallback(() => {
  trackConversion();
}, []);
```

Use `React.memo` for components that receive stable props.

---

### Lazy-load experiment code

If an experiment introduces a large component:

```tsx
const NewCheckout = React.lazy(() => import("./NewCheckout"));
```

Load it only for users assigned to that variant.

---

### Server-side assignment

Prefer assigning variants on the server or edge for:

- Consistent SSR/CSR rendering
- Reduced layout shifts
- SEO-sensitive pages
- Stable hydration

---

### Profiling

Use:

- React DevTools Profiler
- Browser Performance panel
- Lighthouse
- Bundle analyzer

Verify that experiment code does not significantly increase bundle size or render time.

---

# Testing

Use **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react jsdom
```

Example:

```tsx
render(
  <ExperimentContext.Provider value="B">
    <CheckoutButton />
  </ExperimentContext.Provider>,
);

expect(screen.getByText("Complete Purchase")).toBeInTheDocument();
```

Also add integration tests to verify:

- Correct variant rendering
- Exposure tracking
- Conversion event tracking
- Sticky assignment behavior

Use **Playwright** for end-to-end tests covering complete experiment flows.

---

# Ops & Deployment

- Roll out experiments gradually (e.g., 1% → 10% → 50% → 100%).
- Keep experiment assignment sticky across sessions and devices where appropriate.
- Track exposure before measuring conversions to avoid biased results.
- Log experiment IDs and variants for debugging and analytics.
- Remove completed experiments promptly to reduce technical debt and bundle size.
- For SSR frameworks (e.g., Next.js), perform variant assignment on the server when possible to avoid hydration mismatches.

---

# Pitfalls

- **Assigning variants on every render**, causing users to switch experiences. Persist assignments using an SDK, cookie, or user profile.
- **Running multiple overlapping experiments** on the same UI without considering interaction effects, which can invalidate results.
- **Leaving completed experiment code** in the codebase, increasing maintenance costs and bundle size.

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
