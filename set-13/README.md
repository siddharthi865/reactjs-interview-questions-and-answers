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

# How do you optimize component rendering with `React.memo`?

## Short answer

`React.memo` is a **Higher-Order Component (HOC)** that memoizes a functional component. It skips re-rendering when its **props have not changed** (using a shallow comparison by default). Use it for components that render frequently with the same props and are relatively expensive to render.

> `React.memo` optimizes **re-renders**, not the initial render.

---

# Explanation

By default, when a parent component re-renders, **all of its children also re-render**, even if their props haven't changed.

Without `React.memo`:

```text
Parent (re-renders)
├── Header
├── Sidebar
├── UserCard
└── Footer
```

Every child renders again.

With `React.memo`:

```text
Parent (re-renders)
├── Header      ✓ skipped
├── Sidebar     ✓ skipped
├── UserCard    ✓ skipped
└── Footer      ✓ skipped
```

React compares the previous and current props:

- If props are equal (shallow comparison), React reuses the previous rendered output.
- If any prop changes, React re-renders that component.

### How `React.memo` works

Internally, it performs a shallow comparison similar to:

```ts
prevProps === nextProps;
```

For primitive values (`number`, `string`, `boolean`), this works well.

For reference types (`object`, `array`, `function`), a new reference causes a re-render even if the contents are identical.

```tsx
// New object every render → Child re-renders
<Child user={{ name: "Alice" }} />
```

To benefit from `React.memo`, stabilize references using `useMemo` and `useCallback`.

---

## React 18 Rendering Behavior

With React 18:

1. Parent state changes.
2. Automatic batching groups multiple updates occurring in the same event or async context.
3. Parent re-renders.
4. React compares memoized child props.
5. Children with unchanged props are skipped.
6. Only components with changed props are reconciled and updated.

This reduces CPU work for large component trees.

---

## Component Architecture

A common pattern:

```text
Dashboard
├── SearchBar
├── Filters
├── ProductList
│   ├── ProductCard (memo)
│   ├── ProductCard (memo)
│   └── ProductCard (memo)
└── Footer
```

Only the affected `ProductCard` components re-render when their props change.

Another example:

```text
App
├── Header (memo)
├── Sidebar (memo)
├── Content
└── Footer (memo)
```

If only `Content` changes, the other components can skip rendering.

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
import { memo, useCallback, useState } from "react";

type CounterProps = {
  count: number;
};

const Counter = memo(({ count }: CounterProps) => {
  console.log("Counter rendered");
  return <h2>Count: {count}</h2>;
});

export default function App() {
  const [count, setCount] = useState(0);
  const [theme, setTheme] = useState("light");

  const increment = useCallback(() => {
    setCount((c) => c + 1);
  }, []);

  return (
    <>
      <Counter count={count} />

      <button onClick={increment}>Increment</button>

      <button
        onClick={() => setTheme((t) => (t === "light" ? "dark" : "light"))}
      >
        Toggle Theme ({theme})
      </button>
    </>
  );
}
```

**What happens?**

- Clicking **Increment** changes `count`, so `Counter` re-renders.
- Clicking **Toggle Theme** only changes `theme`; `Counter` receives the same `count` prop, so `React.memo` skips its re-render.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Use **Vite** for fast startup, native **ES Modules (ESM)** support, and optimized production builds.
- Use **Next.js App Router** when SSR, streaming, or React Server Components are required. Client-side interactive components that use `React.memo` must be Client Components (`"use client"`).
- Modern bundlers (Vite with Rollup, Next.js with Turbopack/Webpack) provide tree-shaking and efficient production builds.

---

# Performance

Use `React.memo` only after identifying a real rendering bottleneck.

Combine it with:

- **`useCallback`** to stabilize function props.
- **`useMemo`** to stabilize object or array props and cache expensive computations.
- **`React.lazy`** and `Suspense` for code splitting.
- Virtualization libraries (e.g., `react-window`) for long lists.
- **React DevTools Profiler** to measure render frequency and duration before and after optimization.

Example:

```tsx
const user = useMemo(() => ({ name: "Alice" }), []);
const onSave = useCallback(() => {
  // save logic
}, []);

