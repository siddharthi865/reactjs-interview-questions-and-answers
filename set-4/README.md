# Set 4

| #   | Question                                                                                                                                             |
| --- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | [What is prop-types and why is it used?](#question-1-what-is-prop-types-and-why-is-it-used)                                                          |
| 2   | [How do you test React components?](#question-2-how-do-you-test-react-components)                                                                    |
| 3   | [Difference between shallow rendering and full rendering in testing](#question-3-difference-between-shallow-rendering-and-full-rendering-in-testing) |
| 4   | [How do you handle async operations in React?](#question-4-how-do-you-handle-async-operations-in-react)                                              |
| 5   | [Explain debouncing and throttling in React components](#question-5-explain-debouncing-and-throttling-in-react-components)                           |
| 6   | [How do you use React.forwardRef?](#question-6-how-do-you-use-reactforwardref)                                                                       |
| 7   | [Explain controlled input with dynamic validation](#question-7-explain-controlled-input-with-dynamic-validation)                                     |
| 8   | [How do you fetch API data in React?](#question-8-how-do-you-fetch-api-data-in-react)                                                                |
| 9   | [Difference between fetch and axios in React](#question-9-difference-between-fetch-and-axios-in-react)                                               |
| 10  | [How do you use setState asynchronously in class components?](#question-10-how-do-you-use-setstate-asynchronously-in-class-components)               |
| 11  | [Explain React Fiber architecture](#question-11-explain-react-fiber-architecture)                                                                    |
| 12  | [How does React handle reconciliation?](#question-12-how-does-react-handle-reconciliation)                                                           |
| 13  | [Explain the difference between reconciliation and diffing](#question-13-explain-the-difference-between-reconciliation-and-diffing)                  |
| 14  | [How do you implement server-side rendering (SSR) in React?](#question-14-how-do-you-implement-server-side-rendering-ssr-in-react)                   |
| 15  | [What is Next.js and how is it related to React?](#question-15-what-is-nextjs-and-how-is-it-related-to-react)                                        |
| 16  | [How do you implement static site generation (SSG)?](#question-16-how-do-you-implement-static-site-generation-ssg)                                   |
| 17  | [Explain hydration in SSR](#question-17-explain-hydration-in-ssr)                                                                                    |
| 18  | [How do you handle global state in large React applications?](#question-18-how-do-you-handle-global-state-in-large-react-applications)               |
| 19  | [Compare Redux, Zustand, Jotai, and Recoil](#question-19-compare-redux-zustand-jotai-and-recoil)                                                     |
| 20  | [Explain middleware in Redux](#question-20-explain-middleware-in-redux)                                                                              |

## Question 1. What is prop-types and why is it used?

## Short answer

`prop-types` is a runtime type-checking library for React props that helps validate the data passed into components and warn developers during development if props don’t match expected types.

---

## Explanation

### What is `prop-types`?

`prop-types` is a separate package used in React (especially in JavaScript codebases) to define and validate the shape and type of props a component receives at runtime.

It was widely used before TypeScript became mainstream in React ecosystems. Even today, it is still useful in non-TypeScript projects or shared component libraries.

---

### Why is it used?

#### 1. Runtime validation (development safety net)

React itself does not enforce prop types at runtime. `prop-types` helps catch issues like:

- Missing required props
- Wrong data types (string vs number)
- Incorrect object shapes

This is especially useful in large apps where components are reused across teams.

---

#### 2. Better maintainability in JS projects

In JavaScript-only React apps, it acts as lightweight “type documentation” inside the component itself.

---

#### 3. Helps during debugging

Warnings appear in the browser console during development, making issues easier to trace early.

---

### React 18 context & modern perspective

- React 18 does **not include runtime prop validation**
- With **TypeScript**, most teams skip `prop-types`
- Still relevant for:
  - Legacy JS codebases
  - Third-party library authors
  - Gradual migration to TypeScript

- Works in both concurrent rendering and legacy rendering modes because it runs at runtime (not compile time)

---

### State management & architecture perspective

- `prop-types` is **not state management**
- It only validates **input contracts between components**
- Useful in component-driven architecture (design systems, micro-frontends)
- Does not replace:
  - TypeScript (compile-time safety)
  - Redux/Zustand/Context (state management)

---

## Example (React + JavaScript)

### Setup (Vite)

```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm install prop-types
npm run dev
```

---

### Component using prop-types

```jsx
import PropTypes from "prop-types";

function UserCard({ name, age, isAdmin }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>{isAdmin ? "Admin" : "User"}</p>
    </div>
  );
}

UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  isAdmin: PropTypes.bool,
};

export default UserCard;
```

---

## Performance

- `prop-types` runs **only in development mode** in most setups (stripped in production builds)
- No runtime performance cost in production bundles (when configured correctly)
- Still, avoid overusing complex validators in deeply nested structures
- In large apps, prefer TypeScript to eliminate runtime overhead entirely

**Optimization tips:**

- Prefer TypeScript for static validation
- Avoid heavy custom validators in `prop-types`
- Combine with `React.memo` for preventing unnecessary renders
- Use React DevTools Profiler to detect prop-driven re-renders

---

## Testing

With **Vitest + React Testing Library**:

```bash
npm install -D vitest @testing-library/react jsdom
```

Example test:

```jsx
import { render } from "@testing-library/react";
import UserCard from "./UserCard";

test("renders user card", () => {
  render(<UserCard name="John" age={25} isAdmin={false} />);
});
```

Note:

- `prop-types` warnings are not typically asserted in tests
- Focus testing on behavior, not type validation
- Type correctness should ideally be enforced by TypeScript

---

## Ops & Deployment

- `prop-types` is removed in production builds (minification + dead code elimination)
- No impact on bundle size in production
- Works seamlessly with:
  - Vite (ESM-based bundler)
  - Webpack
  - Next.js SSR/CSR

**Best practice:**

- Use `prop-types` only as a dev-time guard
- For SSR (Next.js), prefer TypeScript for consistency across server/client boundaries

---

## Pitfalls

- Over-reliance instead of TypeScript (does not scale well)
- Forgetting to mark critical props as `.isRequired`
- Assuming it prevents runtime crashes (it only warns)
- Using complex nested validators that reduce readability

## Question 2. How do you test React components?

# Short answer

React components are typically tested at three levels:

- **Unit tests** – Test an individual component in isolation.
- **Integration tests** – Test how multiple components work together.
- **End-to-end (E2E) tests** – Test complete user flows in a real browser.

Modern React applications commonly use:

- **Vitest** (or Jest) as the test runner
- **React Testing Library (RTL)** for component testing
- **Playwright** or **Cypress** for E2E testing

The recommended approach is to test **behavior from the user's perspective** rather than implementation details.

---

# Explanation

## React testing philosophy

The React team recommends writing tests that resemble how users interact with your application.

Instead of testing:

- Internal state
- Hook implementation
- Private methods
- Component instance

Test:

- What is rendered
- User interactions
- Accessibility
- API responses
- Navigation
- Error states

For example:

❌ Bad test

```tsx
expect(component.state.count).toBe(1);
```

✅ Good test

```tsx
await user.click(button);

expect(screen.getByText("Count: 1")).toBeInTheDocument();
```

---

## Types of React tests

### 1. Unit Testing

Tests a single component independently.

Example:

- Button renders
- Input accepts text
- Modal opens
- Badge displays correct status

Fast and easy to maintain.

---

### 2. Integration Testing

Tests several components working together.

Example:

```
Login Form
      ↓
Submit Button
      ↓
API Call
      ↓
Dashboard
```

This is the most valuable type of testing in React applications.

---

### 3. End-to-End Testing

Runs the application inside a real browser.

Example:

```
Open website
↓

Login
↓

Search product
↓

Add to cart
↓

Checkout
```

Tools:

- Playwright
- Cypress

---

## React 18 considerations

React 18 introduced:

- Concurrent rendering
- Automatic batching
- Suspense improvements

React Testing Library automatically wraps updates in `act()` where appropriate, but asynchronous rendering means you should prefer async queries like `findBy...` or `waitFor` when waiting for UI updates.

Example:

```tsx
await screen.findByText("Users Loaded");
```

instead of

```tsx
screen.getByText("Users Loaded");
```

---

## Component architecture

Well-tested React applications usually separate:

```
UI Components
      ↓
Hooks
      ↓
Services/API
```

Example:

```
UserCard
      ↓
useUsers()
      ↓
fetchUsers()
```

Test independently:

- UI rendering
- Hook behavior
- API mocking

This makes tests easier to write and maintain.

---

# Example (React + TypeScript)

## Create project (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

Install testing tools:

```bash
npm install -D vitest @testing-library/react @testing-library/user-event @testing-library/jest-dom jsdom
```

**Counter.tsx**

```tsx
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);

  return (
    <>
      <p>Count: {count}</p>

      <button onClick={() => setCount((c) => c + 1)}>Increment</button>
    </>
  );
}
```

**Counter.test.tsx**

```tsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import { describe, expect, it } from "vitest";
import Counter from "./Counter";

describe("Counter", () => {
  it("increments count", async () => {
    render(<Counter />);

    const user = userEvent.setup();

    await user.click(screen.getByRole("button", { name: /increment/i }));

    expect(screen.getByText("Count: 1")).toBeInTheDocument();
  });
});
```

Run tests:

```bash
npx vitest
```

---

# Tooling & Setup

**Preferred stack**

- **Vite** for fast development and native ESM support.
- **Vitest** integrates naturally with Vite and offers excellent performance.
- **React Testing Library** for behavior-focused component testing.
- **Playwright** for reliable cross-browser E2E testing.

**Avoid**

- **Create React App (CRA)**, as it is deprecated.

**ESM vs CommonJS**

- Vite and Vitest use **ES Modules (ESM)** by default, enabling faster startup and better tree-shaking.
- CommonJS is primarily used in older Node.js ecosystems and is less common in modern React projects.

---

# Performance

Testing itself doesn't optimize runtime performance, but it helps prevent regressions.

Performance practices:

- Profile with **React DevTools Profiler**
- Test memoized components (`React.memo`)
- Verify expensive calculations using `useMemo`
- Ensure callbacks passed to memoized children use `useCallback` when beneficial
- Test lazy-loaded components

```tsx
const Dashboard = React.lazy(() => import("./Dashboard"));
```

Mock slow APIs to keep tests deterministic and fast.

---

# Testing

Recommended testing pyramid:

```
        E2E
         ▲
 Integration
         ▲
     Unit Tests
```

Common commands:

```bash
npm run test
```

```bash
npx vitest
```

Playwright:

```bash
npx playwright test
```

Best practices:

- Prefer `screen.getByRole()` over CSS selectors.
- Use `userEvent` instead of `fireEvent` to better simulate real user interactions.
- Mock network requests with tools like Mock Service Worker (MSW) for integration tests.
- Test accessibility by querying elements the way assistive technologies do.

---

# Ops & Deployment

For production-ready applications:

- Run unit and integration tests in CI/CD pipelines.
- Execute Playwright E2E tests before deployment.
- Use **Error Boundaries** to gracefully handle rendering errors.
- Validate both **SSR** and **CSR** behavior if using frameworks like Next.js.
- Keep bundles small with code splitting and lazy loading.
- Deploy static assets through a CDN and monitor errors using services like Sentry.

---

# Pitfalls

- Testing implementation details (state, hooks, private methods) instead of user-visible behavior.
- Overusing snapshots, which can become brittle and difficult to maintain.
- Forgetting to `await` asynchronous updates, leading to flaky tests.

## Question 3. Difference between shallow rendering and full rendering in testing

# Short answer

**Shallow rendering** renders only the component being tested and does **not** render its child components. It was mainly associated with **Enzyme** and is now largely considered a legacy approach.

**Full rendering** renders the entire component tree, including child components, and is the recommended approach today using **React Testing Library (RTL)** because it better reflects how users interact with your application.

---

# Explanation

## Shallow Rendering

Shallow rendering renders only one level of the component tree.

Example:

```text
<App>
   ├── Header
   ├── Sidebar
   └── Dashboard
```

With shallow rendering:

```text
<App>
```

`Header`, `Sidebar`, and `Dashboard` are **not actually rendered**.

### Advantages

- Fast execution
- Easy to isolate a component
- Less setup for unit tests

### Disadvantages

- Doesn't test component integration
- Can miss real-world rendering issues
- Encourages testing implementation details
- Not recommended for modern React applications

---

## Full Rendering

Full rendering mounts the complete component tree.

Example:

```text
<App>
   ├── Header
   ├── Sidebar
   └── Dashboard
        ├── Card
        └── Chart
```

Everything is rendered, just like it would be in the browser.

### Advantages

- Tests actual user behavior
- Detects integration issues
- Works well with React Context, React Router, and Redux
- Aligns with React Testing Library's philosophy

### Disadvantages

- Slightly slower than shallow rendering
- May require mocking APIs or providers
- More setup for complex applications

---

## Comparison

| Feature                  | Shallow Rendering   | Full Rendering               |
| ------------------------ | ------------------- | ---------------------------- |
| Child components         | ❌ Not rendered     | ✅ Fully rendered            |
| DOM rendering            | Partial             | Complete                     |
| User interaction testing | Limited             | Excellent                    |
| React Context support    | Poor                | Excellent                    |
| Redux support            | Limited             | Excellent                    |
| React Router support     | Limited             | Excellent                    |
| Best for                 | Legacy Enzyme tests | Modern React Testing Library |
| Recommended today        | ❌ No               | ✅ Yes                       |

---

## React 18 considerations

With React 18's features like:

- Concurrent rendering
- Automatic batching
- Suspense
- Transitions

Shallow rendering becomes less representative because it doesn't exercise how components interact within the full tree.

Modern testing emphasizes **observable behavior** rather than component internals. Full rendering with React Testing Library is better suited to React 18 and later.

---

## Component architecture

Consider:

```text
App
 ├── Navbar
 ├── ProductList
 │      ├── ProductCard
 │      └── ProductCard
 └── Footer
```

### Shallow rendering

Tests only:

```text
ProductList
```

### Full rendering

Tests:

```text
ProductList
   ↓
ProductCard
   ↓
Button
   ↓
Image
```

This verifies that child components work together correctly.

---

# Example (React + TypeScript)

## Create project (Vite)

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm install -D vitest @testing-library/react @testing-library/user-event jsdom
npm run dev
```

### Parent component

```tsx
function Welcome() {
  return <h2>Welcome!</h2>;
}

export default function App() {
  return (
    <>
      <h1>Home</h1>
      <Welcome />
    </>
  );
}
```

### Full rendering test

```tsx
import { render, screen } from "@testing-library/react";
import { describe, it, expect } from "vitest";
import App from "./App";

describe("App", () => {
  it("renders parent and child components", () => {
    render(<App />);

    expect(screen.getByText("Home")).toBeInTheDocument();
    expect(screen.getByText("Welcome!")).toBeInTheDocument();
  });
});
```

Unlike shallow rendering, the child component (`Welcome`) is rendered and verified as part of the test.

---

# Tooling & Setup

**Preferred stack**

- **Vite** for development and native ESM support.
- **Vitest** as the test runner.
- **React Testing Library** for component testing.
- **Playwright** for end-to-end tests.

Avoid **Create React App (CRA)** because it is deprecated.

**ESM vs CommonJS**

- Vite and Vitest are ESM-first, offering faster startup and better tree-shaking.
- CommonJS remains primarily for older Node.js projects.

---

# Performance

Testing strategies don't directly affect application performance, but they influence test quality and maintainability.

When testing performance-sensitive components:

- Use the **React DevTools Profiler** to identify unnecessary renders.
- Verify memoized components (`React.memo`) still behave correctly.
- Test expensive computations using `useMemo`.
- Test callback stability when using `useCallback`.
- Verify lazy-loaded components with `React.lazy` and `Suspense`.

---

# Testing

Recommended approach:

- **Unit tests:** React Testing Library + Vitest
- **Integration tests:** React Testing Library with providers (Context, Router, Redux)
- **E2E tests:** Playwright

Avoid assertions based on:

- Component instances
- Internal state
- Private methods

Instead, test what users can see and do.

---

# Ops & Deployment

- Run unit and integration tests in CI/CD pipelines.
- Execute Playwright E2E tests before releases.
- Use Error Boundaries to handle rendering failures gracefully.
- Test both SSR and CSR behavior when using frameworks like Next.js.
- Use code splitting and lazy loading to optimize bundle size.
- Deploy assets through a CDN and monitor production issues with tools like Sentry.

---

# Pitfalls

- Using shallow rendering to test implementation details instead of observable behavior.
- Assuming a shallow test guarantees child components work correctly.
- Writing brittle tests that depend on component structure rather than user-visible output.

## Question 4. How do you handle async operations in React?

## Question 5. Explain debouncing and throttling in React components

## Question 6. How do you use React.forwardRef?

## Question 7. Explain controlled input with dynamic validation

## Question 8. How do you fetch API data in React?

## Question 9. Difference between fetch and axios in React

## Question 10. How do you use setState asynchronously in class components?

## Question 11. Explain React Fiber architecture

## Question 12. How does React handle reconciliation?

## Question 13. Explain the difference between reconciliation and diffing

## Question 14. How do you implement server-side rendering (SSR) in React?

## Question 15. What is Next.js and how is it related to React?

## Question 16. How do you implement static site generation (SSG)?

## Question 17. Explain hydration in SSR

## Question 18. How do you handle global state in large React applications?

## Question 19. Compare Redux, Zustand, Jotai, and Recoil

## Question 20. Explain middleware in Redux
