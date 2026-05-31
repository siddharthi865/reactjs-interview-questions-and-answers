# Set 9

| S.No. | Question                                                                                                                                               |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you handle API errors globally in React?](#question-1-how-do-you-handle-api-errors-globally-in-react)                                          |
| 2.    | [How do you prevent memory leaks with subscriptions in React?](#question-2-how-do-you-prevent-memory-leaks-with-subscriptions-in-react)                |
| 3.    | [How do you use React Context with TypeScript?](#question-3-how-do-you-use-react-context-with-typescript)                                              |
| 4.    | [How do you implement tab navigation in React?](#question-4-how-do-you-implement-tab-navigation-in-react)                                              |
| 5.    | [How do you implement a modal component with portals?](#question-5-how-do-you-implement-a-modal-component-with-portals)                                |
| 6.    | [How do you handle multiple async API calls simultaneously in React?](#question-6-how-do-you-handle-multiple-async-api-calls-simultaneously-in-react)  |
| 7.    | [How do you optimize re-renders in large forms?](#question-7-how-do-you-optimize-re-renders-in-large-forms)                                            |
| 8.    | [How do you dynamically import components in React?](#question-8-how-do-you-dynamically-import-components-in-react)                                    |
| 9.    | [How do you implement a responsive layout in React?](#question-9-how-do-you-implement-a-responsive-layout-in-react)                                    |
| 10.   | [How do you handle global notifications or toast messages?](#question-10-how-do-you-handle-global-notifications-or-toast-messages)                     |
| 11.   | [How does React Fiber improve performance over older React versions?](#question-11-how-does-react-fiber-improve-performance-over-older-react-versions) |
| 12.   | [How do you handle Suspense for data fetching?](#question-12-how-do-you-handle-suspense-for-data-fetching)                                             |
| 13.   | [How do you implement server-side rendering (SSR) without Next.js?](#question-13-how-do-you-implement-server-side-rendering-ssr-without-nextjs)        |
| 14.   | [How do you implement incremental static regeneration (ISR)?](#question-14-how-do-you-implement-incremental-static-regeneration-isr)                   |
| 15.   | [Explain the difference between hydration and mounting in SSR](#question-15-explain-the-difference-between-hydration-and-mounting-in-ssr)              |
| 16.   | [How do you implement code-splitting at route and component level?](#question-16-how-do-you-implement-code-splitting-at-route-and-component-level)     |
| 17.   | [How do you handle authentication with OAuth2 in React?](#question-17-how-do-you-handle-authentication-with-oauth2-in-react)                           |
| 18.   | [How do you implement protected routes in React?](#question-18-how-do-you-implement-protected-routes-in-react)                                         |
| 19.   | [How do you implement role-based access control (RBAC) in React apps?](#question-19-how-do-you-implement-role-based-access-control-rbac-in-react-apps) |

## Question 1. How do you handle API errors globally in React?

# Short answer

Handle API errors globally by centralizing HTTP requests (e.g., with an Axios instance or Fetch wrapper), using **interceptors/middleware** to normalize errors, integrating with a global state or context for user notifications, handling authentication failures (401/403), logging errors to monitoring tools, and using **React Error Boundaries** for rendering errors (not API errors). For data fetching, libraries like **React Query (TanStack Query)** or **SWR** provide built-in global error handling and retry mechanisms.

---

# Explanation

In production React applications, you should avoid handling API errors separately in every component. Instead, create a single network layer that is responsible for:

- Sending requests
- Parsing responses
- Converting different backend error formats into a common structure
- Handling authentication errors
- Logging errors
- Displaying global notifications
- Triggering retries when appropriate

A common architecture looks like:

```
React Components
        │
        ▼
API Service Layer
        │
        ▼
Axios / Fetch Wrapper
        │
        ▼
Interceptors
        │
 ┌──────┴─────────┐
 │                │
 ▼                ▼
Toast        Error Logging
 │                │
 ▼                ▼
UI         Sentry/Datadog
```

### React 18 considerations

React 18 introduced:

- Concurrent Rendering
- Automatic Batching
- Suspense improvements

API errors themselves are **not caught by Error Boundaries** because they occur asynchronously.

Instead:

- handle them inside async functions
- let React Query manage them
- show fallback UI
- retry failed requests
- invalidate cache when necessary

---

## Common global error handling flow

```
User clicks button
      │
      ▼
API Request
      │
      ▼
Interceptor
      │
 ┌────┴───────────┐
 │                │
401?           Other Error?
 │                │
 ▼                ▼
Logout      Normalize Error
 │                │
 ▼                ▼
Redirect     Toast
 │                │
 ▼                ▼
Log Error     Return Promise.reject()
```

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest react-api-errors -- --template react-ts
cd react-api-errors
npm install
npm install axios react-hot-toast
npm run dev
```

### api.ts

```tsx
import axios from "axios";
import { toast } from "react-hot-toast";

export const api = axios.create({
  baseURL: "https://api.example.com",
});

api.interceptors.response.use(
  (response) => response,
  (error) => {
    const status = error.response?.status;

    if (status === 401) {
      toast.error("Session expired. Please login again.");
      // logout()
      // navigate("/login")
    } else if (status >= 500) {
      toast.error("Server error. Please try again later.");
    } else {
      toast.error(error.response?.data?.message ?? "Something went wrong.");
    }

    // Send to monitoring service
    console.error(error);

    return Promise.reject(error);
  },
);
```

### UserList.tsx

```tsx
import { useEffect, useState } from "react";
import { api } from "./api";

type User = {
  id: number;
  name: string;
};

export default function UserList() {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    api
      .get<User[]>("/users")
      .then((res) => setUsers(res.data))
      .catch(() => {
        // Global interceptor already handled notification
      });
  }, []);

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

Notice that components remain focused on rendering while the interceptor manages cross-cutting concerns like notifications and authentication.

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript** for fast local development using native ESM and fast HMR.
- **Next.js App Router** if SSR, streaming, or React Server Components are required.
- **Remix** is also a strong choice for route-based data loading and error boundaries.

Avoid **Create React App (CRA)** since it is deprecated.

### Modern data-fetching libraries

- **TanStack Query**
  - Global error callbacks
  - Automatic retries
  - Request deduplication
  - Cache invalidation
  - Background refetching

- **SWR**
  - Stale-While-Revalidate
  - Global fetcher
  - Retry support

### ESM vs CommonJS

- Modern React projects use **ES Modules (ESM)**.
- Vite serves native ESM during development and bundles efficiently for production.

---

# Performance

Global error handling also improves performance because:

- duplicated error logic is eliminated
- retry policies are centralized
- duplicate API calls can be avoided with caching

Optimization techniques include:

- **React Profiler** to identify unnecessary renders after failed requests.
- **React.memo** to prevent unaffected components from re-rendering.
- **useMemo** for expensive derived values.
- **useCallback** for stable callbacks passed to children.
- **React.lazy** and `Suspense` for code splitting of error-heavy routes.
- Use **TanStack Query** cache instead of refetching identical data after transient failures.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test idea:

```tsx
it("shows an error toast on API failure", async () => {
  // Mock Axios request
  // Render component
  // Assert that the toast is displayed
});
```

For end-to-end testing, use **Playwright** to verify scenarios such as expired sessions (401 redirects), offline behavior, and server errors.

---

# Ops & Deployment

In production, pair global error handling with:

- **Error Boundaries** for rendering errors (they do not catch asynchronous API failures).
- Structured logging with monitoring platforms such as Sentry, Datadog, or New Relic.
- Correlation/request IDs to trace failures across frontend and backend services.
- SSR/CSR considerations: handle server-side fetch failures separately from client-side errors.
- CDN caching and proper HTTP cache headers to reduce unnecessary requests.
- Bundle size management by importing only the parts of libraries you need and using route-level code splitting.

---

# Pitfalls

- **Do not rely on Error Boundaries for API failures.** They only catch rendering, lifecycle, and constructor errors in React components.
- **Avoid showing duplicate notifications.** If a global interceptor displays a toast, components generally shouldn't display another one for the same failure.
- **Normalize backend error formats.** Different services may return different payloads; convert them into a consistent error shape before exposing them to the UI.

## Question 2. How do you prevent memory leaks with subscriptions in React?

# Short answer

Prevent memory leaks by **cleaning up subscriptions inside the `useEffect` cleanup function**. Always unsubscribe from event listeners, WebSocket connections, timers, observers, and external libraries when a component unmounts or when dependencies change. In React 18, effects may mount, clean up, and re-run more than once in development (Strict Mode), so cleanup logic must be idempotent.

---

# Explanation

A memory leak occurs when a component is removed from the UI, but resources it created continue to exist. Common causes include:

- Event listeners (`window`, `document`)
- WebSocket connections
- Server-Sent Events (SSE)
- Timers (`setInterval`, `setTimeout`)
- Observers (`IntersectionObserver`, `ResizeObserver`, `MutationObserver`)
- RxJS subscriptions
- External libraries that require explicit cleanup

React's `useEffect` hook supports cleanup by returning a function:

```tsx
useEffect(() => {
  // Subscribe or allocate resource

  return () => {
    // Cleanup before unmount or dependency change
  };
}, []);
```

The cleanup function runs:

1. Before the component unmounts.
2. Before the effect re-runs because one of its dependencies changed.

### React 18 rendering behavior

React 18 introduced:

- Concurrent Rendering
- Automatic Batching
- Development **Strict Mode** effect re-execution

In development, Strict Mode intentionally mounts, cleans up, and remounts components to help detect unsafe side effects. If your cleanup is missing or incomplete, you'll often see duplicate event handlers, multiple WebSocket connections, or repeated timers during development—an indication that the effect isn't managing resources correctly.

---

## Common subscription lifecycle

```text
Component Mount
      │
      ▼
useEffect runs
      │
      ▼
Subscribe / Add Listener
      │
      ▼
Component updates (dependencies change)?
      │
   Yes ▼
Cleanup previous subscription
      │
      ▼
Create new subscription
      │
      ▼
Component Unmount
      │
      ▼
Final Cleanup
```

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest react-subscriptions -- --template react-ts
cd react-subscriptions
npm install
npm run dev
```

### WebSocket subscription with cleanup

```tsx
import { useEffect, useState } from "react";

export default function Notifications() {
  const [messages, setMessages] = useState<string[]>([]);

  useEffect(() => {
    const socket = new WebSocket("wss://example.com/ws");

    socket.onmessage = (event) => {
      setMessages((prev) => [...prev, event.data]);
    };

    return () => {
      socket.close(); // Prevent memory leak
    };
  }, []);

  return (
    <ul>
      {messages.map((message, index) => (
        <li key={index}>{message}</li>
      ))}
    </ul>
  );
}
```

The WebSocket is opened when the component mounts and is always closed when the component unmounts, preventing lingering connections.

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript** for fast development using native ESM and Hot Module Replacement (HMR).
- **Next.js App Router** when SSR, React Server Components, or streaming are needed.
- **Remix** for route-based data loading and cleanup.

Avoid **Create React App (CRA)** since it is deprecated.

### ESM vs CommonJS

- Modern React projects use **ES Modules (ESM)**.
- Vite serves ESM during development and bundles optimized production assets.

---

# Performance

Proper cleanup improves both memory usage and application performance.

Best practices:

- Use **React Profiler** to identify unnecessary re-renders caused by repeated subscriptions.
- Use **React.memo** to avoid re-rendering child components unnecessarily.
- Use **useCallback** when passing event handlers to subscription APIs that depend on stable references.
- Use **useMemo** for expensive derived values rather than recalculating after every update.
- Code split subscription-heavy routes using `React.lazy()` and `Suspense`.
- For server data, prefer **TanStack Query** or **SWR**, which manage subscriptions, caching, retries, and cancellation internally.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test idea:

```tsx
it("closes the WebSocket when the component unmounts", () => {
  // Mock the WebSocket API
  // Render the component
  // Unmount it
  // Assert that socket.close() was called
});
```

For end-to-end verification, use **Playwright** to ensure navigating away from a page doesn't leave active connections or duplicate subscriptions.

---

# Ops & Deployment

- Use **Error Boundaries** for rendering errors; they do not clean up or catch asynchronous subscription issues.
- Log unexpected connection closures or subscription failures to monitoring platforms such as Sentry or Datadog.
- Ensure subscriptions are established only on the client when using SSR frameworks like Next.js.
- Monitor browser memory usage and long-lived connections in production using browser developer tools and observability platforms.

---

# Pitfalls

- **Forgetting the cleanup function**, leaving event listeners, timers, or sockets active after unmount.
- **Using incorrect dependency arrays**, causing multiple subscriptions to accumulate instead of replacing the previous one.
- **Assuming Strict Mode causes bugs**; duplicate effect execution in development is intentional and helps reveal missing cleanup logic.

## Question 3. How do you use React Context with TypeScript?

## Question 4. How do you implement tab navigation in React?

## Question 5. How do you implement a modal component with portals?

## Question 6. How do you handle multiple async API calls simultaneously in React?

## Question 7. How do you optimize re-renders in large forms?

## Question 8. How do you dynamically import components in React?

## Question 9. How do you implement a responsive layout in React?

## Question 10. How do you handle global notifications or toast messages?

## Question 11. How does React Fiber improve performance over older React versions?

## Question 12. How do you handle Suspense for data fetching?

## Question 13. How do you implement server-side rendering (SSR) without Next.js?

## Question 14. How do you implement incremental static regeneration (ISR)?

## Question 15. Explain the difference between hydration and mounting in SSR

## Question 16. How do you implement code-splitting at route and component level?

## Question 17. How do you handle authentication with OAuth2 in React?

## Question 18. How do you implement protected routes in React?

## Question 19. How do you implement role-based access control (RBAC) in React apps?
