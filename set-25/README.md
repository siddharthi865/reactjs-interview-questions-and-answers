# Set 25

| #   | Question                                                                                                                                                                                           |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement optimistic updates for likes, votes, or comments?](#question-1-how-do-you-implement-optimistic-updates-for-likes-votes-or-comments)                                          |
| 2   | [How do you implement multi-tab state synchronization with localStorage events?](#question-2-how-do-you-implement-multi-tab-state-synchronization-with-localstorage-events)                        |
| 3   | [How do you implement dynamic component rendering based on a JSON configuration?](#question-3-how-do-you-implement-dynamic-component-rendering-based-on-a-json-configuration)                      |
| 4   | [How do you implement complex drag-and-drop dashboards?](#question-4-how-do-you-implement-complex-drag-and-drop-dashboards)                                                                        |
| 5   | [How do you implement multi-language internationalization (i18n) in React apps?](#question-5-how-do-you-implement-multi-language-internationalization-i18n-in-react-apps)                          |
| 6   | [How do you implement advanced memoization strategies to optimize performance?](#question-6-how-do-you-implement-advanced-memoization-strategies-to-optimize-performance)                          |
| 7   | [How do you implement code splitting and tree shaking for large apps?](#question-7-how-do-you-implement-code-splitting-and-tree-shaking-for-large-apps)                                            |
| 8   | [How do you implement analytics (GA, Segment) in React apps with multiple events?](#question-8-how-do-you-implement-analytics-ga-segment-in-react-apps-with-multiple-events)                       |
| 9   | [How do you implement A/B testing with React feature flags?](#question-9-how-do-you-implement-ab-testing-with-react-feature-flags)                                                                 |
| 10  | [How do you integrate GraphQL subscriptions for real-time updates?](#question-10-how-do-you-integrate-graphql-subscriptions-for-real-time-updates)                                                 |
| 11  | [How do you implement virtual scrolling for large datasets?](#question-11-how-do-you-implement-virtual-scrolling-for-large-datasets)                                                               |
| 12  | [How do you handle cross-origin API requests and CORS issues in React?](#question-12-how-do-you-handle-cross-origin-api-requests-and-cors-issues-in-react)                                         |
| 13  | [How do you integrate React with Redux Toolkit for scalable state management?](#question-13-how-do-you-integrate-react-with-redux-toolkit-for-scalable-state-management)                           |
| 14  | [How do you implement unit and integration testing for complex components?](#question-14-how-do-you-implement-unit-and-integration-testing-for-complex-components)                                 |
| 15  | [How do you implement performance monitoring with React Profiler and Lighthouse?](#question-15-how-do-you-implement-performance-monitoring-with-react-profiler-and-lighthouse)                     |
| 16  | [How do you implement accessibility (ARIA roles, keyboard navigation) in large apps?](#question-16-how-do-you-implement-accessibility-aria-roles-keyboard-navigation-in-large-apps)                |
| 17  | [How do you architect a large React app for maintainability and modularity?](#question-17-how-do-you-architect-a-large-react-app-for-maintainability-and-modularity)                               |
| 18  | [How do you implement server-side data fetching with error handling and caching?](#question-18-how-do-you-implement-server-side-data-fetching-with-error-handling-and-caching)                     |
| 19  | [How do you integrate React with micro-frontends for multiple teams?](#question-19-how-do-you-integrate-react-with-micro-frontends-for-multiple-teams)                                             |
| 20  | [How do you implement a progressive web app (PWA) with React for offline usage and caching?](#question-20-how-do-you-implement-a-progressive-web-app-pwa-with-react-for-offline-usage-and-caching) |

## Question 1. How do you implement optimistic updates for likes, votes, or comments?

# Short answer

**Optimistic updates** immediately update the UI before the server confirms the request, making the application feel faster. If the API request succeeds, the optimistic state is kept; if it fails, the UI rolls back to the previous state and optionally displays an error message.

---

# Explanation

Optimistic updates are commonly used for:

- 👍 Like/Unlike buttons
- ⬆️ Upvotes/Downvotes
- 💬 Adding comments
- ⭐ Favorites/Bookmarks
- ✅ Todo completion

Instead of waiting for:

```
User Action
    ↓
API Request
    ↓
Server Response
    ↓
UI Update
```

React applications usually do:

```
User Action
    ↓
Update UI Immediately
    ↓
Send API Request
    ↓
Success → Keep state
Failure → Rollback
```

This greatly improves perceived performance.

## Basic flow

1. Save the previous state.
2. Apply the optimistic update immediately.
3. Send the request.
4. If successful, optionally sync with server response.
5. If failed, restore the previous state.

Example timeline:

```
Likes: 25

User clicks ❤️

Immediately:
Likes: 26

API...

Success:
Likes: 26 ✓

OR

Failure:
Likes: 25
Show error
```

---

## React 18 considerations

React 18's **automatic batching** reduces unnecessary renders when multiple state updates occur in async callbacks, improving optimistic update performance.

For complex applications:

- Keep server state separate from UI state.
- Use immutable updates.
- Avoid race conditions from rapid repeated clicks.
- Disable the action while a request is pending, or reconcile multiple in-flight requests.

For large applications, libraries such as **TanStack Query**, **RTK Query**, or **Apollo Client** provide built-in optimistic update support with rollback mechanisms.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest optimistic-demo -- --template react-ts
cd optimistic-demo
npm install
npm run dev
```

`LikeButton.tsx`

```tsx
import { useState } from "react";

async function likePost() {
  // Simulate API
  await new Promise((resolve) => setTimeout(resolve, 1000));

  if (Math.random() < 0.3) {
    throw new Error("Request failed");
  }
}

export default function LikeButton() {
  const [likes, setLikes] = useState(10);
  const [loading, setLoading] = useState(false);

  const handleLike = async () => {
    if (loading) return;

    const previousLikes = likes;

    setLoading(true);

    // Optimistic update
    setLikes((count) => count + 1);

    try {
      await likePost();
    } catch {
      // Rollback
      setLikes(previousLikes);
      alert("Failed to like post.");
    } finally {
      setLoading(false);
    }
  };

  return (
    <button onClick={handleLike} disabled={loading}>
      ❤️ {likes}
    </button>
  );
}
```

If the simulated API succeeds:

```
10 → 11
```

If it fails:

```
10 → 11 → 10
```

---

# Tooling & Setup

Prefer **Vite** over Create React App (CRA), as CRA is deprecated.

Recommended stacks:

- **Vite** for SPAs with a fast ESM-based development experience.
- **Next.js (App Router)** for SSR, Server Components, and full-stack React.
- **Remix** for data-driven routing and progressive enhancement.

Notes:

- **ESM** (`import`/`export`) is the standard module format used by modern React tooling.
- Vite uses native ESM during development and bundles efficiently for production with Rollup.
- For server-rendered applications, Next.js integrates routing, code splitting, and data fetching.

---

# Performance

Optimistic updates already improve **perceived performance**, but consider these additional practices:

- Use **React Profiler** to identify unnecessary re-renders.
- Use **React.memo** for frequently rendered child components.
- Use **useCallback** for stable event handlers passed to memoized children.
- Use **useMemo** for expensive derived values.
- Lazy load non-critical routes with `React.lazy()` and `Suspense`.
- Cache and synchronize server state with libraries like **TanStack Query** or **RTK Query** to reduce redundant network requests.

For server state, prefer dedicated data-fetching libraries instead of duplicating cache logic in component state.

---

# Testing

Test both the optimistic update and rollback behavior.

Using **Vitest** and **React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event
```

Typical test scenarios:

- Clicking updates the UI immediately.
- Successful API call keeps the new value.
- Failed API call restores the previous value.
- Error message is displayed on failure.

For end-to-end verification, use **Playwright** to simulate real user interactions and network failures.

---

# Ops & Deployment

- Log API failures for monitoring and debugging.
- Use **Error Boundaries** for rendering errors (they do not catch async request failures).
- In SSR applications, reconcile optimistic client state with fresh server data after hydration.
- Split bundles by route and lazily load feature modules to reduce initial load time.
- Deploy static assets via a CDN and leverage HTTP caching where appropriate.
- Ensure backend APIs are idempotent or handle duplicate requests safely if users retry actions.

---

# Pitfalls

- **Not rolling back on failure**, leaving the UI inconsistent with the server.
- **Ignoring concurrent requests**, causing stale responses to overwrite newer optimistic state.
- **Mutating state directly** instead of using immutable updates, which can prevent React from detecting changes.

## Question 2. How do you implement multi-tab state synchronization with localStorage events?

# Short answer

**Multi-tab state synchronization** is implemented by listening to the browser's **`storage`** event. When one tab updates `localStorage`, all **other open tabs** receive the event and can update their React state accordingly. Note that the tab making the change **does not receive** the `storage` event, so it must update its own state directly.

---

# Explanation

Imagine a user has your application open in two tabs.

Without synchronization:

```
Tab A                  Tab B

Theme = Light          Theme = Light

↓ User changes theme

Theme = Dark           Theme = Light ❌
```

With `storage` events:

```
Tab A                  Tab B

Theme = Light          Theme = Light

↓ User changes theme

Save to localStorage
Update own state

storage event
---------------------------->

Theme = Dark           Theme = Dark ✅
```

## How it works

1. User updates state.
2. Update React state immediately.
3. Persist the value to `localStorage`.
4. Other tabs receive the `storage` event.
5. Parse the new value and update their state.

React only manages state within a single browser tab. The browser provides the `storage` event to synchronize changes across tabs.

### Key characteristics

- Fires only in **other tabs/windows** of the same origin.
- Does **not** fire in the tab that called `localStorage.setItem()`.
- Includes:
  - `key`
  - `oldValue`
  - `newValue`
  - `storageArea`
  - `url`

---

## React 18 considerations

With React 18:

- Automatic batching minimizes unnecessary renders when multiple state updates occur together.
- Register the event listener inside `useEffect` and clean it up on unmount.
- Prefer immutable updates so React can efficiently detect changes.
- For shared application state, encapsulate the synchronization logic in a custom hook or external store.

For larger applications, `useSyncExternalStore` is a better fit for subscribing to browser-backed external state because it provides a consistent subscription API and avoids tearing during concurrent rendering.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest multi-tab-demo -- --template react-ts
cd multi-tab-demo
npm install
npm run dev
```

`ThemeSwitcher.tsx`

```tsx
import { useEffect, useState } from "react";

const STORAGE_KEY = "theme";

type Theme = "light" | "dark";

export default function ThemeSwitcher() {
  const [theme, setTheme] = useState<Theme>(() => {
    return (localStorage.getItem(STORAGE_KEY) as Theme) ?? "light";
  });

  useEffect(() => {
    const handleStorage = (event: StorageEvent) => {
      if (event.key === STORAGE_KEY && event.newValue) {
        setTheme(event.newValue as Theme);
      }
    };

    window.addEventListener("storage", handleStorage);

    return () => {
      window.removeEventListener("storage", handleStorage);
    };
  }, []);

  const toggleTheme = () => {
    const nextTheme: Theme = theme === "light" ? "dark" : "light";

    // Update current tab
    setTheme(nextTheme);

    // Notify other tabs
    localStorage.setItem(STORAGE_KEY, nextTheme);
  };

  return (
    <div>
      <h2>Current theme: {theme}</h2>

      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}
```

If two tabs are open:

```
Tab A clicks Toggle

↓

setTheme("dark")
localStorage.setItem()

↓

Tab B receives storage event

↓

setTheme("dark")
```

Both tabs stay synchronized without polling.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Recommended options:

- **Vite** for client-side React applications with fast startup, native ESM support during development, and optimized production builds.
- **Next.js (App Router)** when you need SSR, Server Components, or full-stack capabilities. Access browser APIs like `localStorage` only in Client Components.
- **Remix** for data-centric routing and progressive enhancement.

Module notes:

- Use **ES Modules (ESM)** (`import`/`export`), which are the standard in modern React tooling.
- Vite serves ESM directly in development and bundles with Rollup for production.

---

# Performance

For production applications:

- Use the **React Profiler** to verify that only components depending on synchronized state re-render.
- Wrap expensive child components with `React.memo`.
- Use `useCallback` when passing handlers to memoized children.
- Use `useMemo` for expensive derived values.
- Code-split routes and large features using `React.lazy()` and `Suspense`.
- If many components consume the synchronized state, expose it through a custom hook built on `useSyncExternalStore` instead of attaching multiple `storage` listeners.

For high-frequency cross-tab communication (e.g., collaborative editing), consider the **BroadcastChannel API**, which is designed for messaging between tabs and is often more suitable than repeatedly writing to `localStorage`.

---

# Testing

Use **Vitest** with **React Testing Library** for component tests.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event
```

Test scenarios:

- Initial state is loaded from `localStorage`.
- Clicking updates the current tab immediately.
- A simulated `storage` event updates the component.
- Event listeners are cleaned up on unmount.
- Invalid or missing values are handled gracefully.

Use **Playwright** to verify synchronization between two browser tabs in an end-to-end test.

---

# Ops & Deployment

- Keep synchronized data small and serializable (typically JSON).
- Always wrap `JSON.parse` in `try/catch` when storing structured data.
- Don't store sensitive information such as authentication tokens in `localStorage`; prefer secure cookies for authentication.
- Error Boundaries do not catch errors thrown in `storage` event handlers—handle parsing and validation explicitly.
- Remember that `localStorage` is unavailable during server-side rendering, so guard access with browser checks or initialize inside effects when using SSR frameworks.

---

# Pitfalls

- **Expecting the `storage` event to fire in the same tab** that called `localStorage.setItem()`—it only fires in other tabs.
- **Forgetting to remove the event listener**, causing memory leaks or duplicate updates.
- **Not validating or safely parsing stored values**, which can crash the application if the stored data is malformed.

## Question 3. How do you implement dynamic component rendering based on a JSON configuration?

# Short answer

**Dynamic component rendering** maps values from a JSON configuration (such as a `type` field) to React components. Instead of hardcoding JSX, React looks up the appropriate component from a registry and renders it with props from the JSON. This pattern is commonly used in **form builders, dashboards, CMS-driven UIs, page builders, and feature-flagged interfaces**.

---

# Explanation

Instead of writing:

```tsx
return (
  <>
    <Button />
    <Input />
    <Card />
  </>
);
```

you can describe the UI with JSON:

```json
[
  {
    "type": "heading",
    "props": {
      "text": "Welcome"
    }
  },
  {
    "type": "button",
    "props": {
      "label": "Login"
    }
  }
]
```

React reads the configuration and renders components dynamically.

## Architecture

```
JSON Configuration
        │
        ▼
Component Registry
(type → Component)
        │
        ▼
React.createElement / JSX
        │
        ▼
Rendered UI
```

A typical component registry:

```tsx
const componentMap = {
  heading: Heading,
  button: Button,
  card: Card,
};
```

Rendering:

```tsx
const Component = componentMap[item.type];

return Component ? <Component {...item.props} /> : null;
```

This keeps rendering logic generic and makes it easy to add new component types without modifying the renderer.

---

## React 18 considerations

For production applications:

- Keep the JSON **declarative** (describe _what_ to render, not executable logic).
- Validate JSON before rendering (using libraries like Zod or JSON Schema).
- Memoize expensive derived configurations with `useMemo`.
- React 18's automatic batching minimizes unnecessary renders when multiple state updates occur together.
- For very large configurations (hundreds of widgets), combine dynamic rendering with virtualization and lazy loading.

Avoid storing React components directly in JSON. Store identifiers and resolve them through a registry.

---

# Example

**Vite + React + TypeScript**

Create the project:

```bash
npm create vite@latest dynamic-render-demo -- --template react-ts
cd dynamic-render-demo
npm install
npm run dev
```

`App.tsx`

```tsx
import React from "react";

type ConfigItem =
  | {
      id: number;
      type: "heading";
      props: { text: string };
    }
  | {
      id: number;
      type: "button";
      props: { label: string };
    };

const Heading = ({ text }: { text: string }) => <h2>{text}</h2>;

const Button = ({ label }: { label: string }) => <button>{label}</button>;

const componentMap = {
  heading: Heading,
  button: Button,
};

const config: ConfigItem[] = [
  {
    id: 1,
    type: "heading",
    props: { text: "Dynamic UI" },
  },
  {
    id: 2,
    type: "button",
    props: { label: "Submit" },
  },
];

export default function App() {
  return (
    <div>
      {config.map((item) => {
        const Component = componentMap[item.type];

        return <Component key={item.id} {...item.props} />;
      })}
    </div>
  );
}
```

Output:

```
Dynamic UI

[ Submit ]
```

To add another component, simply:

1. Create the component.
2. Register it in `componentMap`.
3. Add a JSON entry.

No changes to the renderer are required.

---

# Tooling & Setup

Avoid **Create React App (CRA)** because it is deprecated.

Preferred tooling:

- **Vite** for SPAs with fast HMR and native ESM support.
- **Next.js (App Router)** when server rendering or React Server Components are required. JSON can be fetched on the server and rendered by Client Components when interactivity is needed.
- **Remix** for route-centric data loading.

Module notes:

- Use **ES Modules (ESM)** (`import`/`export`) as the modern JavaScript standard.
- Vite uses native ESM during development and Rollup for optimized production bundles.

---

# Performance

When rendering many dynamic components:

- Profile rendering with the **React Profiler**.
- Wrap frequently reused components in `React.memo` to avoid unnecessary re-renders.
- Memoize derived configuration with `useMemo`.
- Memoize callbacks passed to dynamic children with `useCallback`.
- Use `React.lazy()` and `Suspense` to lazily load rarely used component types.
- Virtualize long lists (e.g., with `react-window`) if the JSON describes hundreds or thousands of items.
- Cache fetched JSON configurations with libraries such as TanStack Query or RTK Query to avoid redundant network requests.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event
```

Test scenarios:

- Correct component is rendered for each `type`.
- Unknown component types render a fallback UI or nothing.
- Props from JSON are passed correctly.
- Invalid JSON is handled gracefully.

For end-to-end testing of CMS-driven pages, use **Playwright**.

---

# Ops & Deployment

- Validate JSON from external sources before rendering to prevent runtime errors.
- Log unsupported component types for observability.
- Use an **Error Boundary** around the dynamic renderer to isolate rendering failures.
- Split large widget libraries into separate bundles with dynamic imports.
- Serve configuration files through a CDN with appropriate cache headers if they are shared across users.
- Avoid embedding executable code in JSON; keep it as pure data.

---

# Pitfalls

- **Using user-provided component names directly** without validation, which can lead to rendering errors or security concerns.
- **Not providing a fallback** for unknown component types.
- **Embedding business logic in JSON**, making configurations difficult to validate, test, and maintain.

## Question 4. How do you implement complex drag-and-drop dashboards?

## Question 5. How do you implement multi-language internationalization (i18n) in React apps?

## Question 6. How do you implement advanced memoization strategies to optimize performance?

## Question 7. How do you implement code splitting and tree shaking for large apps?

## Question 8. How do you implement analytics (GA, Segment) in React apps with multiple events?

## Question 9. How do you implement A/B testing with React feature flags?

## Question 10. How do you integrate GraphQL subscriptions for real-time updates?

## Question 11. How do you implement virtual scrolling for large datasets?

## Question 12. How do you handle cross-origin API requests and CORS issues in React?

## Question 13. How do you integrate React with Redux Toolkit for scalable state management?

## Question 14. How do you implement unit and integration testing for complex components?

## Question 15. How do you implement performance monitoring with React Profiler and Lighthouse?

## Question 16. How do you implement accessibility (ARIA roles, keyboard navigation) in large apps?

## Question 17. How do you architect a large React app for maintainability and modularity?

## Question 18. How do you implement server-side data fetching with error handling and caching?

## Question 19. How do you integrate React with micro-frontends for multiple teams?

## Question 20. How do you implement a progressive web app (PWA) with React for offline usage and caching?
