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

# Short answer

Use **React Context with TypeScript** by creating a strongly typed context, providing it through a Provider component, and consuming it via a custom hook. A custom hook ensures consumers are used within the Provider and eliminates repetitive null checks.

---

# Explanation

React Context allows you to share state across the component tree without prop drilling. With TypeScript, the goal is to:

- Define a type for the context value.
- Create the context with `undefined` as the default value.
- Wrap your application (or part of it) with a Provider.
- Expose a custom hook that throws an error if used outside the Provider.

This pattern provides:

- **Type safety**
- **Better developer experience (IntelliSense)**
- **Runtime safety**
- **Reusable state management**

### Context architecture

```text
App
│
└── ThemeProvider
      │
      ├── Header
      ├── Sidebar
      └── Content
             │
             ├── Button
             └── Card
```

Every component inside `ThemeProvider` can access the shared context.

### React 18 considerations

React 18 introduced:

- Concurrent Rendering
- Automatic Batching

When the context value changes:

- Every consumer using that context re-renders.
- React batches multiple state updates automatically.
- Context updates are propagated consistently across concurrent renders.

For frequently changing state (e.g., typing into an input), Context can trigger many re-renders. In such cases, consider:

- Splitting contexts by concern (Theme, Auth, Settings).
- Memoizing the provider value with `useMemo`.
- Using state libraries like Redux, Zustand, or Jotai for highly dynamic global state.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest react-context-ts -- --template react-ts
cd react-context-ts
npm install
npm run dev
```

### `ThemeContext.tsx`

```tsx
import { createContext, useContext, useMemo, useState, ReactNode } from "react";

type Theme = "light" | "dark";

interface ThemeContextType {
  theme: Theme;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

interface ThemeProviderProps {
  children: ReactNode;
}

export function ThemeProvider({ children }: ThemeProviderProps) {
  const [theme, setTheme] = useState<Theme>("light");

  const value = useMemo(
    () => ({
      theme,
      toggleTheme: () =>
        setTheme((prev) => (prev === "light" ? "dark" : "light")),
    }),
    [theme],
  );

  return (
    <ThemeContext.Provider value={value}>{children}</ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);

  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }

  return context;
}
```

### `App.tsx`

```tsx
import { ThemeProvider, useTheme } from "./ThemeContext";

function Home() {
  const { theme, toggleTheme } = useTheme();

  return (
    <>
      <h2>Current Theme: {theme}</h2>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <Home />
    </ThemeProvider>
  );
}
```

This approach is type-safe, avoids unnecessary null assertions (`!`), and provides a clean API through the `useTheme` hook.

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript** for fast startup, HMR, and native ESM support.
- **Next.js App Router** if you need SSR, React Server Components, or streaming.
- **Remix** for route-centric data loading.

Avoid **Create React App (CRA)** because it is deprecated.

### ESM vs CommonJS

- Modern React applications use **ES Modules (ESM)**.
- Vite leverages native ESM during development for faster builds and efficient production bundling.

---

# Performance

Context is convenient but should be used thoughtfully.

### Best practices

- Memoize the provider value using `useMemo`.
- Split large contexts into smaller, focused contexts (e.g., Theme, Auth, User Preferences).
- Avoid storing rapidly changing state (such as keystrokes or mouse positions) in Context.
- Use **React Profiler** to identify unnecessary re-renders.
- Apply **React.memo** to consumers where appropriate.
- Use **useCallback** for stable callback references if passing functions deeply.
- Use `React.lazy` and `Suspense` for route-level code splitting.
- For server state, prefer **TanStack Query** instead of Context, as it provides caching, retries, and background refetching.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import { ThemeProvider } from "./ThemeContext";
import App from "./App";

test("renders theme provider", () => {
  render(
    <ThemeProvider>
      <App />
    </ThemeProvider>,
  );

  expect(screen.getByText(/Current Theme/i)).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright** to verify theme changes, authentication flows, or other context-driven behavior.

---

# Ops & Deployment

- Wrap top-level providers in a predictable order (e.g., Auth → Theme → Query Client).
- Use **Error Boundaries** to isolate rendering failures; they do not handle asynchronous errors or API failures.
- Log unexpected provider or context errors to monitoring platforms such as Sentry or Datadog.
- In SSR frameworks like Next.js, ensure context values are consistent between server and client to avoid hydration mismatches.
- Keep provider scopes as narrow as practical to minimize unnecessary re-renders.

---

# Pitfalls

- **Creating the context with a fake default value** (e.g., `{}` as `ThemeContextType`) instead of `undefined`, which can hide provider configuration mistakes.
- **Not memoizing the provider value**, causing all consumers to re-render on every provider render.
- **Putting unrelated state into one context**, leading to unnecessary updates across the application.

## Question 4. How do you implement tab navigation in React?

# Short answer

Tab navigation in React is implemented by maintaining an **active tab state** and conditionally rendering tab panels based on that state. For scalable apps, you structure tabs as a **controlled component**, optionally sync them with the URL (React Router), and optimize rendering by memoizing tab panels or lazy-loading them.

---

# Explanation

At its core, tab navigation is a **state-driven UI pattern**:

- A tab bar sets a `selectedTab` state.
- Clicking a tab updates that state.
- The UI renders only the matching panel.

### Component architecture

A clean tab system typically has:

- `Tabs` (state container)
- `TabList` (navigation UI)
- `Tab` (individual trigger)
- `TabPanels` (content area)
- `TabPanel` (individual content section)

```text id="tab-arch"
Tabs (state: activeTab)
│
├── TabList
│     ├── Tab "Profile"
│     ├── Tab "Settings"
│     └── Tab "Billing"
│
└── TabPanels
      ├── Panel Profile
      ├── Panel Settings
      └── Panel Billing
