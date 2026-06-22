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

## Question 3. How do you implement dynamic component rendering based on a JSON configuration?

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
