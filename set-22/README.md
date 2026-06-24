# Set 22

| #   | Question                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [How do you render a default “No data available” message?](#question-1-how-do-you-render-a-default-no-data-available-message)                                              |
| 2   | [How do you use Fragment shorthand syntax?](#question-2-how-do-you-use-fragment-shorthand-syntax)                                                                          |
| 3   | [How do you handle onClick events for dynamically generated buttons?](#question-3-how-do-you-handle-onclick-events-for-dynamically-generated-buttons)                      |
| 4   | [How do you implement a “toggle visibility” component?](#question-4-how-do-you-implement-a-toggle-visibility-component)                                                    |
| 5   | [How do you implement a simple slider using state?](#question-5-how-do-you-implement-a-simple-slider-using-state)                                                          |
| 6   | [How do you implement a counter with step increments?](#question-6-how-do-you-implement-a-counter-with-step-increments)                                                    |
| 7   | [How do you render elements conditionally using ternary operators in JSX?](#question-7-how-do-you-render-elements-conditionally-using-ternary-operators-in-jsx)            |
| 8   | [How do you combine multiple state variables into a single object?](#question-8-how-do-you-combine-multiple-state-variables-into-a-single-object)                          |
| 9   | [How do you render HTML from strings safely using dangerouslySetInnerHTML?](#question-9-how-do-you-render-html-from-strings-safely-using-dangerouslysetinnerhtml)          |
| 10  | [How do you implement a simple tabbed interface?](#question-10-how-do-you-implement-a-simple-tabbed-interface)                                                             |
| 11  | [How do you implement text truncation with “read more” functionality?](#question-11-how-do-you-implement-text-truncation-with-read-more-functionality)                     |
| 12  | [How do you update nested array objects in state?](#question-12-how-do-you-update-nested-array-objects-in-state)                                                           |
| 13  | [How do you implement simple form validation for required fields?](#question-13-how-do-you-implement-simple-form-validation-for-required-fields)                           |
| 14  | [How do you implement an input that converts text to uppercase automatically?](#question-14-how-do-you-implement-an-input-that-converts-text-to-uppercase-automatically)   |
| 15  | [How do you create a reusable button component with custom styles?](#question-15-how-do-you-create-a-reusable-button-component-with-custom-styles)                         |
| 16  | [How do you implement a custom hook for form input validation?](#question-16-how-do-you-implement-a-custom-hook-for-form-input-validation)                                 |
| 17  | [How do you implement a responsive navigation bar with dropdowns?](#question-17-how-do-you-implement-a-responsive-navigation-bar-with-dropdowns)                           |
| 18  | [How do you implement a “scroll to section” feature?](#question-18-how-do-you-implement-a-scroll-to-section-feature)                                                       |
| 19  | [How do you handle multiple dependent API calls in useEffect?](#question-19-how-do-you-handle-multiple-dependent-api-calls-in-useeffect)                                   |
| 20  | [How do you implement infinite scrolling with useRef and IntersectionObserver?](#question-20-how-do-you-implement-infinite-scrolling-with-useref-and-intersectionobserver) |

## Question 1. How do you render a default “No data available” message?

# Short answer

Render a fallback UI conditionally when your data is empty.

The most common pattern is:

```tsx
{
  items.length === 0 ? (
    <p>No data available.</p>
  ) : (
    items.map((item) => <Item key={item.id} item={item} />)
  );
}
```

---

# Explanation

In React, you typically render a **fallback state** whenever the fetched or computed data is empty. This improves the user experience by clearly communicating that there is currently nothing to display instead of rendering a blank page.

A production-ready component usually handles **four UI states**:

1. **Loading** – Data is being fetched.
2. **Error** – Request failed.
3. **Empty** – Request succeeded, but no records exist.
4. **Success** – Display the data.

Example flow:

```
Loading → Error? → Empty? → Data
```

Instead of directly checking `items.length`, always ensure the value exists:

```tsx
if (loading) return <Spinner />;
if (error) return <ErrorMessage />;
if (!items?.length) return <EmptyState />;

return <ItemList items={items} />;
```

This makes the component easier to maintain and avoids nested conditional rendering.

React 18's automatic batching doesn't change this pattern—it simply makes multiple state updates (e.g., `setLoading(false)` and `setItems(data)`) more efficient.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users] = useState<User[]>([]);

  return (
    <div>
      <h2>Users</h2>

      {users.length === 0 ? (
        <p>No data available.</p>
      ) : (
        <ul>
          {users.map((user) => (
            <li key={user.id}>{user.name}</li>
          ))}
        </ul>
      )}
    </div>
  );
}
```

A reusable approach:

```tsx
function EmptyState() {
  return <p>No data available.</p>;
}

function UserList({ users }: { users: User[] }) {
  if (!users.length) {
    return <EmptyState />;
  }

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

# Tooling & Setup

- Prefer **Vite** over Create React App (CRA), as CRA is deprecated.
- For SSR, SEO, or hybrid rendering, use **Next.js App Router**.
- Vite uses native **ES Modules (ESM)** during development for fast startup and HMR.
- Modern bundlers like **Vite (Rollup)** and **Turbopack** optimize code splitting and tree shaking automatically.

---

# Performance

Even though rendering an empty state is inexpensive, follow these practices for larger applications:

- Render a dedicated `<EmptyState />` component to keep rendering logic clean.
- Use **React Profiler** to identify unnecessary renders.
- Memoize expensive child components with `React.memo`.
- Use `useMemo` and `useCallback` only when profiling shows a benefit.
- Code-split large pages with `React.lazy()` and `Suspense`.
- Cache server data using libraries like **TanStack Query** or **SWR** to reduce unnecessary refetches.

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
import App from "./App";

test("shows empty message", () => {
  render(<App />);
  expect(screen.getByText("No data available.")).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Log API failures to monitoring tools (e.g., Sentry) while showing a friendly empty or error state.
- Wrap pages with an Error Boundary to catch rendering errors.
- Consider SSR (Next.js) for SEO-sensitive pages and CSR for highly interactive dashboards.
- Keep bundles small with lazy loading and route-level code splitting.
- Deploy static Vite apps to a CDN for fast global delivery.

---

# Pitfalls

- **Don't assume the array exists.** Use `!items?.length` instead of `items.length` if data may be `undefined`.
- **Differentiate loading from empty.** Don't show "No data available" while a request is still in progress.
- **Create reusable empty-state components** instead of duplicating the same message throughout the application.

## Question 2. How do you use Fragment shorthand syntax?

# Short answer

Use the **Fragment shorthand syntax** (`<>...</>`) to group multiple JSX elements **without adding an extra DOM node**.

```tsx
<>
  <h1>Title</h1>
  <p>Description</p>
</>
```

It behaves like `<React.Fragment>`, but with a shorter syntax.

---

# Explanation

React components must return **a single parent element**. If you don't want to wrap elements in an unnecessary `<div>`, use a **Fragment**.

There are two ways to write a Fragment:

### 1. Shorthand syntax (most common)

```tsx
<>
  <Header />
  <Main />
  <Footer />
</>
```

### 2. Explicit syntax

```tsx
import { Fragment } from "react";

<Fragment>
  <Header />
  <Main />
  <Footer />
</Fragment>;
```

Both produce the same rendered output:

```html
<h1>...</h1>
<main>...</main>
<footer>...</footer>
```

No extra wrapper element is added to the DOM.

### When to use Fragment

- Returning multiple sibling elements from a component.
- Avoiding unnecessary `<div>` wrappers.
- Keeping HTML semantic (e.g., inside tables, lists, or flex/grid layouts).
- Reducing DOM size and avoiding layout issues caused by extra wrapper elements.

### Limitation of shorthand syntax

The shorthand syntax (`<>...</>`) **cannot accept props or a `key`**.

If you need a `key`, use the explicit syntax:

```tsx
import { Fragment } from "react";

items.map((item) => (
  <Fragment key={item.id}>
    <dt>{item.title}</dt>
    <dd>{item.description}</dd>
  </Fragment>
));
```

React 18's concurrent rendering and automatic batching do not change how Fragments work—they are a rendering convenience and do not create DOM nodes.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
export default function App() {
  return (
    <>
      <h1>React Fragment</h1>
      <p>No unnecessary wrapper div.</p>
      <button>Click Me</button>
    </>
  );
}
```

### Using a keyed Fragment

```tsx
import { Fragment } from "react";

type User = {
  id: number;
  name: string;
};

const users: User[] = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
];

export default function App() {
  return (
    <dl>
      {users.map((user) => (
        <Fragment key={user.id}>
          <dt>ID</dt>
          <dd>{user.id}</dd>

          <dt>Name</dt>
          <dd>{user.name}</dd>
        </Fragment>
      ))}
    </dl>
  );
}
```

---

# Tooling & Setup

- Use **Vite** for modern React development; avoid Create React App (CRA), which is deprecated.
- Use **TypeScript** for type safety and better IDE support.
- Vite uses **ES Modules (ESM)** during development for fast startup and Hot Module Replacement (HMR).
- Use **Next.js App Router** when you need SSR, Server Components, or SEO; use Vite for client-rendered SPAs.

---

# Performance

Fragments have virtually **no runtime cost** because they don't create additional DOM nodes.

For production applications:

- Use **React Profiler** to identify unnecessary re-renders.
- Use `React.memo` for expensive child components.
- Use `useMemo` and `useCallback` only when profiling indicates they improve performance.
- Use `React.lazy()` and `Suspense` for route- or component-level code splitting.
- Cache server data with libraries like **TanStack Query** or **SWR** to minimize redundant requests.

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
import App from "./App";

test("renders fragment content", () => {
  render(<App />);
  expect(screen.getByText("React Fragment")).toBeInTheDocument();
  expect(screen.getByRole("button")).toHaveTextContent("Click Me");
});
```

---

# Ops & Deployment

- Fragments help keep the DOM clean, which can simplify styling and improve maintainability.
- Use Error Boundaries to isolate rendering failures.
- Consider SSR with Next.js for SEO-sensitive pages and CSR with Vite for interactive dashboards.
- Minimize bundle size through lazy loading and route-based code splitting.
- Deploy static Vite applications to a CDN for fast global delivery.

---

# Pitfalls

- **Don't use Fragment shorthand when you need a `key`.** Use `<Fragment key={...}>` instead.
- **Don't wrap elements in unnecessary `<div>` tags** if a Fragment is sufficient.
- **Remember that Fragments don't render DOM elements**, so you cannot attach attributes like `className`, `style`, or event handlers to them.

## Question 3. How do you handle onClick events for dynamically generated buttons?

# Short answer

Handle `onClick` events for dynamically generated buttons by rendering them with `map()` and passing an event handler that receives the item's identifier or data.

```tsx
{
  users.map((user) => (
    <button key={user.id} onClick={() => handleClick(user.id)}>
      {user.name}
    </button>
  ));
}
```

Each button invokes the same handler with a different value.

---

# Explanation

In React, dynamically generated buttons are typically created using `Array.prototype.map()`. Each button gets:

- A unique `key` for React reconciliation.
- An `onClick` handler.
- Data specific to that item (usually an `id`).

Example flow:

```text
users[]
   │
   ▼
map()
   │
   ▼
<button onClick={() => handleClick(user.id)} />
```

### Passing parameters

Since React expects a **function reference**, don't call the function directly.

✅ Correct:

```tsx
onClick={() => handleClick(user.id)}
```

❌ Incorrect:

```tsx
onClick={handleClick(user.id)}
```

The incorrect version executes immediately during rendering instead of waiting for the click.

### Event object

If you need both the event and item data:

```tsx
onClick={(event) => handleClick(event, user.id)}
```

```tsx
const handleClick = (
  event: React.MouseEvent<HTMLButtonElement>,
  id: number,
) => {
  console.log(id);
};
```

### Rendering behavior

React creates a new arrow function on each render when using inline handlers. For most applications, this is perfectly acceptable. Only optimize if profiling reveals performance issues, such as thousands of frequently re-rendering items.

In React 18:

- Automatic batching groups multiple state updates triggered by the click into a single render.
- Concurrent rendering can improve responsiveness but doesn't change how `onClick` handlers are written.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [selected, setSelected] = useState<number | null>(null);

  const users: User[] = [
    { id: 1, name: "Alice" },
    { id: 2, name: "Bob" },
    { id: 3, name: "Charlie" },
  ];

  const handleClick = (id: number) => {
    setSelected(id);
  };

  return (
    <div>
      <h2>Users</h2>

      {users.map((user) => (
        <button
          key={user.id}
          onClick={() => handleClick(user.id)}
          style={{ marginRight: "8px" }}
        >
          {user.name}
        </button>
      ))}

      <p>Selected User ID: {selected ?? "None"}</p>
    </div>
  );
}
```

---

# Tooling & Setup

- Use **Vite** for modern React development. Avoid Create React App (CRA), as it is deprecated.
- Use **TypeScript** for better type safety and editor support.
- Vite uses **ES Modules (ESM)** for fast startup and Hot Module Replacement (HMR).
- Choose **Next.js App Router** when SSR, Server Components, or SEO are required; Vite is ideal for client-rendered SPAs.

---

# Performance

For small and medium lists, inline arrow functions are simple and performant.

For large, frequently re-rendering lists:

- Profile first using **React Profiler**.
- Memoize child components with `React.memo`.
- Use `useCallback` when passing handlers to memoized children to maintain stable function references.
- Use list virtualization (e.g., `react-window` or `react-virtualized`) for thousands of items.
- Use `React.lazy()` and `Suspense` for code splitting, and cache server data with libraries like TanStack Query or SWR.

Example with a memoized child:

```tsx
const handleClick = useCallback((id: number) => {
  setSelected(id);
}, []);
```

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("selects a user when button is clicked", async () => {
  render(<App />);

  await userEvent.click(screen.getByRole("button", { name: "Alice" }));

  expect(screen.getByText(/Selected User ID: 1/i)).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Log important click events (e.g., purchases or navigation) using analytics services such as Google Analytics or Segment.
- Use Error Boundaries to isolate rendering failures (they won't catch errors inside event handlers, so handle those with `try/catch` if needed).
- Use SSR with Next.js for SEO-focused pages and CSR with Vite for highly interactive applications.
- Optimize bundle size with route-level code splitting and deploy static assets through a CDN.

---

# Pitfalls

- **Don't invoke the handler during render.** Use `onClick={() => handleClick(id)}` instead of `onClick={handleClick(id)}`.
- **Always provide a stable `key`** when rendering buttons with `map()`. Avoid using array indexes if items can be reordered.
- **Don't overuse `useCallback`.** Apply it when it provides measurable benefits, particularly with memoized child components.

## Question 4. How do you implement a “toggle visibility” component?

# Short answer

Implement a **toggle visibility** component by using the `useState` hook to track whether content is visible and conditionally render it or hide it.

```tsx
const [isVisible, setIsVisible] = useState(false);

<button onClick={() => setIsVisible((prev) => !prev)}>
  {isVisible ? "Hide" : "Show"}
</button>;

{
  isVisible && <p>This content is visible.</p>;
}
```

---

# Explanation

A toggle visibility component maintains a **boolean state** (`true`/`false`) that determines whether a piece of UI should be displayed.

Typical workflow:

```text
Button Click
      │
      ▼
setIsVisible(prev => !prev)
      │
      ▼
React re-renders
      │
      ▼
Content shown/hidden
```

Using the **functional state update** is recommended because it always uses the latest state:

```tsx
setIsVisible((prev) => !prev);
```

instead of

```tsx
setIsVisible(!isVisible);
```

This avoids stale state issues when multiple updates are batched in React 18.

### Conditional Rendering Options

**1. Logical AND (`&&`)** – Best when rendering only if visible.

```tsx
{
  isVisible && <Details />;
}
```

**2. Ternary Operator** – Useful when rendering different UI.

```tsx
{
  isVisible ? <Details /> : <p>Hidden</p>;
}
```

**3. CSS Visibility** – Keeps the component mounted.

```tsx
<div style={{ display: isVisible ? "block" : "none" }}>
  <Details />
</div>
```

Choose based on the requirement:

- **Unmount component:** Conditional rendering (`&&` or ternary)
- **Keep state alive:** Hide with CSS

### React 18 Considerations

- **Automatic batching** combines multiple state updates triggered by the click into a single render.
- **Concurrent rendering** improves responsiveness without changing the implementation.
- If the hidden component is expensive to mount, consider keeping it mounted and toggling visibility with CSS or using memoization.

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

function ToggleVisibility() {
  const [isVisible, setIsVisible] = useState(false);

  return (
    <div>
      <button onClick={() => setIsVisible((prev) => !prev)}>
        {isVisible ? "Hide Details" : "Show Details"}
      </button>

      {isVisible && (
        <div style={{ marginTop: "1rem" }}>
          <h3>React Interview</h3>
          <p>This content can be toggled on and off.</p>
        </div>
      )}
    </div>
  );
}

export default function App() {
  return (
    <main>
      <ToggleVisibility />
    </main>
  );
}
```

---

# Tooling & Setup

- **Avoid Create React App (CRA)** since it is deprecated.
- Use **Vite** for fast development, instant Hot Module Replacement (HMR), and excellent TypeScript support.
- Vite uses **ES Modules (ESM)** during development and Rollup for optimized production builds.
- For applications requiring SSR, SEO, or React Server Components, use **Next.js App Router**. For SPAs, Vite is the preferred choice.

---

# Performance

Although a visibility toggle is lightweight, consider these optimizations in larger applications:

- Use **React Profiler** to measure rendering performance.
- Wrap expensive child components with `React.memo` to avoid unnecessary re-renders.
- Use `useMemo` for expensive calculations that depend on visibility.
- Use `useCallback` when passing toggle handlers to memoized children.
- Lazy-load heavy components:

```tsx
const SettingsPanel = React.lazy(() => import("./SettingsPanel"));
```

```tsx
<Suspense fallback={<p>Loading...</p>}>
  {isVisible && <SettingsPanel />}
</Suspense>
```

- Cache server data with **TanStack Query** or **SWR** so reopening the panel doesn't trigger unnecessary network requests.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("toggles content visibility", async () => {
  render(<App />);

  expect(screen.queryByText(/React Interview/i)).not.toBeInTheDocument();

  await userEvent.click(screen.getByRole("button", { name: /show details/i }));

  expect(screen.getByText(/React Interview/i)).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Log toggle interactions only if they represent meaningful user actions (e.g., opening a settings panel or expanding important content).
- Use **Error Boundaries** to isolate rendering failures in child components (note that they do not catch errors inside event handlers).
- For SEO-sensitive content, prefer SSR with Next.js; for interactive dashboards, CSR with Vite is often sufficient.
- Keep bundles small using lazy loading and route-level code splitting, and serve assets through a CDN for faster delivery.

---

# Pitfalls

- **Use functional state updates** (`setIsVisible(prev => !prev)`) instead of relying on the current state value.
- **Choose the right hiding strategy:** conditional rendering unmounts components; CSS hiding keeps them mounted and preserves internal state.
- **Don't overuse memoization.** Apply `React.memo`, `useMemo`, or `useCallback` only after profiling demonstrates a benefit.

## Question 5. How do you implement a simple slider using state?

# Short answer

Implement a simple slider by storing the **current slide index** in state and updating it with **Next** and **Previous** buttons.

```tsx
const [currentIndex, setCurrentIndex] = useState(0);

const next = () => setCurrentIndex((prev) => (prev + 1) % slides.length);

const prev = () =>
  setCurrentIndex((prev) => (prev - 1 + slides.length) % slides.length);

return <h2>{slides[currentIndex]}</h2>;
```

Using the functional state update ensures the latest state is used, even with React 18's automatic batching.

---

# Explanation

A slider (or carousel) displays **one item at a time** while maintaining the currently active slide in state.

The basic flow is:

```text
Current Index (state)
        │
        ▼
Render current slide
        │
        ▼
Previous / Next clicked
        │
        ▼
Update index
        │
        ▼
React re-renders with new slide
```

The current slide is usually stored as a numeric index:

```tsx
const [currentIndex, setCurrentIndex] = useState(0);
```

### Next Slide

```tsx
setCurrentIndex((prev) => (prev + 1) % slides.length);
```

The modulo (`%`) operator wraps back to the first slide after the last one.

### Previous Slide

```tsx
setCurrentIndex((prev) => (prev - 1 + slides.length) % slides.length);
```

Adding `slides.length` prevents negative indexes before applying modulo.

### Why use functional updates?

Instead of:

```tsx
setCurrentIndex(currentIndex + 1);
```

prefer:

```tsx
setCurrentIndex((prev) => prev + 1);
```

This avoids stale state when multiple updates are queued and works correctly with React 18's automatic batching.

### Production considerations

A production-ready slider often includes:

- Infinite looping
- Auto-play (`setInterval` with cleanup)
- Swipe support (touch/mouse gestures)
- Keyboard navigation
- Accessibility (`aria-label`, focus management)
- Animated transitions (CSS or animation libraries)

---

# Example

### Create the project (Vite + React + TypeScript)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

### `App.tsx`

```tsx
import { useState } from "react";

const slides = ["React", "TypeScript", "Vite", "Next.js"];

export default function App() {
  const [currentIndex, setCurrentIndex] = useState(0);

  const next = () => setCurrentIndex((prev) => (prev + 1) % slides.length);

  const prev = () =>
    setCurrentIndex((prev) => (prev - 1 + slides.length) % slides.length);

  return (
    <div style={{ textAlign: "center", padding: "2rem" }}>
      <h2>{slides[currentIndex]}</h2>

      <button onClick={prev}>Previous</button>

      <button onClick={next} style={{ marginLeft: "1rem" }}>
        Next
      </button>

      <p>
        {currentIndex + 1} / {slides.length}
      </p>
    </div>
  );
}
```

---

# Tooling & Setup

- **Avoid Create React App (CRA)** because it is deprecated.
- Prefer **Vite** for modern React development due to its fast dev server, HMR, and Rollup-based production builds.
- Vite uses **ES Modules (ESM)** during development for faster startup and efficient module loading.
- If SEO, SSR, or React Server Components are required, use **Next.js App Router**. For client-side SPAs, Vite is the preferred choice.

---

# Performance

For a simple slider, rendering is inexpensive, but production applications should consider:

- Use **React Profiler** to measure render frequency.
- Memoize expensive slide components using `React.memo`.
- Use `useCallback` for navigation handlers if they are passed to memoized child components.
- Lazy-load image-heavy slides with `React.lazy()` or native image lazy loading (`loading="lazy"`).
- Cache slide data fetched from APIs with **TanStack Query** or **SWR**.
- For large collections, render only the current and adjacent slides instead of the entire list.

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import App from "./App";

test("moves to the next slide", async () => {
  render(<App />);

  expect(screen.getByText("React")).toBeInTheDocument();

  await userEvent.click(screen.getByRole("button", { name: /next/i }));

  expect(screen.getByText("TypeScript")).toBeInTheDocument();
});
```

---

# Ops & Deployment

- Track meaningful interactions (e.g., slide changes) using analytics tools if they represent business events.
- Use Error Boundaries to isolate rendering failures in slide components (they do not catch errors inside event handlers).
- For marketing carousels that require SEO, prefer SSR with Next.js. For authenticated dashboards, CSR with Vite is typically sufficient.
- Optimize bundle size by lazy-loading media-heavy slides and serve static assets through a CDN.

---

# Pitfalls

- **Use functional state updates** (`setCurrentIndex(prev => ...)`) to avoid stale state issues.
- **Handle edge cases** such as an empty `slides` array before accessing `slides[currentIndex]`.
- **Clean up timers** with `useEffect` if implementing auto-play to prevent memory leaks.

## Question 6. How do you implement a counter with step increments?

## Question 7. How do you render elements conditionally using ternary operators in JSX?

## Question 8. How do you combine multiple state variables into a single object?

## Question 9. How do you render HTML from strings safely using dangerouslySetInnerHTML?

## Question 10. How do you implement a simple tabbed interface?

## Question 11. How do you implement text truncation with “read more” functionality?

## Question 12. How do you update nested array objects in state?

## Question 13. How do you implement simple form validation for required fields?

## Question 14. How do you implement an input that converts text to uppercase automatically?

## Question 15. How do you create a reusable button component with custom styles?

## Question 16. How do you implement a custom hook for form input validation?

## Question 17. How do you implement a responsive navigation bar with dropdowns?

## Question 18. How do you implement a “scroll to section” feature?

## Question 19. How do you handle multiple dependent API calls in useEffect?

## Question 20. How do you implement infinite scrolling with useRef and IntersectionObserver?