<ProfileCard user={user} onSave={onSave} />;
```

Without `useMemo` and `useCallback`, `ProfileCard` would receive new object/function references on every parent render, defeating `React.memo`.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example strategy:

- Render a memoized component.
- Trigger unrelated parent state updates.
- Verify the UI remains correct.
- Optionally spy on render calls (e.g., `console.log` or a mock) to confirm unnecessary re-renders are skipped.

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Profile production builds instead of relying solely on development mode, where extra checks (such as Strict Mode) can make rendering behavior appear different.
- Use **Error Boundaries** to isolate rendering failures.
- Keep JavaScript bundles small with route-level code splitting and tree-shaking.
- Monitor runtime performance using browser performance tools or application monitoring.
- Balance SSR and CSR based on SEO, interactivity, and time-to-first-byte requirements.

---

# Pitfalls

- **Using `React.memo` everywhere**, adding comparison overhead without measurable benefit.
- **Passing new object, array, or function props** on every render, which defeats memoization unless stabilized with `useMemo` or `useCallback`.
- **Using expensive custom comparison functions** as the second argument to `React.memo`; they can cost more than the render they avoid.

## Question 5. How do you use `useEffect` to listen to window resize events?

# How do you use `useEffect` to listen to window resize events?

## Short answer

You use `useEffect` to **attach a `resize` event listener on `window` when the component mounts**, update state on resize, and **clean it up when the component unmounts** to avoid memory leaks.

---

# Explanation

`useEffect` is used for side effects like:

- DOM event listeners
- subscriptions
- timers
- API calls

For window resize handling:

### Lifecycle flow

```text id="xq2k8d"
Component mounts
   ↓
useEffect runs
   ↓
window.addEventListener("resize", handler)
   ↓
User resizes window → handler runs → state updates
   ↓
Component unmounts
   ↓
cleanup runs → removeEventListener
```

---

## React 18 behavior

In React 18:

- Effects run after render (commit phase)
- State updates from resize events trigger re-renders
- React automatically batches state updates (even in async event handlers in some cases)
- Cleanup runs before re-running effect or unmounting

Key implication:
👉 Frequent resize events can cause performance issues if not throttled/debounced.

---

## Component architecture

A clean pattern separates concerns:

```text id="c1n9kp"
Component
 ├── state (width, height)
 ├── effect (resize listener)
 └── UI (uses dimensions)
```

For large apps:

- Extract logic into a **custom hook**: `useWindowSize()`
- Keep UI components pure and reusable

---

# Example

**Scaffold a modern React + TypeScript app (Vite):**

```bash id="v9x2ld"
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

## Basic implementation

```tsx id="r8k3jd"
import { useEffect, useState } from "react";

export default function App() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener("resize", handleResize);

    // cleanup
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  }, []);

  return (
    <div>
      <h2>Window Size</h2>
      <p>Width: {size.width}</p>
      <p>Height: {size.height}</p>
    </div>
  );
}
```

---

## Production-grade improvement (with custom hook)

```tsx id="m4q9tp"
import { useEffect, useState } from "react";

function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight,
      });
    };

    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}

export default function App() {
  const { width, height } = useWindowSize();

  return (
    <div>
      <h2>Responsive Hook</h2>
      <p>
        {width} × {height}
      </p>
    </div>
  );
}
```

---

# Tooling & Setup

- **Avoid Create React App (CRA)** (deprecated).
- Use **Vite** for fast HMR and ESM-native development.
- For SSR apps, use **Next.js App Router** where window access must be guarded (since `window` is undefined on server).
- In Next.js, wrap access in `useEffect` or check `typeof window !== "undefined"`.

---

# Performance

Resize events can fire **dozens of times per second**, so optimization matters:

### 1. Debounce or throttle

```tsx id="t3k8sd"
const handleResize = () => {
  // update state
};
```

Better:

- Use `requestAnimationFrame`
- Or lodash `throttle`

### 2. Avoid unnecessary re-renders

- Only store needed values (e.g., width only if height unused)
- Split components so only dependent UI re-renders

### 3. Memoize heavy UI

```tsx id="p1k2sd"
const Chart = React.memo(({ width }: { width: number }) => {
  return <div>Chart width: {width}</div>;
});
```