```

### React 18 behavior

- Tab switching triggers re-render of the Tabs component.
- Only the active panel should be rendered or visible.
- With concurrent rendering, React may pause/resume rendering of tab transitions, so avoid expensive computations inside render paths.
- Automatic batching ensures multiple state updates (e.g., analytics + tab change) are grouped efficiently.

### State management trade-offs

- **useState (local state)** → simplest, best for isolated tabs.
- **Context** → useful if tabs need to be shared across deeply nested components.
- **URL state (React Router)** → best for persistence, deep linking, back/forward navigation.
- **State machines (XState)** → best for complex multi-step/tab workflows.

---

# Example

**Vite + React + TypeScript**

Create project:

```bash id="vite-tab"
npm create vite@latest react-tabs -- --template react-ts
cd react-tabs
npm install
npm run dev
```

## Basic tab system (local state)

```tsx id="tabs1"
import { useState } from "react";

type TabKey = "home" | "profile" | "settings";

export default function Tabs() {
  const [activeTab, setActiveTab] = useState<TabKey>("home");

  return (
    <div>
      {/* Tab Buttons */}
      <div style={{ display: "flex", gap: "8px" }}>
        <button onClick={() => setActiveTab("home")}>Home</button>
        <button onClick={() => setActiveTab("profile")}>Profile</button>
        <button onClick={() => setActiveTab("settings")}>Settings</button>
      </div>

      {/* Tab Panels */}
      <div style={{ marginTop: "16px" }}>
        {activeTab === "home" && <div>🏠 Home Content</div>}
        {activeTab === "profile" && <div>👤 Profile Content</div>}
        {activeTab === "settings" && <div>⚙️ Settings Content</div>}
      </div>
    </div>
  );
}
```

---

## Scalable reusable tab component

```tsx id="tabs2"
import { ReactNode, useState } from "react";

type Tab = {
  id: string;
  label: string;
  content: ReactNode;
};

export function Tabs({ tabs }: { tabs: Tab[] }) {
  const [active, setActive] = useState(tabs[0]?.id);

  return (
    <div>
      <div style={{ display: "flex", gap: "10px" }}>
        {tabs.map((tab) => (
          <button
            key={tab.id}
            onClick={() => setActive(tab.id)}
            style={{
              fontWeight: active === tab.id ? "bold" : "normal",
            }}
          >
            {tab.label}
          </button>
        ))}
      </div>

      <div style={{ marginTop: "16px" }}>
        {tabs.find((t) => t.id === active)?.content}
      </div>
    </div>
  );
}
```

### Usage

```tsx id="tabs3"
import { Tabs } from "./Tabs";

