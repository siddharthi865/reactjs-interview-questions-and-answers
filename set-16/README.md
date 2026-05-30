# Set 16

| S.No. | Question                                                                                                                                                                                                                                                |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between SPA (Single Page Application) and MPA (Multi Page Application) in the context of React?](#question-1-what-is-the-difference-between-spa-single-page-application-and-mpa-multi-page-application-in-the-context-of-react) |
| 2.    | [How do you create a React project without using Create React App?](#question-2-how-do-you-create-a-react-project-without-using-create-react-app)                                                                                                       |
| 3.    | [How do you update state based on previous state in functional components?](#question-3-how-do-you-update-state-based-on-previous-state-in-functional-components)                                                                                       |
| 4.    | [What is the difference between setState callback and useEffect?](#question-4-what-is-the-difference-between-setstate-callback-and-useeffect)                                                                                                           |
| 5.    | [How do you handle multiple sibling components updating the same state?](#question-5-how-do-you-handle-multiple-sibling-components-updating-the-same-state)                                                                                             |
| 6.    | [How do you pass props with default values in React?](#question-6-how-do-you-pass-props-with-default-values-in-react)                                                                                                                                   |
| 7.    | [How do you prevent default anchor (`<a>`) behavior in React?](#question-7-how-do-you-prevent-default-anchor-a-behavior-in-react)                                                                                                                       |
| 8.    | [What is the difference between componentDidUpdate and useEffect?](#question-8-what-is-the-difference-between-componentdidupdate-and-useeffect)                                                                                                         |
| 9.    | [How do you implement a simple tab component in React?](#question-9-how-do-you-implement-a-simple-tab-component-in-react)                                                                                                                               |
| 10.   | [How do you handle multiple buttons triggering different functions?](#question-10-how-do-you-handle-multiple-buttons-triggering-different-functions)                                                                                                    |
| 11.   | [How do you display a fallback UI when an image fails to load?](#question-11-how-do-you-display-a-fallback-ui-when-an-image-fails-to-load)                                                                                                              |
| 12.   | [How do you combine multiple state updates into one setState call in class components?](#question-12-how-do-you-combine-multiple-state-updates-into-one-setstate-call-in-class-components)                                                              |
| 13.   | [How do you use the key prop in nested lists in React?](#question-13-how-do-you-use-the-key-prop-in-nested-lists-in-react)                                                                                                                              |
| 14.   | [How do you conditionally disable a button in React?](#question-14-how-do-you-conditionally-disable-a-button-in-react)                                                                                                                                  |
| 15.   | [How do you implement a simple counter with increment and decrement buttons?](#question-15-how-do-you-implement-a-simple-counter-with-increment-and-decrement-buttons)                                                                                  |
| 16.   | [How do you use Boolean props for conditional rendering?](#question-16-how-do-you-use-boolean-props-for-conditional-rendering)                                                                                                                          |
| 17.   | [How do you map over an array of objects and render components dynamically?](#question-17-how-do-you-map-over-an-array-of-objects-and-render-components-dynamically)                                                                                    |
| 18.   | [How do you prevent a form from resetting on submit?](#question-18-how-do-you-prevent-a-form-from-resetting-on-submit)                                                                                                                                  |
| 19.   | [How do you use React Developer Tools for debugging?](#question-19-how-do-you-use-react-developer-tools-for-debugging)                                                                                                                                  |
| 20.   | [How do you handle checkbox groups in forms?](#question-20-how-do-you-handle-checkbox-groups-in-forms)                                                                                                                                                  |

## Question 1. What is the difference between SPA (Single Page Application) and MPA (Multi Page Application) in the context of React?

# Short answer

A **Single Page Application (SPA)** loads a single HTML page and updates the UI dynamically using JavaScript without full page reloads. React is primarily designed for building SPAs.

A **Multi Page Application (MPA)** consists of multiple HTML pages. Navigating between pages typically triggers a full page reload, although React can be used to progressively enhance individual pages or power parts of an MPA. Modern frameworks like **Next.js App Router** also support MPA-like routing with SSR while maintaining React's component model.

---

# Explanation

## Single Page Application (SPA)

In an SPA:

- The browser downloads a single HTML file (`index.html`).
- React takes control of rendering the UI.
- Navigation is handled by client-side routing (e.g., React Router).
- Only the required components are re-rendered instead of reloading the entire page.

### Architecture

```
Browser
    │
index.html
    │
 React App
    │
React Router
    │
Components
```

### Characteristics

- Client-side routing
- Fast navigation after initial load
- Excellent user experience
- API-driven (REST/GraphQL)
- State lives on the client
- Uses React reconciliation for efficient rendering

### React 18 considerations

React 18 improves SPA responsiveness through:

- Concurrent Rendering
- Automatic Batching
- Suspense
- Transitions (`startTransition`)
- Streaming support (when paired with SSR frameworks)

---

## Multi Page Application (MPA)

In an MPA:

- Every route corresponds to a different HTML page.
- Browser requests a new document from the server.
- Entire page reloads.
- JavaScript initializes again.

### Architecture

```
Browser
     │
 Request /products
     │
Server
     │
products.html

Browser
     │
Request /checkout
     │
Server
     │
checkout.html
```

React can still be used:

- On individual pages
- As isolated widgets
- Inside server-rendered applications
- Via frameworks like Next.js that combine SSR with client-side hydration

---

## SPA vs MPA Comparison

| Feature                | SPA                                               | MPA                                            |
| ---------------------- | ------------------------------------------------- | ---------------------------------------------- |
| Initial Load           | Slower (larger JS bundle)                         | Faster per page                                |
| Navigation             | Instant                                           | Full page reload                               |
| Routing                | Client-side (React Router)                        | Server-side                                    |
| SEO                    | Requires SSR/SSG or prerendering for best results | Naturally SEO-friendly                         |
| State                  | Preserved across navigation                       | Usually reset on reload unless persisted       |
| Performance After Load | Very fast                                         | Depends on server response                     |
| Offline Support        | Easier with Service Workers                       | Harder                                         |
| Bundle Size            | Can become large without code splitting           | Smaller per page                               |
| Best For               | Dashboards, SaaS, admin panels                    | Content-heavy sites, e-commerce, documentation |

---

# Example

**Scaffold a React SPA using Vite (recommended):**

```bash
npm create vite@latest react-spa -- --template react-ts
cd react-spa
npm install
npm run dev
```

`App.tsx`

```tsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function Home() {
  return <h2>Home Page</h2>;
}

function About() {
  return <h2>About Page</h2>;
}

export default function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link> | <Link to="/about">About</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

No full page reload occurs when navigating between routes because React Router updates the UI on the client.

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript

Why Vite?

- Fast development server powered by native ESM
- Lightning-fast Hot Module Replacement (HMR)
- Excellent TypeScript support
- Optimized production builds using Rollup

Avoid **Create React App (CRA)** because it is deprecated.

Alternative stacks:

- **Next.js**: SSR, SSG, React Server Components, App Router
- **Remix**: Nested routing and server-first data loading
- **Turbopack** (Next.js): Fast incremental bundling during development

### ESM vs CommonJS

- Prefer **ES Modules (ESM)** (`import`/`export`) in modern React projects.
- CommonJS (`require`, `module.exports`) is mainly used in legacy Node.js environments.

---

# Performance

### SPA optimization

- Split routes with `React.lazy()` and `Suspense`
- Use dynamic imports
- Memoize expensive computations with `useMemo`
- Prevent unnecessary renders using `React.memo`
- Memoize callbacks with `useCallback`
- Use React Profiler to identify rendering bottlenecks
- Cache API responses using libraries like TanStack Query or SWR
- Optimize bundle size with tree shaking and lazy loading

### MPA optimization

- Cache static assets aggressively
- Use CDN edge caching
- Server-side compression (Brotli/Gzip)
- Optimize HTML delivery
- Leverage HTTP caching headers

---

# Testing

For a Vite-based React app:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import Home from "./Home";

test("renders home", () => {
  render(<Home />);
  expect(screen.getByText("Home Page")).toBeInTheDocument();
});
```

For end-to-end testing, consider Playwright:

```bash
npm install -D @playwright/test
```

---

# Ops & Deployment

- Use Error Boundaries to isolate rendering failures.
- Log client-side errors to services such as Sentry or Datadog.
- Choose **CSR** for highly interactive dashboards and authenticated applications.
- Choose **SSR/SSG** (e.g., Next.js) for SEO-sensitive or content-heavy sites.
- Use route-level code splitting to reduce initial bundle size.
- Deploy static SPAs to platforms like Vercel, Netlify, or a CDN-backed object store.
- For SSR applications, consider edge deployments to reduce latency.

---

# Pitfalls

- Relying on a pure SPA for SEO-sensitive pages without SSR or prerendering.
- Loading the entire application in one bundle instead of using route-based code splitting.
- Assuming client-side routing works automatically on production servers; configure rewrite rules so all SPA routes resolve to `index.html`.

## Question 2. How do you create a React project without using Create React App?

# Short answer

Since **Create React App (CRA) is deprecated**, the recommended way to create a React project is to use **Vite** for client-side React applications or **Next.js** if you need server-side rendering (SSR), React Server Components, or full-stack capabilities.

The most common interview answer is:

```bash
npm create vite@latest my-react-app -- --template react-ts
cd my-react-app
npm install
npm run dev
```

---

# Explanation

React is just a UI library—it doesn't dictate how your project is built. Modern React applications rely on build tools/frameworks that provide:

- Fast development server
- Hot Module Replacement (HMR)
- TypeScript support
- Bundling
- Code splitting
- Tree shaking
- Environment variable handling
- Optimized production builds

Today, the most popular choices are:

| Tool        | Best For                   | Uses              |
| ----------- | -------------------------- | ----------------- |
| **Vite** ⭐ | Most React SPAs            | esbuild + Rollup  |
| **Next.js** | SSR, SSG, Full-stack React | Turbopack/Webpack |
| **Remix**   | Server-first applications  | Vite              |
| **Parcel**  | Zero-config projects       | Parcel Bundler    |

---

## Option 1: Create a React App with Vite (Recommended)

### TypeScript

```bash
npm create vite@latest my-app -- --template react-ts
```

### JavaScript

```bash
npm create vite@latest my-app -- --template react
```

Then:

```bash
cd my-app
npm install
npm run dev
```

Development server:

```
http://localhost:5173
```

Project structure:

```text
my-app/
├── public/
├── src/
│   ├── App.tsx
│   ├── main.tsx
│   └── assets/
├── index.html
├── package.json
├── tsconfig.json
└── vite.config.ts
```

---

## Option 2: Create a Next.js Project

For applications requiring:

- SSR
- SSG
- API routes
- Authentication
- SEO
- React Server Components

Run:

```bash
npx create-next-app@latest my-app
```

Choose:

- TypeScript ✅
- ESLint ✅
- App Router ✅
- Turbopack ✅

Start:

```bash
cd my-app
npm run dev
```

---

## Option 3: Using Remix

```bash
npx create-remix@latest
```

Ideal for:

- Nested routes
- Server rendering
- Progressive enhancement

---

# Example

**Scaffold a React + TypeScript project using Vite:**

```bash
npm create vite@latest my-react-app -- --template react-ts
cd my-react-app
npm install
npm run dev
```

`src/App.tsx`

```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <main>
      <h1>React + Vite</h1>

      <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>
    </main>
  );
}
```

Visit:

```
http://localhost:5173
```

---

# Tooling & Setup

**Preferred stack:** **Vite + React + TypeScript**

Why Vite?

- Extremely fast startup using **esbuild** for dependency pre-bundling.
- Native **ES Modules (ESM)** during development.
- Fast Hot Module Replacement (HMR).
- Optimized production builds with **Rollup**.
- Excellent TypeScript support with minimal configuration.

Avoid **Create React App (CRA)** because it is deprecated and receives minimal maintenance.

**ESM vs CommonJS**

- **ESM (`import`/`export`)** is the standard for modern React projects and browsers.
- **CommonJS (`require`)** is mainly used in older Node.js codebases. New React projects should prefer ESM.

---

# Performance

Modern tooling helps optimize performance:

- Use `React.lazy()` and `Suspense` for route/component code splitting.
- Profile rendering with **React DevTools Profiler**.
- Avoid unnecessary renders using `React.memo`.
- Memoize expensive calculations with `useMemo`.
- Memoize event handlers with `useCallback`.
- Use data-fetching libraries like **TanStack Query** or **SWR** for caching and background refetching.
- Enable bundle analysis (e.g., `rollup-plugin-visualizer` or `vite-bundle-visualizer`) to identify large dependencies.

---

# Testing

Install **Vitest** with **React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example test:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders heading", () => {
  render(<App />);
  expect(screen.getByText("React + Vite")).toBeInTheDocument();
});
```

For end-to-end testing:

```bash
npm install -D @playwright/test
```

---

# Ops & Deployment

- Use **Error Boundaries** to isolate UI failures.
- Log runtime errors with services like Sentry.
- Keep bundles small through lazy loading and tree shaking.
- Deploy Vite apps to platforms such as Vercel, Netlify, GitHub Pages, or any CDN-backed static host.
- Use **Next.js** when SSR, SEO, streaming, or edge rendering are required.

---

# Pitfalls

- **Using CRA for new projects** even though it is deprecated.
- **Choosing the wrong tool** (e.g., using a plain SPA when SSR/SEO is a key requirement).
- **Ignoring code splitting**, resulting in unnecessarily large JavaScript bundles and slower initial loads.

## Question 3. How do you update state based on previous state in functional components?

# Short answer

When updating state that depends on its **previous value**, use the **functional updater** form of `useState`:

```tsx
setState((prevState) => newState);
```

This ensures React always uses the **latest state value**, even when multiple state updates are batched together in React 18.

---

# Explanation

React state updates are **asynchronous** and may be **batched** for performance. If you calculate the next state using the current state variable directly, you can end up using a stale value.

### ❌ Incorrect (can use stale state)

```tsx
setCount(count + 1);
setCount(count + 1);
```

If `count` is `0`, both updates calculate `1`, so the final value becomes **1**, not **2**.

### ✅ Correct (uses previous state)

```tsx
setCount((prevCount) => prevCount + 1);
setCount((prevCount) => prevCount + 1);
```

Here, React passes the latest updated value into each updater function:

1. `0 → 1`
2. `1 → 2`

Final value: **2**

---

## Why use the functional updater?

Use it whenever the new state depends on:

- Previous state
- Multiple sequential updates
- Async callbacks (`setTimeout`, promises)
- Event handlers that may trigger several updates

---

## React 18 Rendering Behavior

React 18 introduced **automatic batching**, which groups multiple state updates into a single render, even inside:

- Promises
- `setTimeout`
- Native event handlers
- Async functions

Example:

```tsx
setCount((c) => c + 1);
setCount((c) => c + 1);
setName("John");
```

React batches these updates and performs **one render**, improving performance while ensuring each functional updater receives the latest state.

---

## Component Architecture

Keep state as close as possible to where it's used.

Example:

```text
App
 ├── Counter
 ├── TodoList
 └── Profile
```

Each component manages its own state independently, reducing unnecessary re-renders.

For shared or complex state:

- `useReducer`
- Context API
- Redux Toolkit
- Zustand
- TanStack Query (for server state)

---

# Example

## Scaffold a React + TypeScript app with Vite

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

`App.tsx`

```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  const incrementTwice = () => {
    setCount((prev) => prev + 1);
    setCount((prev) => prev + 1);
  };

  return (
    <main>
      <h2>Count: {count}</h2>

      <button onClick={incrementTwice}>Increment Twice</button>
    </main>
  );
}
```

Clicking the button increases the count by **2** because each update uses the latest state value.

---

# Tooling & Setup

**Preferred stack:** Vite + React + TypeScript

Why Vite?

- Fast dev server with native **ESM**
- Instant Hot Module Replacement (HMR)
- Optimized production builds with Rollup
- Excellent TypeScript support

Avoid **Create React App (CRA)** because it is deprecated.

### ESM vs CommonJS

- Use **ES Modules** (`import`/`export`) in modern React projects.
- CommonJS (`require`) is mainly for older Node.js projects.

Framework choices:

- **Vite** → SPAs
- **Next.js** → SSR, SSG, React Server Components
- **Remix** → Server-first applications

---

# Performance

- React 18 **automatic batching** reduces unnecessary renders.
- Use `React.memo` to prevent re-rendering unchanged child components.
- Use `useMemo` for expensive computations.
- Use `useCallback` for stable callback references passed to memoized children.
- Use `React.lazy()` and `Suspense` for route/component code splitting.
- Cache server data with **TanStack Query** or **SWR**.
- Profile rendering with the **React DevTools Profiler** before optimizing.

---

# Testing

Install testing tools:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom jsdom
```

Example:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("increments twice", () => {
  render(<App />);

  fireEvent.click(screen.getByText("Increment Twice"));

  expect(screen.getByText("Count: 2")).toBeInTheDocument();
});
```

For end-to-end testing:

```bash
npm install -D @playwright/test
```

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Log runtime errors with tools like Sentry.
- Keep bundles small through lazy loading and tree shaking.
- Prefer SSR (e.g., Next.js) for SEO-sensitive pages and CSR (e.g., Vite) for highly interactive dashboards.
- Deploy static Vite apps to Vercel, Netlify, or any CDN-backed static host.

---

# Pitfalls

- Using `setState(state + 1)` when the next state depends on the current state.
- Expecting state updates to be available immediately after calling `setState`.
- Mutating state objects or arrays instead of creating new copies before updating.

## Question 4. What is the difference between setState callback and useEffect?

## Question 5. How do you handle multiple sibling components updating the same state?

## Question 6. How do you pass props with default values in React?

## Question 7. How do you prevent default anchor (`<a>`) behavior in React?

## Question 8. What is the difference between componentDidUpdate and useEffect?

## Question 9. How do you implement a simple tab component in React?

## Question 10. How do you handle multiple buttons triggering different functions?

## Question 11. How do you display a fallback UI when an image fails to load?

## Question 12. How do you combine multiple state updates into one setState call in class components?

## Question 13. How do you use the key prop in nested lists in React?

## Question 14. How do you conditionally disable a button in React?

## Question 15. How do you implement a simple counter with increment and decrement buttons?

## Question 16. How do you use Boolean props for conditional rendering?

## Question 17. How do you map over an array of objects and render components dynamically?

## Question 18. How do you prevent a form from resetting on submit?

## Question 19. How do you use React Developer Tools for debugging?

## Question 20. How do you handle checkbox groups in forms?