### 4. Avoid inline state objects if possible

Frequent object creation can cause extra reconciliation work.

---

# Testing

Use **Vitest + React Testing Library**

```bash id="v2m8sd"
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

### Test strategy:

- Mock `window.innerWidth`
- Dispatch resize event
- Assert UI updates

Example:

```tsx id="k8x3sd"
window.innerWidth = 500;
window.dispatchEvent(new Event("resize"));
```

For E2E:

- Use **Playwright** to test responsive layouts at different viewport sizes.

---

# Ops & Deployment

- Ensure SSR safety: avoid direct `window` access outside `useEffect`
- Use Error Boundaries for UI safety
- Monitor performance with browser dev tools (Performance tab)
- Prefer CSS media queries when possible (better than JS resize listeners for layout changes)
- For large apps, consider a centralized responsive state (context or Zustand) if multiple components depend on window size

---

# Pitfalls

- ❌ Forgetting cleanup → memory leaks and duplicate listeners
- ❌ Directly using `window` in SSR (Next.js crash)
- ❌ Updating state too frequently without throttling → performance degradation

## Question 6. How do you implement theme switching using context and hooks?

# How do you implement theme switching using Context and Hooks?

## Short answer

Create a **Theme Context** that stores the current theme (e.g., `"light"` or `"dark"`) and exposes a `toggleTheme()` function. Wrap your application in a `ThemeProvider`, consume the context with a custom `useTheme()` hook, and apply the theme using a `data-theme` attribute, CSS variables, or a CSS-in-JS solution.

---

# Explanation

Theme switching is a common example of **global UI state**. Since multiple components (navbar, sidebar, buttons, cards, etc.) need access to the current theme, **React Context** is a good fit.

Typical architecture:

```text
App
└── ThemeProvider
    ├── Header
    ├── Sidebar
    ├── Dashboard
    ├── Footer
    └── ThemeToggle
```

The provider owns:

- Current theme (`light` or `dark`)
- `toggleTheme()`
- Optionally `setTheme()` for selecting a specific theme

Components consume the theme via a custom hook:

```tsx
const { theme, toggleTheme } = useTheme();
```

This avoids prop drilling and centralizes theme management.

---

## React 18 Rendering Behavior

When the user toggles the theme:

1. `toggleTheme()` updates the context state.
2. React 18 automatically batches updates occurring in the same event or async context.
3. The `ThemeProvider` re-renders.
4. Components consuming the theme context receive the updated value.
5. React updates only the affected DOM nodes.

Since every consumer of the context re-renders when the context value changes, keep the context focused on theme-related state only.

---

## Component Architecture

A scalable structure:

```text
src/
├── context/
│   └── ThemeContext.tsx
├── hooks/
│   └── useTheme.ts
├── components/
│   ├── ThemeToggle.tsx
│   └── Header.tsx
├── styles/
│   └── theme.css
└── App.tsx
```

Responsibilities:

- **ThemeProvider** → manages theme state.
- **useTheme()** → provides access to the theme API.
- **ThemeToggle** → switches themes.
- **CSS** → defines colors using CSS variables.

---

# Example

**Scaffold a modern React + TypeScript app (Vite):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm i
npm run dev
```

### `ThemeContext.tsx`

```tsx
import { createContext, useContext, useMemo, useState, ReactNode } from "react";

type Theme = "light" | "dark";

type ThemeContextType = {
  theme: Theme;
  toggleTheme: () => void;
};

const ThemeContext = createContext<ThemeContextType | null>(null);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<Theme>("light");

  const toggleTheme = () => {
    setTheme((prev) => (prev === "light" ? "dark" : "light"));
  };

  const value = useMemo(() => ({ theme, toggleTheme }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      <div data-theme={theme}>{children}</div>
    </ThemeContext.Provider>
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

function ThemeToggle() {
  const { theme, toggleTheme } = useTheme();

  return (
    <>
      <p>Current theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <ThemeToggle />
    </ThemeProvider>
  );
}
```

### `theme.css`

