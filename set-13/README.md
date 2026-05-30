# Set 13

| S.No. | Question                                                                                                                                             |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [How do you share state between sibling components?](#question-1-how-do-you-share-state-between-sibling-components)                                  |
| 2.    | [How do you create a reusable modal component with context?](#question-2-how-do-you-create-a-reusable-modal-component-with-context)                  |
| 3.    | [How do you implement tab-based navigation with state?](#question-3-how-do-you-implement-tab-based-navigation-with-state)                            |
| 4.    | [How do you optimize component rendering with `React.memo`?](#question-4-how-do-you-optimize-component-rendering-with-reactmemo)                     |
| 5.    | [How do you use `useEffect` to listen to window resize events?](#question-5-how-do-you-use-useeffect-to-listen-to-window-resize-events)              |
| 6.    | [How do you implement theme switching using context and hooks?](#question-6-how-do-you-implement-theme-switching-using-context-and-hooks)            |
| 7.    | [How do you debounce input search using `useEffect`?](#question-7-how-do-you-debounce-input-search-using-useeffect)                                  |
| 8.    | [How do you cancel fetch requests in `useEffect`?](#question-8-how-do-you-cancel-fetch-requests-in-useeffect)                                        |
| 9.    | [How do you implement a countdown timer with hooks?](#question-9-how-do-you-implement-a-countdown-timer-with-hooks)                                  |
| 10.   | [How do you implement a "read more / read less" feature?](#question-10-how-do-you-implement-a-read-more--read-less-feature)                          |
| 11.   | [How do you fetch data when a user scrolls to the bottom of a page?](#question-11-how-do-you-fetch-data-when-a-user-scrolls-to-the-bottom-of-a-page) |
| 12.   | [How do you implement a dynamic form with multiple sections?](#question-12-how-do-you-implement-a-dynamic-form-with-multiple-sections)               |
| 13.   | [How do you integrate Google Maps in React?](#question-13-how-do-you-integrate-google-maps-in-react)                                                 |
| 14.   | [How do you implement image sliders or carousels in React?](#question-14-how-do-you-implement-image-sliders-or-carousels-in-react)                   |
| 15.   | [How do you handle multi-step form submission with hooks?](#question-15-how-do-you-handle-multi-step-form-submission-with-hooks)                     |
| 16.   | [How do you create a dynamic list with add/remove functionality?](#question-16-how-do-you-create-a-dynamic-list-with-addremove-functionality)        |
| 17.   | [How do you focus an input programmatically?](#question-17-how-do-you-focus-an-input-programmatically)                                               |
| 18.   | [How do you implement a search filter for a list?](#question-18-how-do-you-implement-a-search-filter-for-a-list)                                     |
| 19.   | [How do you lazy load images with IntersectionObserver in React?](#question-19-how-do-you-lazy-load-images-with-intersectionobserver-in-react)       |
| 20.   | [How do you implement a responsive sidebar menu?](#question-20-how-do-you-implement-a-responsive-sidebar-menu)                                       |

## Question 1. How do you share state between sibling components?

# How do you share state between sibling components?

## Short answer

Sibling components **cannot directly share state**. The recommended approach is to **lift the shared state up** to their closest common parent and pass the state and update functions down as props. For larger applications, use a shared state solution such as **React Context**, Redux, Zustand, Jotai, or server-state libraries like TanStack Query when appropriate.

---

# Explanation

In React, data flows **one way (parent → child)**. Since sibling components don't have a direct relationship, they communicate through their common ancestor.

The typical pattern is:

```
Parent
 ├── Sibling A (updates state)
 └── Sibling B (reads state)
```

The parent owns the state.

```
Parent
   state
     │
 ┌───┴────┐
 │        │
A        B
```

When **Sibling A** changes the state, React re-renders the parent, and the updated state is passed to **Sibling B**.

### Why React recommends lifting state up

- Creates a **single source of truth**
- Prevents duplicated state
- Makes components predictable
- Easier debugging
- Easier testing

---

## Common approaches

### 1. Lift State Up (Recommended)

Best for:

- Forms
- Small to medium applications
- Closely related components

Example:

```
Parent
  count
  setCount
```

Both children receive:

```
count
setCount
```

---

### 2. React Context

When many sibling or deeply nested components need the same state.

```
App
 └── Context Provider
      ├── Component A
      ├── Component B
      └── Component C
```

Avoids prop drilling.

---

### 3. Global State Libraries

For complex applications.

Examples:

- Redux Toolkit
- Zustand
- Jotai
- MobX
- Recoil (legacy/less actively maintained)

Useful when many unrelated components need the same state.

---

### 4. Server State

If the shared data comes from an API, avoid copying it into local state.

Instead use libraries like:

- TanStack Query
- SWR

These automatically share cached server state across components.

---

## Rendering behavior (React 18)

When a sibling updates shared state:

1. Event occurs.
2. State setter is called.
3. **Automatic batching** groups multiple updates triggered during the same event or async context into a single render where possible.
4. Parent re-renders.
5. Both siblings receive updated props.
6. React only updates the DOM where values actually changed.

If only one sibling depends on the changed value, optimize rendering with `React.memo` or by splitting components appropriately.

---

## Component architecture

Good architecture:

```
Dashboard
 ├── SearchBox
 ├── ProductList
 └── CartSummary
```

Shared state:

```
Dashboard
   searchTerm
   cartItems
```

Each child receives only the props it needs.

Avoid:

```
SearchBox
     ↓
ProductList
     ↓
CartSummary
```

Siblings should **not** update each other directly.

---

# Example

**Scaffold a modern React + TypeScript app (Vite):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

```tsx
import { useState } from "react";

function CounterButtons({ onIncrement }: { onIncrement: () => void }) {
  return <button onClick={onIncrement}>Increment</button>;
}

function CounterDisplay({ count }: { count: number }) {
  return <h2>Count: {count}</h2>;
}

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <>
      <CounterButtons onIncrement={() => setCount((c) => c + 1)} />
      <CounterDisplay count={count} />
    </>
  );
}
```

Here:

- `App` owns the shared state.
- `CounterButtons` updates it.
- `CounterDisplay` reads it.
- The siblings never communicate directly.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** as it is deprecated.
- Prefer **Vite** for fast startup, ESM-native development, and efficient builds.
- Use **Next.js App Router** when you need SSR, SSG, streaming, or React Server Components.
- **Remix** is a strong choice for data-driven routing and progressive enhancement.
- Modern tooling favors **ES Modules (ESM)** over CommonJS for better tree-shaking and browser-native module support.
- Vite uses native ESM during development and Rollup for production bundling.

---

# Performance

When sharing state:

- Keep state as close as possible to where it's needed to reduce unnecessary re-renders.
- Use `React.memo` to prevent sibling re-renders when props are unchanged.
- Use `useCallback` for stable callback references passed to memoized children.
- Use `useMemo` for expensive derived values, not as a default optimization.
- Split large contexts into smaller providers or use context selectors to avoid re-rendering all consumers.
- Use `React.lazy` and dynamic imports for code splitting.
- Profile rendering behavior with **React DevTools Profiler** before optimizing.

---

# Testing

Use **Vitest** with **React Testing Library** for unit and integration tests.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example command:

```bash
npm run test
```

Test that clicking the button updates the displayed count by rendering the parent component and asserting the shared state flows correctly between siblings.

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Log state transitions selectively in development; avoid verbose production logging.
- Use **Error Boundaries** to isolate UI failures (for render/lifecycle errors).
- Choose **CSR** for highly interactive SPAs and **SSR/SSG** (e.g., Next.js) for faster first paint and SEO.
- Keep bundles small with route-level code splitting, tree-shaking, and dependency analysis.
- Deploy static Vite apps to a CDN, or use edge-capable platforms (e.g., Vercel, Cloudflare) for SSR applications.

---

# Pitfalls

- **Duplicating state** in multiple sibling components instead of lifting it up.
- **Using Context for rapidly changing state**, causing unnecessary re-renders across many consumers.
- **Passing too many props (prop drilling)** instead of introducing Context or a state management library when the component tree grows.

## Question 2. How do you create a reusable modal component with context?

# How do you create a reusable modal component with context?

## Short answer

Create a **Modal Context** that exposes methods like `open()`, `close()`, and optionally `toggle()`. Wrap your application in a **ModalProvider**, render a single reusable `Modal` component near the root, and allow any descendant component to control the modal through a custom hook (e.g., `useModal`). This avoids prop drilling and centralizes modal state and behavior.

---

# Explanation

A reusable modal should separate **state management** from **presentation**.

Typical architecture:

```text
App
└── ModalProvider
    ├── Navbar
    ├── Dashboard
    ├── Products
    ├── Settings
    └── Global Modal
```

The provider owns the modal state:

- `isOpen`
- `content`
- `title` (optional)
- `open()`
- `close()`

Any component can do:

```tsx
const { open } = useModal();

open(<UserDetails user={user} />);
```

The provider renders the modal once, making it reusable across the entire application.

### Why Context?

Without Context:

```text
App
 ├── Dashboard
 │    └── Button
 └── Modal
```

You'd have to pass:

```text
openModal
closeModal
isOpen
content
```

through multiple levels (prop drilling).

With Context:

```text
Button
   ↓
useModal()
   ↓
ModalProvider
```

Any component can open or close the modal directly.

---

## React 18 Rendering Behavior

When `open()` is called:

1. State updates inside `ModalProvider`.
2. React 18 automatically batches updates occurring in the same event or async context.
3. Only the provider and components consuming the changed context re-render.
4. The modal appears with the new content.

For performance:

- Memoize context values with `useMemo`.
- Memoize callbacks with `useCallback`.
- Split contexts if unrelated state changes frequently.

---

## Component Architecture

A scalable folder structure:

```text
src/
 ├── context/
 │     ModalContext.tsx
 ├── hooks/
 │     useModal.ts
 ├── components/
 │     Modal.tsx
 ├── App.tsx
```

Responsibilities:

- **ModalProvider** → state management
- **Modal** → UI only
- **useModal()** → developer-friendly API

This keeps the modal reusable and maintainable.

---

# Example

**Scaffold a modern React + TypeScript app (Vite):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### `ModalContext.tsx`

```tsx
import {
  createContext,
  useContext,
  useState,
  useMemo,
  useCallback,
  ReactNode,
} from "react";

type ModalContextType = {
  open: (content: ReactNode) => void;
  close: () => void;
};

const ModalContext = createContext<ModalContextType | null>(null);

export function ModalProvider({ children }: { children: ReactNode }) {
  const [content, setContent] = useState<ReactNode>(null);

  const open = useCallback((node: ReactNode) => {
    setContent(node);
  }, []);

  const close = useCallback(() => {
    setContent(null);
  }, []);

  const value = useMemo(() => ({ open, close }), [open, close]);

  return (
    <ModalContext.Provider value={value}>
      {children}

      {content && (
        <div
          style={{
            position: "fixed",
            inset: 0,
            background: "rgba(0,0,0,.4)",
            display: "grid",
            placeItems: "center",
          }}
          onClick={close}
        >
          <div
            style={{
              background: "#fff",
              padding: "1rem",
              borderRadius: 8,
              minWidth: 300,
            }}
            onClick={(e) => e.stopPropagation()}
          >
            {content}
            <button onClick={close}>Close</button>
          </div>
        </div>
      )}
    </ModalContext.Provider>
  );
}

export function useModal() {
  const ctx = useContext(ModalContext);

  if (!ctx) {
    throw new Error("useModal must be used inside ModalProvider");
  }

  return ctx;
}
```

### `App.tsx`

```tsx
import { ModalProvider, useModal } from "./ModalContext";

function Home() {
  const { open } = useModal();

  return (
    <button onClick={() => open(<h2>Hello from a reusable modal!</h2>)}>
      Open Modal
    </button>
  );
}

export default function App() {
  return (
    <ModalProvider>
      <Home />
    </ModalProvider>
  );
}
```

This pattern is reusable because:

- The modal is rendered only once.
- Any component can open it.
- No prop drilling is required.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Use **Vite** for fast development, native ESM support, and optimized production builds.
- Use **Next.js App Router** if your application needs SSR, streaming, or React Server Components. The modal provider should be placed in a Client Component (e.g., a top-level layout marked with `"use client"`).
- Prefer **ES Modules (ESM)** over CommonJS for better tree-shaking and modern tooling compatibility.

---

# Performance

- Memoize the context value with `useMemo` to avoid unnecessary re-renders of consumers.
- Memoize `open` and `close` with `useCallback` so their references remain stable.
- Use `React.memo` for modal content components if they receive stable props.
- Lazy-load heavy modal content with `React.lazy` and `Suspense`.
- Profile modal open/close interactions with the **React DevTools Profiler**.
- Keep server state (API data) in libraries like TanStack Query instead of copying it into modal context.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test ideas:

- Verify `open()` displays the modal.
- Verify `close()` hides it.
- Ensure clicking the backdrop closes the modal while clicking inside does not.
- Test keyboard interactions such as closing with the **Escape** key if implemented.

For end-to-end testing, use **Playwright** to validate modal behavior in the browser.

---

# Ops & Deployment

- Add an **Error Boundary** around complex modal content so failures don't crash the entire application.
- For accessibility, use `role="dialog"`, `aria-modal="true"`, associate labels with `aria-labelledby`, trap keyboard focus, restore focus on close, and support the **Escape** key.
- Render the modal using **React Portals** (`createPortal`) into `document.body` to avoid stacking and overflow issues.
- Code-split infrequently used modal content to reduce the initial bundle size.
- In SSR frameworks (e.g., Next.js), ensure portal targets exist only on the client.

---

# Pitfalls

- **Creating a new context value object on every render**, causing all consumers to re-render. Memoize it.
- **Storing unrelated global state** in the modal context, making updates more expensive.
- **Ignoring accessibility**, such as missing focus management, ARIA attributes, or keyboard support.

## Question 3. How do you implement tab-based navigation with state?

# How do you implement tab-based navigation with state?

## Short answer

Implement tab-based navigation by storing the **currently active tab** in React state (typically with `useState`). Render the active tab's content conditionally based on that state, and update it when a user clicks a tab. For URL-based navigation, use **React Router**, but for UI-only tabs, local state is usually sufficient.

---

# Explanation

A tab component consists of:

- **Active tab state**
- **Tab buttons**
- **Tab panels (content)**
- **Click handler** to change the active tab

Example flow:

```text
User clicks "Profile"
        │
        ▼
setActiveTab("profile")
        │
        ▼
React re-renders
        │
        ▼
Profile panel is displayed
```

The active tab acts as the **single source of truth**.

```text
Tabs
 ├── Home
 ├── Profile
 └── Settings

State
activeTab = "profile"
```

---

## React 18 Rendering Behavior

When a user clicks a tab:

1. Click event fires.
2. `setActiveTab()` updates the state.
3. React 18 automatically batches state updates occurring in the same event or async context.
4. The component re-renders.
5. Only the active tab panel is displayed.
6. React updates only the changed DOM elements.

If tab content is expensive:

- Memoize heavy child components.
- Lazy-load rarely used tabs.
- Keep inactive tabs mounted only if preserving state is required.

---

## Component Architecture

A reusable tab component can be organized as:

```text
Tabs
 ├── TabList
 ├── TabButton
 ├── TabPanels
 └── TabPanel
```

The parent (`Tabs`) owns:

- active tab
- tab change handler

Children receive props.

Example:

```text
Tabs
   activeTab
      │
 ┌────┴────┐
 │         │
Buttons   Panels
```

This keeps the component reusable and predictable.

---

# Example

**Scaffold a modern React + TypeScript app (Vite):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

```tsx
import { useState } from "react";

type Tab = "home" | "profile" | "settings";

export default function App() {
  const [activeTab, setActiveTab] = useState<Tab>("home");

  return (
    <div>
      <nav style={{ display: "flex", gap: "1rem" }}>
        <button onClick={() => setActiveTab("home")}>Home</button>

        <button onClick={() => setActiveTab("profile")}>Profile</button>

        <button onClick={() => setActiveTab("settings")}>Settings</button>
      </nav>

      <hr />

      {activeTab === "home" && <h2>Home Content</h2>}

      {activeTab === "profile" && <h2>Profile Content</h2>}

      {activeTab === "settings" && <h2>Settings Content</h2>}
    </div>
  );
}
```

This approach:

- Keeps the active tab in one place.
- Makes the UI predictable.
- Is easy to extend with dynamic tabs.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Use **Vite** for fast startup, native ESM support, and optimized production builds.
- Use **Next.js App Router** when tabs are part of an SSR application. Use URL segments or search parameters if tab state should be shareable via links.
- Prefer **ES Modules (ESM)** over CommonJS for better tree-shaking and compatibility with modern bundlers.

---

# Performance

- Wrap expensive tab panels with `React.memo` if their props are stable.
- Use `useMemo` for costly derived values inside a tab.
- Use `useCallback` for stable event handlers passed to memoized children.
- Lazy-load heavy tab content with `React.lazy` and `Suspense`.
- Profile tab switches with the **React DevTools Profiler**.
- If preserving state is important (e.g., form inputs), keep inactive panels mounted and hide them with CSS instead of unmounting them.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
render(<App />);
fireEvent.click(screen.getByText("Profile"));
expect(screen.getByText("Profile Content")).toBeInTheDocument();
```

For end-to-end testing, use **Playwright** to verify keyboard navigation and tab interactions.

---

# Ops & Deployment

- Build reusable tab components instead of duplicating logic.
- Follow accessibility guidelines by using `role="tablist"`, `role="tab"`, `role="tabpanel"`, `aria-selected`, and linking tabs to panels with `aria-controls` and `aria-labelledby`.
- Log tab change events if analytics are required.
- Split large tab panels into separate chunks to reduce the initial bundle size.
- If tabs represent distinct application views, consider routing (React Router or Next.js) instead of local state.

---

# Pitfalls

- **Using array indexes as tab identifiers**, making dynamic tabs harder to maintain.
- **Unmounting inactive tabs unintentionally**, causing loss of form or scroll state when persistence is expected.
- **Ignoring accessibility**, such as missing keyboard navigation (arrow keys, Home/End) and ARIA attributes.

## Question 4. How do you optimize component rendering with `React.memo`?

## Question 5. How do you use `useEffect` to listen to window resize events?

## Question 6. How do you implement theme switching using context and hooks?

## Question 7. How do you debounce input search using `useEffect`?

## Question 8. How do you cancel fetch requests in `useEffect`?

## Question 9. How do you implement a countdown timer with hooks?

## Question 10. How do you implement a "read more / read less" feature?

## Question 11. How do you fetch data when a user scrolls to the bottom of a page?

## Question 12. How do you implement a dynamic form with multiple sections?

## Question 13. How do you integrate Google Maps in React?

## Question 14. How do you implement image sliders or carousels in React?

## Question 15. How do you handle multi-step form submission with hooks?

## Question 16. How do you create a dynamic list with add/remove functionality?

## Question 17. How do you focus an input programmatically?

## Question 18. How do you implement a search filter for a list?

## Question 19. How do you lazy load images with IntersectionObserver in React?

## Question 20. How do you implement a responsive sidebar menu?