export default function App() {
  return (
    <Tabs
      tabs={[
        { id: "home", label: "Home", content: <div>Home Page</div> },
        { id: "profile", label: "Profile", content: <div>User Profile</div> },
        {
          id: "settings",
          label: "Settings",
          content: <div>Settings Panel</div>,
        },
      ]}
    />
  );
}
```

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript** → fast HMR, simple setup, modern ESM-based bundling.
- **Next.js App Router** → best if tabs represent routes (e.g., `/account/profile`, `/account/settings`).
- **Remix** → good for data-driven tab routes.

Avoid **Create React App (CRA)** (deprecated).

### ESM vs CommonJS

- Modern tab components use **ESM imports/exports**.
- Vite supports native ESM for faster dev reloads.

---

# Performance

Tab navigation can become expensive if all tab content mounts at once.

### Optimizations

- **Conditional rendering**: render only active tab.
- **Lazy loading tabs**:

  ```tsx
  const Settings = React.lazy(() => import("./Settings"));
  ```

- Wrap tabs with **React.memo** to prevent unnecessary re-renders.
- Use **useMemo** for tab definitions if they are static.
- Use **useCallback** for tab click handlers in large lists.
- Use **React Profiler** to detect unnecessary re-renders during tab switches.
- Consider **virtualization** if tabs contain large lists.

### Advanced optimization

- Keep inactive tabs unmounted to avoid DOM memory usage.
- Or keep mounted but hidden if you need state persistence.

---

# Testing

Use **Vitest + React Testing Library**:

```bash id="test-tabs"
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

### Example test

```tsx id="test1"
import { render, screen, fireEvent } from "@testing-library/react";
import Tabs from "./Tabs";

test("switches tabs on click", () => {
  render(<Tabs />);

  fireEvent.click(screen.getByText("Profile"));

  expect(screen.getByText("👤 Profile Content")).toBeInTheDocument();
});
```

For E2E:

- Use **Playwright** to verify URL sync, keyboard navigation, and accessibility.

---

# Ops & Deployment

- Ensure tab state resets correctly on route change (if not URL-driven).
- If tabs are route-based, use React Router or Next.js App Router for SSR consistency.
- Use Error Boundaries to isolate failures in tab panels.
- Monitor performance regressions using tools like Sentry or Datadog.
- Ensure hydration consistency if using SSR frameworks (Next.js).

---

# Pitfalls

- ❌ Rendering all tab panels at once (bad for performance).
- ❌ Mixing tab state with unrelated global state.
- ❌ Not preserving state when switching tabs (if UX expects persistence).
- ❌ Using index as key for dynamic tabs (can cause UI bugs).
- ❌ Not handling accessibility (keyboard navigation, ARIA roles).

## Question 5. How do you implement a modal component with portals?

# Short answer

A modal in React is best implemented using **React Portals** to render it outside the normal DOM hierarchy (usually into `document.body`), while still maintaining React state and context. This avoids CSS issues like `overflow: hidden`, stacking context problems, and z-index conflicts.

---

# Explanation

A modal has two key requirements:

1. **UI separation from layout flow**
   - It should visually overlay everything.
   - It should not be constrained by parent containers.

2. **Logical integration with React tree**
   - It should still receive props, state, and context.
   - It should respond to React lifecycle events.

React Portals solve this by rendering a component into a DOM node outside the parent hierarchy:

```text id="portal-arch"
React Tree
   │
   ├── App
   │     ├── Page
   │     │     └── Button (opens modal)
   │
   └── Portal Root (document.body)
         └── Modal
```

### React 18 behavior

- Portals are fully compatible with concurrent rendering.
- Modal state updates are batched automatically.
- Event bubbling still works through React’s synthetic event system (even across portals).
- Focus management becomes critical due to concurrent UI updates.

---

### Why not just CSS overlays?

Without portals, modals often break due to:

- `overflow: hidden` clipping
- z-index stacking context issues
- parent `transform` creating new stacking contexts
- layout shifts affecting positioning

Portals bypass all of that by rendering at the root level.

---

# Example

**Vite + React + TypeScript**

Create project:

```bash id="modal-vite"
npm create vite@latest react-modal-portal -- --template react-ts
cd react-modal-portal
npm install
npm run dev
```

---

## Step 1: Add portal root

In `index.html`:

```html id="html-root"
<body>
  <div id="root"></div>
  <div id="modal-root"></div>
</body>
```