```css
[data-theme="light"] {
  --bg: white;
  --text: black;
}

[data-theme="dark"] {
  --bg: #1a1a1a;
  --text: white;
}

body {
  background: var(--bg);
  color: var(--text);
}
```

This implementation:

- Centralizes theme state.
- Avoids prop drilling.
- Uses CSS variables for efficient styling.
- Is easy to extend with additional themes.

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Use **Vite** for fast development, native **ES Modules (ESM)** support, and optimized production builds.
- Use **Next.js App Router** if SSR or React Server Components are required. The `ThemeProvider` must be a Client Component (`"use client"`).
- CSS variables are framework-agnostic and work well with Vite, Next.js, Tailwind CSS, or CSS Modules.

---

# Performance

- Memoize the context value with `useMemo` to avoid unnecessary consumer re-renders.
- Keep the theme context focused only on theme-related state.
- Use `React.memo` for expensive components that receive stable props.
- Lazy-load theme-specific assets if they are large.
- Use the **React DevTools Profiler** to verify rendering behavior before optimizing.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
render(
  <ThemeProvider>
    <ThemeToggle />
  </ThemeProvider>,
);

fireEvent.click(screen.getByText("Toggle Theme"));
expect(screen.getByText(/dark/i)).toBeInTheDocument();
```

For end-to-end testing, use **Playwright** to verify theme persistence and visual changes.

---

# Ops & Deployment

- Persist the selected theme in `localStorage` and restore it on initial load.
- Respect the user's system preference using `window.matchMedia("(prefers-color-scheme: dark)")`.
- In SSR applications (e.g., Next.js), avoid a flash of the wrong theme by initializing the theme before hydration.
- Use CSS variables instead of inline styles to minimize DOM updates.
- Log theme preference changes only if required for analytics.

---

# Pitfalls

- **Not memoizing the context value**, causing all consumers to re-render unnecessarily.
- **Ignoring persisted or system theme preferences**, leading to a poor user experience.
- **Putting unrelated global state into the theme context**, increasing unnecessary re-renders.

## Question 7. How do you debounce input search using `useEffect`?

# How do you debounce input search using `useEffect`?

## Short answer

Debouncing delays an action until the user **stops typing for a specified duration**. In React, you can implement debouncing with `useEffect` by setting a `setTimeout` whenever the input changes and clearing the previous timeout in the effect's cleanup function. This prevents unnecessary API calls on every keystroke.

---

# Explanation

Without debouncing:

```text
User types: React

R  → API call
Re → API call
Rea → API call
Reac → API call
React → API call
```

This results in **5 API requests**.

With a **500ms debounce**:

```text
User types: React

R
Re
Rea
Reac
React
      ↓ (500ms of inactivity)
      API call
```

Only **one API request** is made after the user pauses typing.

### How `useEffect` implements debouncing

1. User updates the input.
2. State (`query`) changes.
3. `useEffect` runs and starts a timer.
4. If the user types again before the timer finishes:
   - Cleanup runs.
   - The previous timer is cleared.
   - A new timer starts.

5. When the timer completes, update the debounced value or trigger the API call.

This pattern avoids race conditions caused by stale timers.

---

## React 18 Rendering Behavior

With React 18:

1. Input change updates state.
2. React automatically batches multiple state updates occurring in the same event.
3. Component re-renders.
4. `useEffect` schedules the timeout.
5. Cleanup cancels the previous timeout if the input changes again.
6. Only after the debounce delay does the API request execute.

For network requests, combine debouncing with `AbortController` to cancel outdated requests if a newer search starts.

---

## Component Architecture

A clean separation:

```text
SearchPage
├── SearchInput
├── useDebouncedValue (custom hook)
└── SearchResults
```

Responsibilities:

- **SearchInput** → captures user input.
- **useDebouncedValue** → delays updates.
- **SearchResults** → fetches and renders results.

This makes the debounce logic reusable across components.

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
import { useEffect, useState } from "react";

export default function App() {
  const [query, setQuery] = useState("");
  const [debouncedQuery, setDebouncedQuery] = useState("");

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedQuery(query);
    }, 500);

    return () => clearTimeout(timer);
  }, [query]);

  useEffect(() => {
    if (!debouncedQuery) return;

    console.log("Searching:", debouncedQuery);
    // Example:
    // fetch(`/api/search?q=${encodeURIComponent(debouncedQuery)}`);
  }, [debouncedQuery]);

  return (
    <div>
      <input
        type="text"
        placeholder="Search..."
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />

      <p>Searching for: {debouncedQuery}</p>
    </div>
  );
}
```

