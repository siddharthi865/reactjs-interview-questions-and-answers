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

## Question 3. How do you update state based on previous state in functional components?

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
