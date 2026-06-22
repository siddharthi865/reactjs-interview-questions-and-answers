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

## Question 3. How do you handle cross-tab state synchronization using localStorage or IndexedDB?

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