### Reusable custom hook

```tsx
import { useEffect, useState } from "react";

export function useDebouncedValue<T>(value: T, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}
```

Usage:

```tsx
const debouncedQuery = useDebouncedValue(query, 500);
```

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Use **Vite** for fast development, native **ES Modules (ESM)** support, and optimized production builds.
- Use **Next.js App Router** if your application requires SSR. Keep debounced search logic in Client Components (`"use client"`).
- Consider integrating with **TanStack Query** for caching and request deduplication once the debounced value changes.

---

# Performance

- Debounce API requests to reduce unnecessary network traffic.
- Cancel in-flight requests with `AbortController` when a newer search supersedes an older one.
- Memoize expensive derived results with `useMemo` when appropriate.
- Use `React.memo` for result list items if they receive stable props.
- Virtualize long result lists using libraries like `react-window`.
- Profile typing performance with the **React DevTools Profiler**.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm i -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Testing strategy:

- Use fake timers (`vi.useFakeTimers()`).
- Simulate rapid typing.
- Advance timers with `vi.advanceTimersByTime(500)`.
- Assert that the search function is called only once after the debounce delay.

For end-to-end testing, use **Playwright** to verify search behavior in the browser.

---

# Ops & Deployment

- Log search queries only if required, while respecting privacy requirements.
- Handle API errors gracefully and display loading/error states.
- Cache repeated searches using TanStack Query or SWR.
- Code-split large search result components if needed.
- Monitor API latency and debounce timing to balance responsiveness with server load.

---

# Pitfalls

- **Forgetting to clear the timeout**, resulting in multiple delayed executions.
- **Using debouncing for every interaction**, even when immediate feedback is expected (e.g., form validation on blur may be more appropriate).
- **Ignoring stale API responses**; cancel or ignore outdated requests to prevent older results from overwriting newer ones.

## Question 8. How do you cancel fetch requests in `useEffect`?

You can cancel fetch requests in `useEffect` using the **`AbortController`** API. This prevents:

- Memory leaks
- Updating state after a component unmounts
- Race conditions when multiple requests are made

---

## Example using `AbortController`

```jsx
import { useState, useEffect } from "react";

function User() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch("https://jsonplaceholder.typicode.com/users/1", {
      signal: controller.signal,
    })
      .then((response) => response.json())
      .then((data) => setUser(data))
      .catch((error) => {
        if (error.name !== "AbortError") {
          console.error(error);
        }
      });

    return () => {
      controller.abort();
    };
  }, []);

  return <div>{user ? user.name : "Loading..."}</div>;
}

export default User;
```

---

## How it works

1. Create an `AbortController`.

```jsx
const controller = new AbortController();
```

2. Pass its `signal` to `fetch`.

```jsx
fetch(url, {
  signal: controller.signal,
});
```

3. Abort the request during cleanup.

```jsx
return () => {
  controller.abort();
};
```

---

## Why use it?

Imagine a user navigates away before the API responds.

Without cancellation:

```
Component Mounted
      ↓
Fetch Started
      ↓
Component Unmounted
      ↓
Fetch Completes
      ↓
setState() runs ❌
```

With `AbortController`:

```
Component Mounted
      ↓
Fetch Started
      ↓
Component Unmounted
      ↓
abort() called
      ↓
Fetch Cancelled ✅
```

---

## Example: Search with changing query

Cancel the previous request whenever the search term changes.

