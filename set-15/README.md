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

## Question 3. How do you integrate GraphQL with Apollo Client?

## Question 4. How do you implement lazy-loaded routes with React Router?

## Question 5. How do you implement React Query for data fetching and caching?

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
