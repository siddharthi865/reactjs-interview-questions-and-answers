# Set 24

| #   | Question                                                                                                                                                               |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you implement scroll-to-top buttons dynamically?](#question-1-how-do-you-implement-scroll-to-top-buttons-dynamically)                                          |
| 2   | [How do you implement a global error handling system with context?](#question-2-how-do-you-implement-a-global-error-handling-system-with-context)                      |
| 3   | [How do you implement a multi-select dropdown with search functionality?](#question-3-how-do-you-implement-a-multi-select-dropdown-with-search-functionality)          |
| 4   | [How do you implement a sortable table with dynamic columns?](#question-4-how-do-you-implement-a-sortable-table-with-dynamic-columns)                                  |
| 5   | [How do you implement infinite scrolling with API pagination?](#question-5-how-do-you-implement-infinite-scrolling-with-api-pagination)                                |
| 6   | [How do you implement sticky footers in React layouts?](#question-6-how-do-you-implement-sticky-footers-in-react-layouts)                                              |
| 7   | [How do you implement collapsible sections in content pages?](#question-7-how-do-you-implement-collapsible-sections-in-content-pages)                                  |
| 8   | [How do you implement a carousel/slider with autoplay and navigation buttons?](#question-8-how-do-you-implement-a-carouselslider-with-autoplay-and-navigation-buttons) |
| 9   | [How do you implement a rating/review component with stars and comments?](#question-9-how-do-you-implement-a-ratingreview-component-with-stars-and-comments)           |
| 10  | [How do you implement a dynamic breadcrumb navigation component?](#question-10-how-do-you-implement-a-dynamic-breadcrumb-navigation-component)                         |
| 11  | [How do you implement Suspense for data fetching with Concurrent Mode?](#question-11-how-do-you-implement-suspense-for-data-fetching-with-concurrent-mode)             |
| 12  | [How do you implement lazy-loading routes with React Router v6?](#question-12-how-do-you-implement-lazy-loading-routes-with-react-router-v6)                           |
| 13  | [How do you implement SSR with Next.js and dynamic data?](#question-13-how-do-you-implement-ssr-with-nextjs-and-dynamic-data)                                          |
| 14  | [How do you implement Incremental Static Regeneration (ISR) in Next.js?](#question-14-how-do-you-implement-incremental-static-regeneration-isr-in-nextjs)              |
| 15  | [How do you implement authentication using OAuth 2.0 in React?](#question-15-how-do-you-implement-authentication-using-oauth-20-in-react)                              |
| 16  | [How do you implement JWT token refresh in a React app?](#question-16-how-do-you-implement-jwt-token-refresh-in-a-react-app)                                           |
| 17  | [How do you implement role-based access control (RBAC) in React Router?](#question-17-how-do-you-implement-role-based-access-control-rbac-in-react-router)             |
| 18  | [How do you implement WebSockets for real-time chat in React?](#question-18-how-do-you-implement-websockets-for-real-time-chat-in-react)                               |
| 19  | [How do you implement offline-first React apps with service workers?](#question-19-how-do-you-implement-offline-first-react-apps-with-service-workers)                 |
| 20  | [How do you implement React Query for caching, fetching, and synchronization?](#question-20-how-do-you-implement-react-query-for-caching-fetching-and-synchronization) |

## Question 1. How do you implement scroll-to-top buttons dynamically?

# How do you implement scroll-to-top buttons dynamically?

## Short answer

A dynamic **scroll-to-top button** appears only after the user scrolls beyond a certain threshold (e.g., 300px). In React, you typically:

- Listen to the window's scroll position.
- Store visibility in state.
- Show/hide the button conditionally.
- Smoothly scroll to the top using `window.scrollTo({ top: 0, behavior: "smooth" })`.
- Clean up the event listener in `useEffect`.

---

# Explanation

A scroll-to-top button improves UX on long pages by allowing users to quickly return to the top.

A production-ready implementation should consider:

- Showing the button only after a threshold.
- Avoiding excessive re-renders from scroll events.
- Cleaning up listeners.
- Accessibility.
- Mobile support.
- Respecting reduced motion preferences.

### Basic Flow

```
Page loads
      ↓
User scrolls
      ↓
Scroll > threshold?
      ↓
Yes -----------------> Show button
No ------------------> Hide button
      ↓
User clicks
      ↓
window.scrollTo({
   top: 0,
   behavior: "smooth"
})
```

---

## React 18 Considerations

React 18 automatically batches state updates.

However, **scroll events fire very frequently (60–120 times/sec)**.

Avoid updating state on every event.

Better approaches:

- only update when visibility changes
- throttle
- debounce
- requestAnimationFrame

Example:

```ts
if (window.scrollY > 300 !== visible) {
  setVisible(window.scrollY > 300);
}
```

This avoids unnecessary renders.

---

## Example (React + TypeScript + Vite)

### Create project

```bash
npm create vite@latest my-app -- --template react-ts

cd my-app

npm install

npm run dev
```

### ScrollToTopButton.tsx

```tsx
import { useEffect, useState } from "react";

export default function ScrollToTopButton() {
  const [visible, setVisible] = useState(false);

  useEffect(() => {
    const handleScroll = () => {
      const shouldShow = window.scrollY > 300;
      setVisible((prev) => (prev !== shouldShow ? shouldShow : prev));
    };

    window.addEventListener("scroll", handleScroll, { passive: true });

    handleScroll();

    return () => {
      window.removeEventListener("scroll", handleScroll);
    };
  }, []);

  const scrollToTop = () => {
    window.scrollTo({
      top: 0,
      behavior: "smooth",
    });
  };

  if (!visible) return null;

  return (
    <button
      onClick={scrollToTop}
      aria-label="Scroll to top"
      style={{
        position: "fixed",
        right: 20,
        bottom: 20,
        padding: "12px 16px",
        borderRadius: "50%",
        cursor: "pointer",
      }}
    >
      ↑
    </button>
  );
}
```

### App.tsx

```tsx
import ScrollToTopButton from "./ScrollToTopButton";

export default function App() {
  return (
    <>
      <div style={{ height: "3000px", padding: 20 }}>
        <h1>Long Page</h1>
        <p>Scroll down to see the button.</p>
      </div>

      <ScrollToTopButton />
    </>
  );
}
```

---

# Tooling & Setup

### Preferred Stack

- **Vite + React + TypeScript**
  - Fast dev server (ES modules).
  - Lightning-fast HMR.
  - Excellent TypeScript support.

Avoid **Create React App (CRA)** since it is deprecated.

Other good choices:

- **Next.js** (SSR, SSG, App Router)
- **Remix** (nested routing and data loading)
- **Turbopack** (Next.js development bundler)

### ESM vs CommonJS

- Vite uses **ES Modules (ESM)** natively in development for faster startup.
- CommonJS is primarily used in older Node.js ecosystems; modern React projects should prefer ESM.

---

# Performance

For long pages, optimize scroll handling:

### 1. Passive event listeners

```ts
window.addEventListener("scroll", handleScroll, {
  passive: true,
});
```

Allows the browser to optimize scrolling.

---

### 2. Throttle scroll events

```ts
let ticking = false;

const handleScroll = () => {
  if (!ticking) {
    requestAnimationFrame(() => {
      setVisible(window.scrollY > 300);
      ticking = false;
    });
    ticking = true;
  }
};
```

---

### 3. Avoid unnecessary state updates

```ts
setVisible((prev) => (prev !== shouldShow ? shouldShow : prev));
```

---

### 4. Memoization

- `React.memo` if the button receives props.
- `useCallback` for event handlers passed to child components.
- `useMemo` only if expensive calculations are involved (not usually necessary here).

---

### 5. Code splitting

If the button is part of a large UI library:

```tsx
const ScrollButton = React.lazy(() => import("./ScrollButton"));
```

---

### 6. React Profiler

Use React DevTools Profiler to ensure:

- Scroll events don't trigger unnecessary renders.
- Only the button re-renders when its visibility changes.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import ScrollToTopButton from "./ScrollToTopButton";

test("button is hidden initially", () => {
  render(<ScrollToTopButton />);
  expect(screen.queryByRole("button")).toBeNull();
});
```

For end-to-end behavior (scrolling and clicking), tools like Playwright are well-suited.

---

# Ops & Deployment

- Log client-side errors with services such as Sentry.
- Wrap the application in an Error Boundary to isolate rendering failures (note that Error Boundaries do not catch errors in event handlers).
- In SSR frameworks like Next.js, ensure browser-only APIs (`window`, `document`) are accessed inside effects or client components.
- Keep the button lightweight to avoid impacting bundle size.
- Deploy static assets via a CDN and use cache headers for optimal performance.

---

# Pitfalls

- **Forgetting to remove the scroll event listener**, causing memory leaks.
- **Updating state on every scroll event**, leading to excessive re-renders.
- **Ignoring accessibility**, such as missing an `aria-label` or not respecting users who prefer reduced motion.

## Question 2. How do you implement a global error handling system with context?

# How do you implement a global error handling system with Context?

## Short answer

A **global error handling system** in React is typically implemented using the **Context API** to expose error state and helper functions (e.g., `showError`, `clearError`) throughout the application. Combine it with an **Error Boundary** for render-time errors and centralized API/network error handling (e.g., Axios interceptors or `fetch` wrappers) to provide consistent user feedback and logging.

---

# Explanation

A production-ready global error handling system has multiple layers because different types of errors originate from different places:

| Error Type                 | Best Handling Mechanism                      |
| -------------------------- | -------------------------------------------- |
| Component rendering errors | Error Boundary                               |
| Event handler errors       | try/catch + Context                          |
| API errors                 | Axios interceptors / fetch wrapper + Context |
| Async errors               | try/catch + Context                          |
| Global JS errors           | `window.onerror`, `unhandledrejection`       |
| Server errors              | Backend logging + monitoring                 |

A common architecture looks like this:

```text
             API Request
                  │
                  ▼
        Axios/Fetch Wrapper
                  │
          Error Interceptor
                  │
                  ▼
        ErrorContext.showError()
                  │
                  ▼
         Global Error State
                  │
      ┌───────────┴───────────┐
      ▼                       ▼
 Error Toast             Error Modal
      │
      ▼
 User dismisses → clearError()

Render Errors
      │
      ▼
 Error Boundary
      │
      ▼
 ErrorContext / Logging Service
```

### Why Context?

Instead of each component maintaining its own error state:

```tsx
const [error, setError] = useState(null);
```

you centralize error management:

- One source of truth
- Consistent UI
- Easier logging
- Cleaner components
- Easier testing

---

# Example (React + TypeScript + Vite)

### Create project

```bash
npm create vite@latest my-app -- --template react-ts

cd my-app

npm install

npm run dev
```

### `ErrorContext.tsx`

```tsx
import { createContext, useContext, useState, ReactNode } from "react";

type ErrorContextType = {
  error: string | null;
  showError: (message: string) => void;
  clearError: () => void;
};

const ErrorContext = createContext<ErrorContextType | undefined>(undefined);

export function ErrorProvider({ children }: { children: ReactNode }) {
  const [error, setError] = useState<string | null>(null);

  const showError = (message: string) => {
    setError(message);
  };

  const clearError = () => {
    setError(null);
  };

  return (
    <ErrorContext.Provider
      value={{
        error,
        showError,
        clearError,
      }}
    >
      {children}

      {error && (
        <div
          style={{
            position: "fixed",
            top: 20,
            right: 20,
            background: "crimson",
            color: "white",
            padding: 12,
          }}
        >
          {error}
          <button onClick={clearError} style={{ marginLeft: 10 }}>
            ×
          </button>
        </div>
      )}
    </ErrorContext.Provider>
  );
}

export function useError() {
  const context = useContext(ErrorContext);

  if (!context) {
    throw new Error("useError must be used inside ErrorProvider");
  }

  return context;
}
```

### Using it in a component

```tsx
import { useError } from "./ErrorContext";

export default function UserList() {
  const { showError } = useError();

  async function loadUsers() {
    try {
      throw new Error("Failed to fetch users");
    } catch (err) {
      showError((err as Error).message);
    }
  }

  return <button onClick={loadUsers}>Load Users</button>;
}
```

### `main.tsx`

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { ErrorProvider } from "./ErrorContext";

ReactDOM.createRoot(document.getElementById("root")!).render(
  <React.StrictMode>
    <ErrorProvider>
      <App />
    </ErrorProvider>
  </React.StrictMode>,
);
```

---

# Tooling & Setup

**Recommended stack:**

- **Vite + React + TypeScript** for fast development with native ESM, excellent HMR, and minimal configuration.
- Avoid **Create React App (CRA)** because it is deprecated.
- For SSR or hybrid rendering, use **Next.js App Router**. Place the `ErrorProvider` in a client component (e.g., the root layout) and use Next.js `error.tsx` files for route-level error boundaries.
- **Remix** is another good option when leveraging nested routing and data APIs.

**ESM vs CommonJS**

- Vite uses **ES Modules (ESM)** in development, resulting in faster startup and on-demand module loading.
- CommonJS remains common in older Node.js projects, but modern React applications should favor ESM.

---

# Performance

A global error system should not become a source of unnecessary re-renders.

### 1. Memoize context value

```tsx
const value = useMemo(
  () => ({
    error,
    showError,
    clearError,
  }),
  [error],
);
```

This prevents a new context object from being created on every render.

### 2. Memoize callbacks

```tsx
const showError = useCallback((message: string) => {
  setError(message);
}, []);

const clearError = useCallback(() => {
  setError(null);
}, []);
```

### 3. Automatic batching

React 18 automatically batches state updates, reducing unnecessary renders when multiple updates occur in the same event or async task.

### 4. React Profiler

Use the React DevTools Profiler to verify that only components consuming the error context re-render when the error state changes.

### 5. Code splitting

Lazy-load heavy error UI (e.g., detailed error dialogs or reporting forms) with `React.lazy` and `Suspense` to keep the initial bundle small.

### 6. Caching and retries

If using libraries like **TanStack Query**, configure retry logic and use global query/mutation error handlers to surface failures through the context instead of duplicating error handling in every component.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import { ErrorProvider } from "./ErrorContext";
import UserList from "./UserList";

test("shows global error message", async () => {
  render(
    <ErrorProvider>
      <UserList />
    </ErrorProvider>,
  );

  fireEvent.click(screen.getByText("Load Users"));

  expect(await screen.findByText("Failed to fetch users")).toBeInTheDocument();
});
```

For integration tests, mock API failures with **MSW (Mock Service Worker)**. For end-to-end verification of error flows, use **Playwright**.

---

# Ops & Deployment

- **Error Boundaries:** Wrap major UI sections in Error Boundaries to catch rendering errors. They do not catch errors in event handlers, async code, or server-side rendering.
- **Centralized logging:** Forward errors to services such as Sentry, Datadog, or New Relic with metadata like user ID (if appropriate), route, browser, and stack trace.
- **SSR/CSR considerations:** In frameworks like Next.js, access browser APIs (`window`, `document`) only in client components or effects. Use route-level error boundaries (`error.tsx`) for server-rendered routes.
- **Bundle size:** Keep the context lightweight. Dynamically import advanced reporting or diagnostics UI if needed.
- **Deployment:** Serve static assets through a CDN and generate source maps (uploaded securely to your monitoring platform) to improve production debugging.

---

# Pitfalls

- **Using Context as the only error mechanism.** Context does not catch render errors—combine it with Error Boundaries.
- **Storing every transient error globally.** Reserve global state for application-wide notifications; keep local validation errors within their components.
- **Triggering unnecessary re-renders.** Memoize context values and callbacks, and avoid recreating provider values on every render.

## Question 3. How do you implement a multi-select dropdown with search functionality?

# How do you implement a multi-select dropdown with search functionality?

## Short answer

A **multi-select dropdown with search** allows users to filter a list of options, select multiple items, and manage the selected values. In React, implement it using:

- `useState` for search text and selected items.
- Filtered options derived from the search query.
- Checkboxes (or clickable list items) for multi-selection.
- A reusable, controlled component (`value` + `onChange`).
- Memoization (`useMemo`) for efficient filtering of large datasets.

---

# Explanation

A production-ready multi-select component should support:

- ✅ Multiple selection
- ✅ Search/filtering
- ✅ Controlled component API
- ✅ Keyboard accessibility
- ✅ Click outside to close
- ✅ Clear all selections
- ✅ Custom option rendering
- ✅ Async data support (optional)
- ✅ Virtualization for large datasets

### Component Architecture

```text
Parent Component
      │
      ▼
 MultiSelect
      │
 ┌────┴──────────────┐
 ▼                   ▼
Search Input     Selected Tags
      │
      ▼
 Filter Options (useMemo)
      │
      ▼
 Option List (Checkboxes)
      │
      ▼
 onChange(selectedItems)
```

### Why use a controlled component?

Instead of managing selected values internally:

```tsx
const [selected, setSelected] = useState<string[]>([]);
```

Expose them via props:

```tsx
<MultiSelect value={selected} onChange={setSelected} />
```

This keeps the component reusable and makes it easier to integrate with forms, state libraries, and validation.

### React 18 Considerations

- **Automatic batching** reduces re-renders when updating multiple pieces of state.
- Use **`useMemo`** to avoid filtering on every render.
- Use **`useCallback`** if handlers are passed to memoized child components.
- For very large option lists, consider **`useDeferredValue`** to keep typing responsive.

---

# Example (React + TypeScript + Vite)

### Create project

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `MultiSelect.tsx`

```tsx
import { useMemo, useState } from "react";

type Option = {
  id: number;
  label: string;
};

type Props = {
  options: Option[];
  value: Option[];
  onChange: (value: Option[]) => void;
};

export default function MultiSelect({ options, value, onChange }: Props) {
  const [search, setSearch] = useState("");

  const filteredOptions = useMemo(() => {
    return options.filter((option) =>
      option.label.toLowerCase().includes(search.toLowerCase()),
    );
  }, [options, search]);

  const toggleOption = (option: Option) => {
    const exists = value.some((item) => item.id === option.id);

    if (exists) {
      onChange(value.filter((item) => item.id !== option.id));
    } else {
      onChange([...value, option]);
    }
  };

  return (
    <div style={{ width: 300 }}>
      <input
        type="text"
        placeholder="Search..."
        value={search}
        onChange={(e) => setSearch(e.target.value)}
      />

      <div style={{ margin: "10px 0" }}>
        {value.map((item) => (
          <span
            key={item.id}
            style={{
              marginRight: 6,
              padding: "4px 8px",
              border: "1px solid gray",
              borderRadius: 12,
            }}
          >
            {item.label}
          </span>
        ))}
      </div>

      <ul style={{ maxHeight: 180, overflowY: "auto" }}>
        {filteredOptions.map((option) => (
          <li key={option.id}>
            <label>
              <input
                type="checkbox"
                checked={value.some((v) => v.id === option.id)}
                onChange={() => toggleOption(option)}
              />
              {option.label}
            </label>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### `App.tsx`

```tsx
import { useState } from "react";
import MultiSelect from "./MultiSelect";

const options = [
  { id: 1, label: "React" },
  { id: 2, label: "Angular" },
  { id: 3, label: "Vue" },
  { id: 4, label: "Svelte" },
];

export default function App() {
  const [selected, setSelected] = useState<typeof options>([]);

  return (
    <MultiSelect options={options} value={selected} onChange={setSelected} />
  );
}
```

---

# Tooling & Setup

**Recommended stack**

- **Vite + React + TypeScript** for fast HMR, native ESM support, and a lightweight development experience.
- Avoid **Create React App (CRA)** because it is deprecated.
- Use **Next.js** if SSR, Server Components, or SEO are required.
- Use **Remix** when leveraging nested routing and data loading.

**ESM vs CommonJS**

- Vite uses **ES Modules (ESM)** in development, enabling faster startup and on-demand loading.
- CommonJS is primarily used in older Node.js projects; modern React applications should favor ESM.

---

# Performance

### 1. Memoize filtering

```tsx
const filteredOptions = useMemo(
  () =>
    options.filter((option) =>
      option.label.toLowerCase().includes(search.toLowerCase()),
    ),
  [options, search],
);
```

### 2. Memoize callbacks

```tsx
const toggleOption = useCallback(
  (option: Option) => {
    // update selection
  },
  [value, onChange],
);
```

Useful when passing handlers to memoized children.

### 3. React.memo

Wrap expensive option row components:

```tsx
const OptionRow = React.memo(OptionRowComponent);
```

### 4. Virtualize large lists

For thousands of options, use libraries like `react-window` or `@tanstack/react-virtual` to render only visible rows.

### 5. Concurrent rendering

For expensive filtering, use `useDeferredValue`:

```tsx
const deferredSearch = useDeferredValue(search);
```

This keeps typing smooth while filtering large datasets.

### 6. Code splitting

Lazy-load the dropdown if it is only used on specific routes:

```tsx
const MultiSelect = React.lazy(() => import("./MultiSelect"));
```

### 7. React Profiler

Use the React DevTools Profiler to verify that typing in the search input does not trigger unnecessary re-renders across the application.

---

# Testing

Use **Vitest + React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("filters options", () => {
  render(<App />);

  fireEvent.change(screen.getByPlaceholderText("Search..."), {
    target: { value: "React" },
  });

  expect(screen.getByText("React")).toBeInTheDocument();
});
```

For integration tests, verify selection state updates correctly. Use **Playwright** for end-to-end testing of keyboard navigation, dropdown opening/closing, and search behavior.

---

# Ops & Deployment

- **Logging:** Capture unexpected client-side errors with services like Sentry or Datadog.
- **Error Boundaries:** Wrap larger feature areas to catch rendering errors (event handler errors still require `try/catch`).
- **SSR/CSR:** In Next.js, ensure browser-specific APIs are used only in client components or effects. Consider server-side fetching for option data when beneficial.
- **Bundle size:** Avoid shipping large UI libraries if only a simple dropdown is needed. Prefer tree-shakeable dependencies and lazy-load advanced features.
- **Accessibility:** Implement ARIA roles (`combobox`, `listbox`, `option`), support keyboard navigation (arrow keys, Enter, Escape), and manage focus correctly.
- **Deployment:** Serve static assets via a CDN and cache option data appropriately if it changes infrequently.

---

# Pitfalls

- **Filtering on every render without memoization**, which can become slow for large datasets.
- **Using array indexes as keys**, leading to rendering issues when options change.
- **Ignoring accessibility**, such as missing keyboard support, ARIA attributes, or focus management.

## Question 4. How do you implement a sortable table with dynamic columns?

## Question 5. How do you implement infinite scrolling with API pagination?

## Question 6. How do you implement sticky footers in React layouts?

## Question 7. How do you implement collapsible sections in content pages?

## Question 8. How do you implement a carousel/slider with autoplay and navigation buttons?

## Question 9. How do you implement a rating/review component with stars and comments?

## Question 10. How do you implement a dynamic breadcrumb navigation component?

## Question 11. How do you implement Suspense for data fetching with Concurrent Mode?

## Question 12. How do you implement lazy-loading routes with React Router v6?

## Question 13. How do you implement SSR with Next.js and dynamic data?

## Question 14. How do you implement Incremental Static Regeneration (ISR) in Next.js?

## Question 15. How do you implement authentication using OAuth 2.0 in React?

## Question 16. How do you implement JWT token refresh in a React app?

## Question 17. How do you implement role-based access control (RBAC) in React Router?

## Question 18. How do you implement WebSockets for real-time chat in React?

## Question 19. How do you implement offline-first React apps with service workers?

## Question 20. How do you implement React Query for caching, fetching, and synchronization?