---

## Step 2: Modal component using portal

```tsx id="modal1"
import { ReactNode, useEffect } from "react";
import { createPortal } from "react-dom";

type ModalProps = {
  open: boolean;
  onClose: () => void;
  children: ReactNode;
};

export default function Modal({ open, onClose, children }: ModalProps) {
  if (!open) return null;

  const modalRoot = document.getElementById("modal-root");
  if (!modalRoot) return null;

  // Close on ESC
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === "Escape") onClose();
    };

    window.addEventListener("keydown", handleKeyDown);

    return () => {
      window.removeEventListener("keydown", handleKeyDown);
    };
  }, [onClose]);

  return createPortal(
    <div
      onClick={onClose}
      style={{
        position: "fixed",
        inset: 0,
        background: "rgba(0,0,0,0.5)",
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
      }}
    >
      <div
        onClick={(e) => e.stopPropagation()}
        style={{
          background: "white",
          padding: "20px",
          borderRadius: "8px",
          minWidth: "300px",
        }}
      >
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>,
    modalRoot,
  );
}
```

---

## Step 3: Usage

```tsx id="modal2"
import { useState } from "react";
import Modal from "./Modal";

export default function App() {
  const [open, setOpen] = useState(false);

  return (
    <div>
      <h1>Portal Modal Example</h1>
      <button onClick={() => setOpen(true)}>Open Modal</button>

      <Modal open={open} onClose={() => setOpen(false)}>
        <h2>Hello Modal</h2>
        <p>This is rendered using React Portal</p>
      </Modal>
    </div>
  );
}
```

---

# Tooling & Setup

### Preferred stack

- **Vite + React + TypeScript** → fast dev server, native ESM, simple DOM control for portals.
- **Next.js App Router** → useful if modal needs SSR-safe rendering patterns (use `use client` and dynamic imports).
- **Remix** → route-aware modals (modals as nested routes).

Avoid **Create React App (CRA)** (deprecated).

### ESM vs CommonJS

- Portals rely on browser DOM APIs (`document.getElementById`)
- Modern apps use **ESM-based bundlers (Vite, Next.js, Turbopack)** for optimal tree-shaking.

---

# Performance

Portals themselves are lightweight, but modals can become expensive due to:

### Optimization strategies

- **Conditional rendering**
  - Don’t mount modal when closed (`if (!open) return null`)

- **React.memo**
  - Prevent unnecessary re-renders of modal content

- **useCallback**
  - Stabilize `onClose` handlers

- **Focus management**
  - Prevent layout thrashing on open/close

- **Lazy load heavy modal content**

  ```tsx
  const SettingsModal = React.lazy(() => import("./SettingsModal"));
  ```

- Use **React Profiler** to ensure modal open/close is not triggering cascading re-renders
- Avoid unnecessary global state updates when modal opens

---

# Testing

Use **Vitest + React Testing Library**:

```bash id="modal-test"
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

### Example test

```tsx id="modal-test-1"
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("opens and closes modal", () => {
  render(<App />);

  fireEvent.click(screen.getByText("Open Modal"));
  expect(screen.getByText("Hello Modal")).toBeInTheDocument();

  fireEvent.click(screen.getByText("Close"));
  expect(screen.queryByText("Hello Modal")).not.toBeInTheDocument();
});
```

For E2E testing:

- Use **Playwright** to test:
  - ESC key close behavior
  - click-outside behavior
  - focus trap behavior

---

# Ops & Deployment

- Ensure modal root (`#modal-root`) exists in production HTML.
- Use **Error Boundaries** to isolate modal rendering failures.
- Log modal-related runtime errors to observability tools like Sentry or Datadog.
- In SSR frameworks (Next.js):
  - ensure portals only render on client
  - avoid accessing `document` during server render

- Be careful with z-index layering in design systems (modals often conflict with dropdowns, tooltips, etc.)

---

# Pitfalls

- ❌ Forgetting to add a portal root (`#modal-root`)
- ❌ Accessing `document` during SSR (breaks Next.js server rendering)
- ❌ Not handling focus trapping (accessibility issue)
- ❌ Leaving event listeners (e.g., ESC key) without cleanup
- ❌ Allowing background scroll when modal is open (UX issue)

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