```jsx
import { useEffect, useState } from "react";

function Search() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) return;

    const controller = new AbortController();

    fetch(`https://api.example.com/search?q=${query}`, {
      signal: controller.signal,
    })
      .then((res) => res.json())
      .then((data) => setResults(data))
      .catch((err) => {
        if (err.name !== "AbortError") {
          console.error(err);
        }
      });

    return () => controller.abort();
  }, [query]);

  return (
    <>
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />

      <ul>
        {results.map((item) => (
          <li key={item.id}>{item.name}</li>
        ))}
      </ul>
    </>
  );
}
```

Here, every time the user types:

- The previous request is cancelled.
- A new request starts.
- Only the latest response is processed.

---

## Using `async/await`

```jsx
useEffect(() => {
  const controller = new AbortController();

  async function fetchData() {
    try {
      const response = await fetch(
        "https://jsonplaceholder.typicode.com/posts",
        {
          signal: controller.signal,
        },
      );

      const data = await response.json();
      setPosts(data);
    } catch (error) {
      if (error.name !== "AbortError") {
        console.error(error);
      }
    }
  }

  fetchData();

  return () => controller.abort();
}, []);
```

---

## Benefits

- Prevents memory leaks.
- Avoids updating state after a component unmounts.
- Eliminates race conditions from overlapping requests.
- Improves performance by cancelling unnecessary network requests.
- Uses the built-in browser `AbortController` API without additional libraries.

---

### Interview Tip

If asked _"How do you cancel fetch requests in `useEffect`?"_, a concise answer is:

> Use the browser's **`AbortController`**. Create a controller inside `useEffect`, pass `controller.signal` to the `fetch` request, and call `controller.abort()` in the cleanup function. Handle the `AbortError` separately so that cancelled requests don't trigger error handling. This prevents memory leaks and stale state updates when the component unmounts or the effect re-runs.

## Question 9. How do you implement a countdown timer with hooks?

A countdown timer in React can be implemented using the **`useState`** hook to store the remaining time and the **`useEffect`** hook to update the timer every second. The interval should be cleaned up to prevent memory leaks.

## Basic Countdown Timer

```jsx
import { useState, useEffect } from "react";

function CountdownTimer() {
  const [seconds, setSeconds] = useState(10);

  useEffect(() => {
    if (seconds <= 0) return;

    const timer = setInterval(() => {
      setSeconds((prev) => prev - 1);
    }, 1000);

    return () => clearInterval(timer);
  }, [seconds]);

  return (
    <div>
      <h2>Time Left: {seconds}s</h2>
    </div>
  );
}

export default CountdownTimer;
```

### How it works

1. `useState` initializes the countdown value.
2. `useEffect` starts an interval.
3. Every second, the state is decremented.
4. The cleanup function clears the interval.
5. When the timer reaches `0`, the effect exits without creating another interval.

---

## Optimized Version (Single Interval)

Instead of creating a new interval every second, create it once and use a functional state update.

```jsx
import { useState, useEffect } from "react";

function CountdownTimer() {
  const [seconds, setSeconds] = useState(10);

  useEffect(() => {
    const timer = setInterval(() => {
      setSeconds((prev) => {
        if (prev <= 1) {
          clearInterval(timer);
          return 0;
        }
        return prev - 1;
      });
    }, 1000);

    return () => clearInterval(timer);
  }, []);

  return <h2>{seconds}s</h2>;
}

export default CountdownTimer;
```

**Why this is better:**

- Only one interval is created.
- Uses a functional state update to always access the latest state.
- Clears the interval automatically when the countdown reaches zero.

---

## Countdown with Start, Pause, and Reset

```jsx
import { useState, useEffect } from "react";

function CountdownTimer() {
  const initialTime = 20;

  const [seconds, setSeconds] = useState(initialTime);
  const [running, setRunning] = useState(false);

  useEffect(() => {
    if (!running || seconds === 0) return;

    const timer = setInterval(() => {
      setSeconds((prev) => prev - 1);
    }, 1000);

    return () => clearInterval(timer);
  }, [running, seconds]);

  return (
    <div>
      <h2>{seconds}s</h2>

      <button onClick={() => setRunning(true)}>Start</button>

      <button onClick={() => setRunning(false)}>Pause</button>

      <button
        onClick={() => {
          setRunning(false);
          setSeconds(initialTime);
        }}
      >
        Reset
      </button>
    </div>
  );
}

