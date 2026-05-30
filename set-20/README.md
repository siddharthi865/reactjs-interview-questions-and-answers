# Set 20

| #   | Question                                                                                                                                                                            |
| --- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement offline-first React apps with service workers?](#question-1-how-do-you-implement-offline-first-react-apps-with-service-workers)                               |
| 2   | [How do you implement analytics tracking (Google Analytics, Segment) in React?](#question-2-how-do-you-implement-analytics-tracking-google-analytics-segment-in-react)              |
| 3   | [How do you handle cross-tab state synchronization using localStorage or IndexedDB?](#question-3-how-do-you-handle-cross-tab-state-synchronization-using-localstorage-or-indexeddb) |
| 4   | [How do you implement dynamic forms driven by JSON schema?](#question-4-how-do-you-implement-dynamic-forms-driven-by-json-schema)                                                   |
| 5   | [How do you handle large forms efficiently using React Hook Form?](#question-5-how-do-you-handle-large-forms-efficiently-using-react-hook-form)                                     |
| 6   | [How do you implement multi-language support (i18n) with dynamic content?](#question-6-how-do-you-implement-multi-language-support-i18n-with-dynamic-content)                       |
| 7   | [How do you implement virtualization for tables with thousands of rows?](#question-7-how-do-you-implement-virtualization-for-tables-with-thousands-of-rows)                         |
| 8   | [How do you implement drag-and-drop dashboards with widgets?](#question-8-how-do-you-implement-drag-and-drop-dashboards-with-widgets)                                               |
| 9   | [How do you implement A/B testing in React applications?](#question-9-how-do-you-implement-ab-testing-in-react-applications)                                                        |
| 10  | [How do you monitor performance using React Profiler and Lighthouse?](#question-10-how-do-you-monitor-performance-using-react-profiler-and-lighthouse)                              |
| 11  | [How do you integrate Jest and React Testing Library for complex component tests?](#question-11-how-do-you-integrate-jest-and-react-testing-library-for-complex-component-tests)    |
| 12  | [How do you implement accessibility best practices with ARIA attributes?](#question-12-how-do-you-implement-accessibility-best-practices-with-aria-attributes)                      |
| 13  | [How do you implement dynamic routing based on user roles?](#question-13-how-do-you-implement-dynamic-routing-based-on-user-roles)                                                  |
| 14  | [How do you implement server-side data fetching with Next.js getServerSideProps?](#question-14-how-do-you-implement-server-side-data-fetching-with-nextjs-getserversideprops)       |
| 15  | [How do you implement Incremental Static Regeneration (ISR) in Next.js?](#question-15-how-do-you-implement-incremental-static-regeneration-isr-in-nextjs)                           |
| 16  | [How do you optimize bundle size using tree-shaking and dynamic imports?](#question-16-how-do-you-optimize-bundle-size-using-tree-shaking-and-dynamic-imports)                      |
| 17  | [How do you implement advanced memoization strategies for large lists?](#question-17-how-do-you-implement-advanced-memoization-strategies-for-large-lists)                          |
| 18  | [How do you prevent unnecessary network requests in high-frequency API calls?](#question-18-how-do-you-prevent-unnecessary-network-requests-in-high-frequency-api-calls)            |
| 19  | [How do you implement multi-window or multi-tab synchronization for user sessions?](#question-19-how-do-you-implement-multi-window-or-multi-tab-synchronization-for-user-sessions)  |
| 20  | [How do you architect a large-scale React app for maintainability and scalability?](#question-20-how-do-you-architect-a-large-scale-react-app-for-maintainability-and-scalability)  |

## Question 1. How do you implement offline-first React apps with service workers?

# How do you implement offline-first React apps with service workers?

## Short answer

An **offline-first React app** uses a **Service Worker** to cache application assets and API responses so the app continues to work without an internet connection. Modern React applications typically use **Vite + vite-plugin-pwa**, **Next.js with PWA plugins**, or **Workbox** instead of manually writing service worker logic. The service worker intercepts network requests and serves cached resources based on caching strategies like **Cache First**, **Network First**, or **Stale While Revalidate**.

---

# Explanation

A **Service Worker** is a JavaScript file that runs separately from your React application in the browser. It acts as a programmable proxy between your app and the network.

Typical request flow:

```
Browser
    │
    ▼
Service Worker
   │      │
Cache   Network
   │      │
   └──► Response
```

An offline-first React application generally consists of:

1. **App Shell**
   - HTML
   - JavaScript bundles
   - CSS
   - Fonts
   - Icons

These are cached during installation so the application loads instantly even when offline.

2. **Runtime Cache**
   - API responses
   - Images
   - User avatars
   - JSON
   - Documents

These are cached as users browse the application.

3. **Background Sync (optional)**
   - Queue failed POST/PUT requests
   - Retry automatically when connectivity returns

4. **Cache Strategies**

| Strategy               | Best For                  |
| ---------------------- | ------------------------- |
| Cache First            | Images, fonts, icons      |
| Network First          | API data                  |
| Stale While Revalidate | Product lists, news feeds |
| Cache Only             | Versioned assets          |
| Network Only           | Payments, authentication  |

---

### React Architecture

A production React app usually separates responsibilities:

```
React Components
        │
        ▼
React Query / Redux Toolkit
        │
        ▼
Fetch API
        │
        ▼
Service Worker
        │
   Cache Storage
```

React components should never directly manipulate cache storage.

Instead:

- Components request data.
- React Query manages client cache.
- Service Worker manages browser cache.
- Backend remains the source of truth.

---

### React 18 considerations

React 18 features still work normally offline.

Examples include:

- Automatic batching
- Concurrent rendering
- Suspense
- Lazy loading

When connectivity changes:

```
offline
↓

render cached data

↓

connection restored

↓

background fetch

↓

React Query updates UI

↓

automatic batched render
```

---

### Caching Strategies

### Cache First

```
Cache?

Yes → Return cache

No

↓

Network

↓

Save cache
```

Good for:

- Logos
- Images
- CSS
- Fonts

---

### Network First

```
Network

↓

Success → Cache

↓

Return

↓

Failure

↓

Use cache
```

Best for:

- Dashboard
- User profile
- Orders

---

### Stale While Revalidate

```
Return cache immediately

↓

Fetch network

↓

Update cache

↓

Next request gets fresh data
```

Perfect for:

- News
- Blogs
- Products

---

# Example

**Scaffold a modern React app (Vite + TypeScript):**

```bash
npm create vite@latest offline-demo -- --template react-ts
cd offline-demo
npm i
npm i -D vite-plugin-pwa
npm run dev
```

**vite.config.ts**

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
        runtimeCaching: [
          {
            urlPattern: /^https:\/\/jsonplaceholder\.typicode\.com\//,
            handler: "NetworkFirst",
            options: {
              cacheName: "api-cache",
            },
          },
        ],
      },
    }),
  ],
});
```

**App.tsx**

```tsx
import { useEffect, useState } from "react";

type Todo = {
  id: number;
  title: string;
};

export default function App() {
  const [todos, setTodos] = useState<Todo[]>([]);

  useEffect(() => {
    fetch("https://jsonplaceholder.typicode.com/todos?_limit=5")
      .then((r) => r.json())
      .then(setTodos);
  }, []);

  return (
    <ul>
      {todos.map((todo) => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  );
}
```

The first successful request is cached by the service worker. If the network becomes unavailable later, the cached response is served according to the configured `NetworkFirst` strategy.

---

# Tooling & Setup

**Preferred stack:** **Vite + TypeScript + `vite-plugin-pwa`** for lightweight, production-ready PWAs. For server-rendered applications, **Next.js** can be paired with a PWA plugin. Avoid **Create React App (CRA)**, as it is deprecated.

- **Vite**: Fast development server using native ES modules (ESM) and Rollup for production builds.
- **ESM vs CommonJS**: Modern React tooling favors **ESM**, enabling efficient tree-shaking and native browser module support. CommonJS remains common in Node.js but is less suitable for browser bundles.
- **Dev server**: Use `npm run dev` during development and `npm run build` for optimized production assets. Test service workers using a production preview (`npm run preview`) because they are typically disabled or limited in development.

---

# Performance

- **React Profiler**: Measure unnecessary renders before optimizing caching behavior.
- **Memoization**: Use `React.memo`, `useMemo`, and `useCallback` to reduce re-renders when cached data updates.
- **Code splitting**: Use `React.lazy` and `Suspense` so only required chunks are downloaded and cached.
- **Cache strategies**:
  - Cache static assets aggressively.
  - Use `StaleWhileRevalidate` for frequently viewed content.
  - Use `NetworkFirst` for user-specific data.

- **Precaching**: Cache the application shell during service worker installation to enable instant offline startup.
- **Avoid cache bloat**: Version caches and remove outdated entries during service worker activation.

---

# Testing

Use **Vitest** with **React Testing Library** for component tests and **Playwright** for end-to-end validation of offline behavior.

Example commands:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

```bash
npx playwright test
```

For offline testing:

- Simulate offline mode in browser DevTools.
- Verify cached assets load correctly.
- Confirm API fallbacks behave as expected.
- Test service worker updates and cache invalidation.

---

# Ops & Deployment

- Add **Error Boundaries** to gracefully handle rendering failures independent of network state.
- Use structured logging (e.g., Sentry or OpenTelemetry) to monitor service worker lifecycle events and offline failures.
- **SSR vs CSR**:
  - CSR PWAs benefit most from service workers.
  - SSR frameworks (e.g., Next.js) can combine server rendering with offline support for static assets and selected API responses.

- Manage bundle size with tree-shaking, dynamic imports, and route-level code splitting.
- Serve assets from a CDN with immutable cache headers for hashed files, while allowing the service worker to control runtime caching and updates.

---

# Pitfalls

- **Do not cache everything**—stale authenticated or sensitive data can cause inconsistent behavior and security risks.
- **Version and clean caches** whenever deploying a new application build to avoid serving outdated assets.
- **Keep business logic out of the service worker**; it should focus on request interception, caching, synchronization, and update management.

## Question 2. How do you implement analytics tracking (Google Analytics, Segment) in React?

# How do you implement analytics tracking (Google Analytics, Segment) in React?

## Short answer

Analytics in React should be implemented using a **centralized analytics service** instead of calling Google Analytics or Segment directly from components. Track **page views**, **user events**, and **user properties** through a reusable abstraction. In React Router, page views are typically tracked using `useLocation`, while user interactions are tracked via a shared analytics utility. This approach makes it easy to switch providers (e.g., Google Analytics, Segment, Mixpanel, Amplitude) without changing application code.

---

# Explanation

A common mistake is sprinkling analytics calls throughout components:

```tsx
// ❌ Avoid
<button onClick={() => {
  gtag("event", "purchase");
}}>
```

Instead, create an analytics layer:

```text
React Components
        │
        ▼
Analytics Service
        │
        ▼
Google Analytics
Segment
Mixpanel
Amplitude
```

Benefits:

- Single source of truth
- Easier testing
- Vendor independence
- Consistent event naming
- Easier debugging

---

## Types of analytics

### 1. Page Views

Automatically track route changes.

Examples:

- Home
- Dashboard
- Product
- Checkout

---

### 2. User Events

Track meaningful interactions.

Examples:

```text
Button Click

Form Submit

Video Play

Purchase

Search

Login

Logout

Add to Cart
```

---

### 3. User Properties

Identify users.

Example:

```ts
{
  id: "123",
  role: "admin",
  plan: "premium"
}
```

Useful for segmentation and personalization.

---

### 4. Performance Metrics

Track:

- Core Web Vitals
- Load time
- API latency
- Largest Contentful Paint (LCP)
- Interaction to Next Paint (INP)
- Cumulative Layout Shift (CLS)

---

## React Architecture

A scalable structure:

```text
Pages
Components
      │
      ▼
analytics.ts
      │
      ▼
Provider SDK
(GA / Segment)
```

Never import provider SDKs directly into UI components.

---

## Tracking page views

With React Router:

```text
Location changes

↓

Track page view

↓

Analytics provider
```

This ensures every navigation is captured automatically.

---

## Tracking events

Good event names:

```text
user_login

product_view

checkout_started

checkout_completed

button_clicked
```

Avoid vague names like:

```text
click

button

submit

event1
```

Use descriptive, consistent naming conventions.

---

## React 18 considerations

React 18's automatic batching and concurrent rendering do not change analytics fundamentals, but keep these points in mind:

- Track events in **event handlers** or committed effects (`useEffect`), not during rendering.
- In development, **Strict Mode** intentionally invokes some lifecycle logic twice to help detect side effects. Guard page-view tracking to avoid duplicate events during development.
- Lazy-loaded routes can trigger page-view events after the route has finished loading, which generally reflects what the user actually sees.

---

# Example

**Scaffold a modern React app (Vite + TypeScript):**

```bash
npm create vite@latest analytics-demo -- --template react-ts
cd analytics-demo
npm i
npm i react-router-dom react-ga4
npm run dev
```

**analytics.ts**

```tsx
import ReactGA from "react-ga4";

ReactGA.initialize("G-XXXXXXXXXX");

export const analytics = {
  pageView(path: string) {
    ReactGA.send({
      hitType: "pageview",
      page: path,
    });
  },

  event(name: string, params?: Record<string, unknown>) {
    ReactGA.event(name, params);
  },
};
```

**RouteTracker.tsx**

```tsx
import { useEffect } from "react";
import { useLocation } from "react-router-dom";
import { analytics } from "./analytics";

export function RouteTracker() {
  const location = useLocation();

  useEffect(() => {
    analytics.pageView(location.pathname);
  }, [location]);

  return null;
}
```

**App.tsx**

```tsx
import { analytics } from "./analytics";

export default function App() {
  return (
    <button
      onClick={() =>
        analytics.event("button_clicked", {
          button: "Subscribe",
        })
      }
    >
      Subscribe
    </button>
  );
}
```

For **Segment**, the implementation is similar—the `analytics` service would delegate to `analytics.track()`, `analytics.page()`, and `analytics.identify()` instead of Google Analytics methods.

---

# Tooling & Setup

**Preferred stack:** **Vite + TypeScript** for client-rendered apps due to its fast development experience. For SSR or hybrid rendering, **Next.js App Router** is a strong choice because it provides server rendering, route-based code splitting, and built-in performance optimizations. Avoid **Create React App (CRA)**, as it is deprecated.

- **ESM vs CommonJS**: Modern React projects use **ESM** for native module support and better tree-shaking. CommonJS is mainly used in Node.js environments.
- **Bundler**: Vite uses esbuild during development and Rollup for production builds, enabling efficient code splitting and optimized bundles.
- **Configuration**: Store analytics measurement IDs or write keys in environment variables (e.g., `VITE_GA_MEASUREMENT_ID`) rather than hard-coding them.

---

# Performance

- **React Profiler**: Ensure analytics hooks are not causing unnecessary re-renders.
- **Memoization**: Use `React.memo`, `useMemo`, and `useCallback` where appropriate, though analytics calls themselves generally don't require memoization.
- **Lazy loading**: Dynamically import analytics SDKs after user consent or after the initial render to reduce initial bundle size.
- **Batching**: Where supported by the provider, batch events to reduce network overhead.
- **Sampling**: For high-traffic applications, consider event sampling for non-critical telemetry to control costs.

---

# Testing

Use **Vitest** and **React Testing Library** to verify analytics behavior by mocking the analytics service rather than the provider SDK.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { vi, expect, test } from "vitest";
import { analytics } from "./analytics";

test("tracks button click", () => {
  const spy = vi.spyOn(analytics, "event");

  analytics.event("button_clicked");

  expect(spy).toHaveBeenCalledWith("button_clicked");
});
```

For end-to-end validation, use **Playwright** and inspect outgoing analytics requests or mock the analytics endpoint.

---

# Ops & Deployment

- Add **Error Boundaries** so analytics failures never impact the user experience.
- Log analytics initialization failures and monitor them with tools like Sentry or OpenTelemetry.
- Respect privacy regulations (GDPR, CCPA, etc.) by obtaining user consent before enabling non-essential tracking where required.
- Use environment-specific measurement IDs (development, staging, production) to avoid polluting production reports.
- Keep analytics SDKs out of the critical rendering path by loading them asynchronously when possible.

---

# Pitfalls

- **Don't call analytics during rendering**; use event handlers or `useEffect` after the UI has committed.
- **Avoid duplicate events**, especially in development with React Strict Mode or by tracking the same interaction in multiple places.
- **Standardize event names and payloads** across the application to make dashboards reliable and easier to analyze.

## Question 3. How do you handle cross-tab state synchronization using localStorage or IndexedDB?

# How do you handle cross-tab state synchronization using `localStorage` or `IndexedDB`?

## Short answer

Cross-tab state synchronization allows multiple tabs of the same application to stay in sync. For lightweight state (theme, authentication status, filters), listen to the browser's **`storage` event** when using `localStorage`. For larger or structured data, use **IndexedDB** (often with libraries like Dexie) together with a notification mechanism such as **BroadcastChannel** or IndexedDB observers. Keep React state as the UI source of truth while browser storage acts as shared persistence.

---

# Explanation

When a user opens multiple tabs, each tab has its own React state:

```text
Tab A               Tab B
React State         React State
     │                  │
     └──── Shared Storage ────► localStorage / IndexedDB
```

Without synchronization:

- Login in Tab A
- Tab B still shows logged out

With synchronization:

- Login in Tab A
- Storage updates
- Tab B receives notification
- React state updates automatically

---

## Option 1: `localStorage` + `storage` event

The browser fires a **`storage`** event in **other tabs** when `localStorage` changes.

```text
Tab A
setItem()

        │

        ▼

localStorage updated

        │

        ▼

Tab B receives storage event

        │

        ▼

Update React state
```

Good for:

- Theme
- Language
- Login/logout
- Feature flags
- Small preferences

**Note:** The tab that performs `setItem()` does **not** receive its own `storage` event, so update local React state directly in that tab.

---

## Option 2: IndexedDB

Use IndexedDB when data is:

- Large
- Structured
- Offline
- Frequently updated

Examples:

- Messages
- Documents
- Cached API responses
- Offline-first applications

Common libraries:

- Dexie
- idb

---

## Option 3: BroadcastChannel (Recommended)

Modern applications often combine storage with **BroadcastChannel**.

```text
Tab A

Update state

↓

Save IndexedDB

↓

BroadcastChannel

↓

Tab B

↓

Update React state
```

Advantages:

- Immediate updates
- Works with complex objects
- Doesn't rely on storage events
- Cleaner architecture

---

## React Architecture

```text
React Components
        │
        ▼
Context / Redux / Zustand
        │
        ▼
Storage Service
   │            │
localStorage  IndexedDB
        │
        ▼
BroadcastChannel / storage event
```

Components should never manipulate storage directly.

Instead:

- Storage service updates storage.
- Storage service broadcasts change.
- State manager updates UI.

---

## React 18 considerations

React 18 features work naturally with synchronized state.

When storage changes:

```text
Storage change

↓

Event received

↓

setState()

↓

Automatic batching

↓

Single re-render
```

Use `useSyncExternalStore` when integrating an external store with React. It provides a consistent subscription model that works correctly with concurrent rendering.

---

# Example

**Scaffold a modern React app (Vite + TypeScript):**

```bash
npm create vite@latest sync-demo -- --template react-ts
cd sync-demo
npm i
npm run dev
```

**ThemeSync.tsx**

```tsx
import { useEffect, useState } from "react";

const STORAGE_KEY = "theme";

export default function ThemeSync() {
  const [theme, setTheme] = useState(
    () => localStorage.getItem(STORAGE_KEY) ?? "light",
  );

  useEffect(() => {
    function onStorage(event: StorageEvent) {
      if (event.key === STORAGE_KEY && event.newValue) {
        setTheme(event.newValue);
      }
    }

    window.addEventListener("storage", onStorage);

    return () => window.removeEventListener("storage", onStorage);
  }, []);

  function toggleTheme() {
    const next = theme === "light" ? "dark" : "light";

    setTheme(next);
    localStorage.setItem(STORAGE_KEY, next);
  }

  return <button onClick={toggleTheme}>Current theme: {theme}</button>;
}
```

If the user changes the theme in one tab, all other open tabs update automatically via the `storage` event.

---

# Tooling & Setup

**Preferred stack:** **Vite + TypeScript** because it offers fast startup, HMR, and an excellent developer experience. For SSR or hybrid rendering, **Next.js App Router** is a good option. Avoid **Create React App (CRA)** since it is deprecated.

For storage:

- **`localStorage`**: Simple key-value data.
- **IndexedDB**: Large structured datasets.
- **Dexie**: A popular wrapper around IndexedDB with a clean API.
- **BroadcastChannel**: Ideal for low-latency cross-tab messaging.

**ESM vs CommonJS**

- Vite uses **ES Modules (ESM)** for development and Rollup for production builds.
- Prefer ESM for better tree-shaking and modern tooling compatibility.

---

# Performance

- Use **React Profiler** to verify that storage updates don't trigger unnecessary component re-renders.
- Memoize derived values with `useMemo` and callbacks with `useCallback` where appropriate.
- Use `React.memo` for components receiving synchronized state that changes infrequently.
- Batch multiple storage writes when possible to reduce I/O.
- For IndexedDB, debounce or batch large write operations instead of writing on every keystroke.
- Lazy-load storage libraries (e.g., Dexie) if only a subset of the application requires offline storage.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom
```

Example approach:

- Mock `localStorage`.
- Dispatch a synthetic `StorageEvent`.
- Verify that React state updates correctly.
- For IndexedDB, use a mock implementation (e.g., `fake-indexeddb`) during tests.

Use **Playwright** for end-to-end testing by opening multiple browser contexts or pages and verifying synchronization between them.

---

# Ops & Deployment

- Keep storage access inside dedicated services or custom hooks rather than UI components.
- Use **Error Boundaries** to isolate rendering failures from storage-related issues.
- Handle quota exceeded errors and storage unavailability gracefully.
- Consider encrypting sensitive data before storing it in IndexedDB or `localStorage`; avoid storing secrets such as JWT refresh tokens in browser storage.
- Version your IndexedDB schema and implement migrations carefully to prevent data corruption after deployments.

---

# Pitfalls

- **Don't rely solely on `localStorage` for large or frequently changing data**; use IndexedDB instead.
- **Remember that the `storage` event doesn't fire in the same tab** that performs the update—update local React state directly there.
- **Avoid direct storage access from components**; centralize persistence logic in reusable services or hooks.

## Question 4. How do you implement dynamic forms driven by JSON schema?

## Question 5. How do you handle large forms efficiently using React Hook Form?

## Question 6. How do you implement multi-language support (i18n) with dynamic content?

## Question 7. How do you implement virtualization for tables with thousands of rows?

## Question 8. How do you implement drag-and-drop dashboards with widgets?

## Question 9. How do you implement A/B testing in React applications?

## Question 10. How do you monitor performance using React Profiler and Lighthouse?

## Question 11. How do you integrate Jest and React Testing Library for complex component tests?

## Question 12. How do you implement accessibility best practices with ARIA attributes?

## Question 13. How do you implement dynamic routing based on user roles?

## Question 14. How do you implement server-side data fetching with Next.js getServerSideProps?

## Question 15. How do you implement Incremental Static Regeneration (ISR) in Next.js?

## Question 16. How do you optimize bundle size using tree-shaking and dynamic imports?

## Question 17. How do you implement advanced memoization strategies for large lists?

## Question 18. How do you prevent unnecessary network requests in high-frequency API calls?

## Question 19. How do you implement multi-window or multi-tab synchronization for user sessions?

## Question 20. How do you architect a large-scale React app for maintainability and scalability?