export default CountdownTimer;
```

---

## Using `setTimeout` Instead of `setInterval`

Another approach is to schedule the next tick with `setTimeout`.

```jsx
useEffect(() => {
  if (seconds <= 0) return;

  const timeout = setTimeout(() => {
    setSeconds((prev) => prev - 1);
  }, 1000);

  return () => clearTimeout(timeout);
}, [seconds]);
```

This avoids overlapping intervals and is often easier to reason about because each timeout schedules only the next update.

---

## Best Practices

- Always clear intervals or timeouts in the cleanup function.
- Use functional state updates (`setState(prev => ...)`) to avoid stale state.
- Stop the timer when it reaches `0`.
- Create only one interval when possible for better performance.
- Add controls (start, pause, reset) by managing a separate `running` state.

### Interview Tip

If asked **"How do you implement a countdown timer with hooks?"**, you can answer:

> "I use `useState` to store the remaining time and `useEffect` to start a timer with `setInterval` or `setTimeout`. On each tick, I decrement the state using a functional update. I clear the timer in the cleanup function to prevent memory leaks, and stop the countdown when it reaches zero. For additional controls like pause or reset, I manage a separate `running` state."

## Question 10. How do you implement a "read more / read less" feature?

A **"Read More / Read Less"** feature allows users to toggle between a shortened version of text and the full content. In React, this is typically implemented using the **`useState`** hook.

---

## Basic Example

```jsx
import { useState } from "react";

function ReadMore() {
  const [isExpanded, setIsExpanded] = useState(false);

  const text =
    "React is a JavaScript library for building user interfaces. It uses a component-based architecture, virtual DOM, hooks, and declarative rendering to build fast and scalable web applications.";

  const shortText = text.slice(0, 80);

  return (
    <div>
      <p>{isExpanded ? text : `${shortText}...`}</p>

      <button onClick={() => setIsExpanded(!isExpanded)}>
        {isExpanded ? "Read Less" : "Read More"}
      </button>
    </div>
  );
}

export default ReadMore;
```

---

## Reusable Component

```jsx
import { useState } from "react";

function ReadMore({ text, maxLength = 100 }) {
  const [expanded, setExpanded] = useState(false);

  if (text.length <= maxLength) {
    return <p>{text}</p>;
  }

  return (
    <div>
      <p>{expanded ? text : `${text.substring(0, maxLength)}...`}</p>

      <button onClick={() => setExpanded(!expanded)}>
        {expanded ? "Read Less" : "Read More"}
      </button>
    </div>
  );
}

export default ReadMore;
```

### Usage

```jsx
function App() {
  return (
    <ReadMore
      text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
      maxLength={60}
    />
  );
}
```

---

## Using CSS Line Clamp

If you want to limit the number of visible lines instead of characters:

```jsx
import { useState } from "react";

function ReadMore() {
  const [expanded, setExpanded] = useState(false);

  return (
    <>
      <p
        style={{
          display: "-webkit-box",
          WebkitLineClamp: expanded ? "unset" : 3,
          WebkitBoxOrient: "vertical",
          overflow: "hidden",
        }}
      >
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod
        tempor incididunt ut labore et dolore magna aliqua...
      </p>

      <button onClick={() => setExpanded(!expanded)}>
        {expanded ? "Read Less" : "Read More"}
      </button>
    </>
  );
}
```

This approach preserves whole words and works well for paragraphs with varying lengths.

---

## How It Works

1. Store the expanded/collapsed state with `useState`.
2. Display either:
   - the full text, or
   - a truncated version.

3. Toggle the state when the button is clicked.
4. Update the button label dynamically.

---

## Best Practices

- Make the component reusable by accepting `text` and `maxLength` as props.
- Hide the button if the text is already shorter than `maxLength`.
- Consider truncating at word boundaries instead of fixed characters for better readability.
- For long articles, prefer CSS line clamping to avoid cutting text awkwardly.
- Ensure the toggle button is keyboard accessible.

---

### Interview Tip

If asked **"How do you implement a Read More / Read Less feature in React?"**, you can answer:

> "I use `useState` to track whether the content is expanded. Based on that state, I conditionally render either a truncated version of the text or the full content. A button toggles the state, updating both the displayed text and the button label. For a reusable solution, I accept the text and maximum length as props, and for better UX with paragraphs, I may use CSS line clamping."

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
