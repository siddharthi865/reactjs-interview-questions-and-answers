# Set 11

| S.No. | Question                                                                                                                                                                                 |
| ----- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.    | [What is the difference between React and React Native?](#question-1-what-is-the-difference-between-react-and-react-native)                                                              |
| 2.    | [How do you include CSS in React components?](#question-2-how-do-you-include-css-in-react-components)                                                                                    |
| 3.    | [What is the difference between inline, internal, and external CSS in React?](#question-3-what-is-the-difference-between-inline-internal-and-external-css-in-react)                      |
| 4.    | [How do you apply conditional rendering with ternary operators?](#question-4-how-do-you-apply-conditional-rendering-with-ternary-operators)                                              |
| 5.    | [How do you handle multiple class names dynamically?](#question-5-how-do-you-handle-multiple-class-names-dynamically)                                                                    |
| 6.    | [What is the difference between state and props immutability?](#question-6-what-is-the-difference-between-state-and-props-immutability)                                                  |
| 7.    | [How do you combine multiple states into a single object in functional components?](#question-7-how-do-you-combine-multiple-states-into-a-single-object-in-functional-components)        |
| 8.    | [What are `defaultProps` and when are they useful?](#question-8-what-are-defaultprops-and-when-are-they-useful)                                                                          |
| 9.    | [How do you pass props to nested child components?](#question-9-how-do-you-pass-props-to-nested-child-components)                                                                        |
| 10.   | [How do you handle button click events in functional components?](#question-10-how-do-you-handle-button-click-events-in-functional-components)                                           |
| 11.   | [How do you render HTML content from a string safely?](#question-11-how-do-you-render-html-content-from-a-string-safely)                                                                 |
| 12.   | [How do you stop event propagation in React?](#question-12-how-do-you-stop-event-propagation-in-react)                                                                                   |
| 13.   | [How do you create a reusable button component?](#question-13-how-do-you-create-a-reusable-button-component)                                                                             |
| 14.   | [How do you conditionally render multiple components in a parent component?](#question-14-how-do-you-conditionally-render-multiple-components-in-a-parent-component)                     |
| 15.   | [How do you handle component styling with CSS modules?](#question-15-how-do-you-handle-component-styling-with-css-modules)                                                               |
| 16.   | [What is the difference between `React.StrictMode` in development vs production?](#question-16-what-is-the-difference-between-reactstrictmode-in-development-vs-production)              |
| 17.   | [How do you use comments inside JSX?](#question-17-how-do-you-use-comments-inside-jsx)                                                                                                   |
| 18.   | [How do you render an array of elements with dynamic content?](#question-18-how-do-you-render-an-array-of-elements-with-dynamic-content)                                                 |
| 19.   | [What is the difference between class component state and functional component state?](#question-19-what-is-the-difference-between-class-component-state-and-functional-component-state) |

## Question 1. What is the difference between React and React Native?

# What is the difference between React and React Native?

## Short answer

**React** is a JavaScript library for building **web applications** that render to the **DOM (HTML/CSS)**.

**React Native** uses the same React concepts (components, hooks, state, props) but builds **native mobile applications (iOS and Android)** by rendering to **native UI components**, not HTML.

---

# Explanation

Although React and React Native share the same programming model, they target different platforms and rendering engines.

| Feature    | React                            | React Native                       |
| ---------- | -------------------------------- | ---------------------------------- |
| Platform   | Web browsers                     | iOS & Android                      |
| Rendering  | HTML DOM                         | Native UI components               |
| Styling    | CSS, SCSS, Tailwind, CSS Modules | JavaScript StyleSheet / NativeWind |
| Navigation | React Router                     | React Navigation / Expo Router     |
| Components | div, span, button                | View, Text, Pressable              |
| Build Tool | Vite, Next.js                    | Expo, React Native CLI             |
| Animation  | CSS Animations, Framer Motion    | Reanimated, Animated API           |
| APIs       | Browser APIs                     | Native Device APIs                 |
| Deployment | Web Server/CDN                   | App Store / Play Store             |

---

## Architecture

### React (Web)

```
React Components
       │
Virtual DOM
       │
React DOM
       │
HTML + CSS
       │
Browser
```

Uses:

- HTML
- CSS
- Browser APIs
- DOM diffing

Example:

```jsx
<button>Click</button>
```

---

### React Native

```
React Components
       │
React Native Renderer
       │
Native UI Bridge / JSI
       │
UIKit (iOS)
Android Views
```

Uses:

- Native mobile controls
- Native device APIs
- JavaScript bridge / JSI
- No HTML

Example:

```tsx
<Button title="Click" onPress={handleClick} />
```

---

## Rendering behavior

### React

React updates the **Virtual DOM**, compares changes, and efficiently updates the browser DOM.

```
State changes
      ↓
Virtual DOM
      ↓
Diffing
      ↓
Real DOM updates
```

---

### React Native

There is **no DOM**.

React Native renders to native widgets.

```
State changes
      ↓
React Fiber
      ↓
Native Renderer
      ↓
UIKit / Android Views
```

Modern React Native uses the **New Architecture** (Fabric renderer and TurboModules with JSI) to reduce bridge overhead and improve rendering performance.

---

## React 18 Features

Both support:

- Concurrent rendering (where supported)
- Automatic batching
- Hooks
- Suspense (framework support varies)
- Context API
- React.memo
- useMemo
- useCallback

React Native adopts React's rendering improvements, though some web-specific features depend on the version of React Native and its renderer.

---

## State Management

Both can use:

- React Context
- Redux Toolkit
- Zustand
- MobX
- Jotai
- Recoil (community)

The state management approach is identical.

Example:

```
Redux Store

        ↑
React Web

        ↑
React Native
```

---

## Example

**Create a React Web project (Vite + TypeScript):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**React (Web):**

```tsx
import { useState } from "react";

export default function App() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount((c) => c + 1)}>Count: {count}</button>;
}
```

**React Native (TypeScript):**

```tsx
import { useState } from "react";
import { View, Text, Button } from "react-native";

export default function App() {
  const [count, setCount] = useState(0);

  return (
    <View style={{ padding: 20 }}>
      <Text>{count}</Text>
      <Button title="Increment" onPress={() => setCount((c) => c + 1)} />
    </View>
  );
}
```

Notice the differences:

- `button` → `Button`
- `div` → `View`
- `p/span` → `Text`
- CSS → JavaScript style objects

---

# Tooling & Setup

### React (Web)

Preferred stack:

- **Vite** for fast development and HMR.
- **Next.js** for SSR, Server Components, SEO, and full-stack features.
- **Remix** for data-driven routing.

Avoid **Create React App (CRA)** because it is deprecated.

### React Native

Preferred stack:

- **Expo** for the easiest development experience, OTA updates, and access to many native APIs.
- **React Native CLI** when custom native modules or advanced platform configuration is required.

### ESM vs CommonJS

- Modern React and React Native projects primarily use **ES Modules (ESM)**.
- Bundlers like Vite (web) and Metro (React Native) understand ESM and optimize builds.
- CommonJS is still encountered in some dependencies but is generally not the preferred module format for new applications.

---

# Performance

### React

- Use React Profiler to identify unnecessary re-renders.
- Use `React.memo` for expensive child components.
- Use `useMemo` for costly computations.
- Use `useCallback` for stable callback references.
- Split bundles with `React.lazy` and `Suspense`.
- Use caching libraries such as TanStack Query or SWR for efficient server-state management.

### React Native

- Avoid unnecessary re-renders of large lists.
- Use `FlatList` instead of mapping long arrays into `ScrollView`.
- Optimize images and animations.
- Prefer the New Architecture (Fabric/JSI) where supported.
- Use `React.memo`, `useMemo`, and `useCallback` similarly to web apps.

---

# Testing

### Unit & Integration

Use **Vitest** with **React Testing Library** for React web projects:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
render(<App />);
expect(screen.getByText(/Count/)).toBeInTheDocument();
```

### React Native

Use **Jest** together with **React Native Testing Library** to test components and interactions.

### E2E

- React → Playwright
- React Native → Detox

---

# Ops & Deployment

### React

- Deploy static assets to a CDN.
- Use SSR or static generation when SEO matters.
- Add Error Boundaries around major UI sections.
- Monitor with tools like Sentry.
- Analyze bundles with visualizers to reduce JavaScript size.

### React Native

- Release through the App Store and Google Play.
- Use Expo EAS or native CI/CD pipelines.
- Monitor crashes and performance with tools such as Sentry or Firebase Crashlytics.
- Optimize startup time, app size, and asset loading.

---

# Pitfalls

- **Don't expect HTML or CSS in React Native**—use components like `View`, `Text`, and `StyleSheet` instead.
- **Avoid assuming browser APIs exist in React Native** (for example, `document` or `window` APIs may not be available or behave differently).
- **Share business logic, not UI components**, between React and React Native unless using a cross-platform design system.

## Question 2. How do you include CSS in React components?

# How do you include CSS in React components?

## Short answer

React supports multiple ways to style components:

1. **External CSS files** (most common)
2. **CSS Modules** (scoped styles)
3. **Inline styles** (JavaScript objects)
4. **CSS-in-JS** (e.g., Styled Components, Emotion)
5. **Utility-first CSS frameworks** (e.g., Tailwind CSS)

For production React applications, **CSS Modules** or **Tailwind CSS** are common choices for component-level styling, while global styles are typically kept in a small set of shared CSS files.

---

# Explanation

React itself doesn't provide a styling system. Since JSX is JavaScript, you can import CSS files directly (with modern bundlers like Vite), apply inline styles, or use third-party styling libraries.

## 1. External CSS (Global)

Create a CSS file:

**Button.css**

```css
.button {
  background-color: royalblue;
  color: white;
  padding: 10px 16px;
  border: none;
  border-radius: 6px;
}
```

Import it into the component:

```tsx
import "./Button.css";

export default function Button() {
  return <button className="button">Click Me</button>;
}
```

**Pros**

- Easy to use
- Familiar CSS syntax
- Good for global styles

**Cons**

- Class name collisions
- Styles are globally scoped

---

## 2. CSS Modules (Recommended)

CSS Modules automatically generate unique class names, preventing collisions.

**Button.module.css**

```css
.button {
  background: green;
  color: white;
  padding: 10px;
}
```

Component:

```tsx
import styles from "./Button.module.css";

export default function Button() {
  return <button className={styles.button}>Save</button>;
}
```

Generated class:

```text
Button_button__3XQaP
```

**Advantages**

- Scoped styles
- No naming conflicts
- Better maintainability
- Great for large applications

---

## 3. Inline Styles

Styles are JavaScript objects.

```tsx
export default function Button() {
  return (
    <button
      style={{
        backgroundColor: "tomato",
        color: "white",
        padding: "10px",
      }}
    >
      Click
    </button>
  );
}
```

Notice:

- `background-color` → `backgroundColor`
- Values are strings or numbers
- No CSS file needed

Use inline styles for **dynamic values** rather than complete component styling.

---

## 4. CSS-in-JS

Example using Styled Components:

```tsx
import styled from "styled-components";

const Button = styled.button`
  background: royalblue;
  color: white;
  padding: 10px;
`;

export default function App() {
  return <Button>Click</Button>;
}
```

Useful for:

- Dynamic themes
- Design systems
- Reusable component libraries

Trade-offs include additional runtime (unless using compile-time solutions) and dependency overhead.

---

## 5. Tailwind CSS

```tsx
export default function Button() {
  return (
    <button className="rounded bg-blue-600 px-4 py-2 text-white">Click</button>
  );
}
```

Advantages:

- No custom CSS for many common styles
- Consistent design system
- Excellent developer experience
- Small production bundles through tree-shaking

---

## Rendering Behavior

Styling generally does **not** affect React's reconciliation process. However:

- Changing `className` or `style` causes React to update the rendered element.
- React 18's automatic batching groups state updates, reducing unnecessary re-renders regardless of the styling approach.
- Creating new inline style objects every render can cause memoized child components to re-render if those style objects are passed as props. Use `useMemo` when a stable reference matters.

---

# Example

**Create a Vite + React + TypeScript project:**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**Button.module.css**

```css
.button {
  background: #2563eb;
  color: white;
  padding: 0.75rem 1rem;
  border-radius: 6px;
  border: none;
}
```

**App.tsx**

```tsx
import styles from "./Button.module.css";

export default function App() {
  return <button className={styles.button}>Submit</button>;
}
```

---

# Tooling & Setup

- **Vite** is the recommended choice for client-side React projects because it provides fast startup, HMR, and first-class support for CSS, CSS Modules, and PostCSS.
- **Next.js** is a good choice when SSR, Server Components, routing, and SEO are required.
- **Remix** is another strong option for data-driven applications.
- Avoid **Create React App (CRA)** because it is deprecated.
- Modern React projects use **ES Modules (ESM)**. Bundlers such as Vite (powered by Rollup for production) handle CSS imports and code splitting seamlessly.

---

# Performance

- Use **React Profiler** to detect unnecessary re-renders.
- Use `React.memo` for components that receive stable props.
- Use `useMemo` for computed style objects passed to memoized children.
- Use `useCallback` for stable event handlers.
- Lazy-load large UI sections with `React.lazy` and `Suspense`.
- Minimize unused CSS by leveraging code splitting and tools like Tailwind's purge/content scanning or CSS Modules.

---

# Testing

For unit and integration testing with **Vitest** and **React Testing Library**:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders submit button", () => {
  render(<App />);
  expect(screen.getByRole("button", { name: /submit/i })).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Keep global styles minimal and organize component-specific styles with CSS Modules or a utility framework.
- Use Error Boundaries to isolate UI failures; styling errors should not break rendering.
- Optimize bundle size by removing unused CSS and splitting large feature bundles.
- Deploy static assets through a CDN for efficient caching and delivery.

---

# Pitfalls

- **Use `className`, not `class`**, because `class` is a reserved JavaScript keyword in JSX.
- **Avoid excessive inline styles** for static UI; they reduce reusability and cannot leverage CSS features like media queries or pseudo-classes directly.
- **Prefer CSS Modules or utility classes** over global CSS to prevent naming conflicts in large applications.

## Question 3. What is the difference between inline, internal, and external CSS in React?

# What is the difference between inline, internal, and external CSS in React?

## Short answer

In React:

- **Inline CSS** → Styles are written as JavaScript objects using the `style` prop.
- **Internal CSS** → Traditional `<style>` tags inside HTML are **generally not used** in React components. If needed, styles can be injected, but this is uncommon. React developers instead use CSS Modules or CSS-in-JS.
- **External CSS** → Styles are stored in separate `.css` files and imported into components. This is the most common approach.

---

# Explanation

Although the concepts of inline, internal, and external CSS come from standard HTML, React applications (especially those built with Vite or Next.js) typically rely on **external CSS**, **CSS Modules**, or **CSS-in-JS** rather than internal `<style>` blocks.

| Feature         | Inline CSS                              | Internal CSS       | External CSS                            |
| --------------- | --------------------------------------- | ------------------ | --------------------------------------- |
| Location        | `style` prop                            | `<style>` tag      | Separate `.css` file                    |
| Syntax          | JavaScript object                       | CSS                | CSS                                     |
| Reusability     | Low                                     | Medium             | High                                    |
| Dynamic styling | Excellent                               | Limited            | Limited (without CSS variables/classes) |
| Performance     | New objects each render unless memoized | Good               | Excellent                               |
| Best for        | Dynamic values                          | Small demos (rare) | Production apps                         |

---

## 1. Inline CSS

Styles are passed as a JavaScript object to the `style` prop.

```tsx
export default function Button() {
  return (
    <button
      style={{
        backgroundColor: "royalblue",
        color: "white",
        padding: "10px",
      }}
    >
      Save
    </button>
  );
}
```

### Characteristics

- Uses **camelCase** property names.
- Values are strings or numbers.
- Cannot directly use pseudo-classes (`:hover`) or media queries.
- Best for styles that change dynamically based on state or props.

---

## 2. Internal CSS

Traditional HTML uses:

```html
<style>
  .button {
    color: white;
  }
</style>
```

In React, placing `<style>` tags inside components is **not a recommended styling strategy** because:

- Styles are recreated if rendered repeatedly.
- Styles remain globally scoped unless managed carefully.
- It becomes difficult to maintain.

Example (possible but uncommon):

```tsx
export default function App() {
  return (
    <>
      <style>{`
        .title {
          color: blue;
        }
      `}</style>

      <h1 className="title">Hello React</h1>
    </>
  );
}
```

Use this sparingly, such as for embedding third-party snippets or dynamically generated CSS.

---

## 3. External CSS

Create a separate CSS file.

**App.css**

```css
.button {
  background: royalblue;
  color: white;
  padding: 10px;
}
```

Import it:

```tsx
import "./App.css";

export default function App() {
  return <button className="button">Submit</button>;
}
```

This is the standard approach for shared and global styles.

---

## CSS Modules (Recommended Alternative)

For large React applications, CSS Modules provide scoped styles.

**Button.module.css**

```css
.button {
  background: green;
}
```

```tsx
import styles from "./Button.module.css";

export default function Button() {
  return <button className={styles.button}>Save</button>;
}
```

Benefits:

- No class name collisions
- Easier maintenance
- Better scalability

---

## Rendering Behavior

- React treats `className` and `style` as props and updates the DOM only when they change.
- With React 18, automatic batching reduces unnecessary renders caused by multiple state updates.
- Passing a newly created inline style object to a memoized child component on every render can defeat `React.memo`; use `useMemo` if a stable object reference is important.

---

# Example

**Create a Vite + React + TypeScript project:**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**App.css**

```css
.container {
  padding: 20px;
}

.primary {
  background: #2563eb;
  color: white;
}
```

**App.tsx**

```tsx
import "./App.css";

export default function App() {
  return (
    <div className="container">
      <button className="primary" style={{ borderRadius: "8px" }}>
        Click Me
      </button>
    </div>
  );
}
```

This example combines external CSS for reusable styles with an inline style for a dynamic or one-off property.

---

# Tooling & Setup

- **Vite** is recommended for React projects due to fast development, HMR, and built-in support for CSS imports and CSS Modules.
- **Next.js** is preferred for applications requiring SSR, Server Components, routing, and SEO.
- Avoid **Create React App (CRA)** because it is deprecated.
- Modern React ecosystems use **ES Modules (ESM)**, and bundlers such as Vite handle CSS bundling, tree-shaking, and code splitting automatically.

---

# Performance

- Use **React Profiler** to identify unnecessary renders.
- Prefer CSS classes over large inline style objects for static styling.
- Memoize dynamic style objects with `useMemo` when passing them to memoized children.
- Use `React.memo`, `useCallback`, and `useMemo` appropriately.
- Split large feature bundles with `React.lazy` and `Suspense`.
- Remove unused CSS and leverage caching/CDNs for static assets.

---

# Testing

Install testing tools:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders button", () => {
  render(<App />);
  expect(screen.getByRole("button")).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Keep global CSS minimal and organize component-specific styles using CSS Modules or a utility framework.
- Use Error Boundaries to isolate rendering failures.
- Optimize CSS delivery with code splitting and CDN caching.
- Monitor bundle size using build analysis tools.

---

# Pitfalls

- **Use `className` instead of `class`** in JSX.
- **Avoid relying on `<style>` tags inside components** for application styling; prefer external CSS, CSS Modules, or CSS-in-JS.
- **Don't overuse inline styles** for static UI because they reduce reusability and don't support pseudo-classes or media queries directly.

## Question 4. How do you apply conditional rendering with ternary operators?

# How do you apply conditional rendering with ternary operators?

## Short answer

In React, the **ternary operator (`condition ? A : B`)** is used to render one of two UI outputs based on a condition.

```jsx
{
  isLoggedIn ? <Dashboard /> : <Login />;
}
```

It is the most common way to perform inline conditional rendering in JSX when you need an **either/or UI decision**.

---

# Explanation

Conditional rendering in React is just JavaScript expression evaluation inside JSX. Since JSX is compiled to `React.createElement`, any valid JS expression can be used inside `{}`.

The ternary operator is especially useful because it:

- Returns a value (so it works in JSX)
- Keeps logic inline and declarative
- Avoids verbose `if/else` blocks in render functions

---

## 1. Basic ternary rendering

```tsx id="r1a2b3"
export default function App() {
  const isLoggedIn = true;

  return (
    <div>{isLoggedIn ? <h1>Welcome back!</h1> : <h1>Please log in</h1>}</div>
  );
}
```

---

## 2. Conditional component rendering

```tsx id="c4d5e6"
function Dashboard() {
  return <h2>Dashboard</h2>;
}

function Login() {
  return <h2>Login Page</h2>;
}

export default function App() {
  const isLoggedIn = false;

  return <div>{isLoggedIn ? <Dashboard /> : <Login />}</div>;
}
```

---

## 3. Ternary with state (real-world pattern)

```tsx id="s7t8u9"
import { useState } from "react";

export default function App() {
  const [isLoading, setIsLoading] = useState(false);

  return <div>{isLoading ? <p>Loading...</p> : <p>Data loaded</p>}</div>;
}
```

---

## 4. Nested ternary (use carefully)

```tsx id="n1n2n3"
export default function App() {
  const status = "error";

  return (
    <div>
      {status === "loading" ? (
        <p>Loading...</p>
      ) : status === "error" ? (
        <p>Error occurred</p>
      ) : (
        <p>Success</p>
      )}
    </div>
  );
}
```

⚠️ Nested ternaries work but can hurt readability. In production, prefer:

- early returns
- switch statements
- mapping status to components

---

## 5. Conditional props or attributes

Ternaries are also used outside JSX elements:

```tsx id="p3q4r5"
<button disabled={isSubmitting ? true : false}>Submit</button>
```

Better version:

```tsx id="p3q4r5b"
<button disabled={isSubmitting}>Submit</button>
```

---

## 6. Ternary vs logical AND (`&&`)

| Use case            | Best approach |
| ------------------- | ------------- |
| Either A or B       | ternary       |
| Render only if true | `&&`          |

Example:

```tsx id="l8m9n0"
{
  isLoggedIn && <Dashboard />;
}
```

vs

```tsx id="l8m9n0b"
{
  isLoggedIn ? <Dashboard /> : <Login />;
}
```

---

# Rendering behavior (React internals)

- React evaluates JSX expressions during render phase.
- Ternary expressions are **pure JavaScript evaluation**, not React-specific logic.
- React 18’s **automatic batching** ensures multiple state updates triggering conditional UI changes are grouped efficiently.
- Conditional branches may mount/unmount components, triggering:
  - `useEffect` cleanup
  - state reset on unmount
  - re-initialization when remounted

This is important for performance and correctness (especially with forms, modals, or data fetching components).

---

# Example (React + TypeScript)

## Setup (Vite recommended)

```bash id="v1setup"
npm create vite@latest conditional-ui -- --template react-ts
cd conditional-ui
npm install
npm run dev
```

---

## Example: Auth-based UI switch

```tsx id="auth1"
import { useState } from "react";

function Dashboard() {
  return <h1>Dashboard</h1>;
}

function Login() {
  return <h1>Login Page</h1>;
}

export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  return (
    <div>
      {isLoggedIn ? <Dashboard /> : <Login />}

      <button onClick={() => setIsLoggedIn((v) => !v)}>Toggle Auth</button>
    </div>
  );
}
```

---

# Performance considerations

- Avoid deeply nested ternaries → reduce readability and maintainability issues.
- Extract conditional logic into variables or helper functions:

```tsx
const content = isLoading ? <Loader /> : <Data />;
return <div>{content}</div>;
```

- Use `React.memo` to prevent re-renders of conditional child components when props don't change.
- Use `useMemo` when expensive UI branches depend on computed values.
- Be aware that switching branches can unmount components, resetting internal state.

---

# Testing

With **Vitest + React Testing Library**:

```bash id="test1"
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("renders correct UI based on auth state", () => {
  render(<App />);
  expect(screen.getByText(/login/i)).toBeInTheDocument();
});
```

For E2E testing:

- Use **Playwright** to validate UI state transitions.

---

# Ops & deployment notes

- Conditional rendering can affect:
  - hydration in SSR (Next.js)
  - component remounting behavior
  - client/server mismatch if conditions depend on browser-only APIs

- Ensure SSR-safe conditions (avoid `window` checks during render unless guarded).
- Monitor conditional UI paths in production via logging/analytics.
- Use Error Boundaries around conditionally rendered feature blocks.

---

# Common pitfalls and best practices

- Avoid **nested ternaries** for complex UI logic—move to variables or mapping objects.
- Don’t misuse ternary when `&&` is clearer for single-condition rendering.
- Be aware that toggling conditions can **unmount components and reset state**.
- Keep JSX readable—prefer extracting logic when conditions grow.

## Question 5. How do you handle multiple class names dynamically?

# How do you handle multiple class names dynamically?

## Short answer

In React, multiple class names are handled dynamically using:

- Template literals
- Array join patterns
- Conditional expressions (ternary / logical AND)
- Utility libraries like **classnames** or **clsx** (recommended)

```tsx
<div className={`${isActive ? "active" : ""} btn primary`} />
```

For production apps, **clsx/classnames is the cleanest and most scalable approach**.

---

# Explanation

React uses `className` (not `class`) to assign CSS classes. When class logic becomes dynamic, you typically need to:

- Add/remove classes based on state
- Combine multiple static + conditional classes
- Avoid messy string concatenation

This becomes important in large-scale UI systems where styling depends on:

- component state
- props
- feature flags
- user roles
- API data

---

## 1. Template literals (basic approach)

```tsx id="t1"
export default function Button({ isActive }: { isActive: boolean }) {
  return (
    <button className={`btn ${isActive ? "btn-active" : "btn-inactive"}`}>
      Click
    </button>
  );
}
```

### Pros

- Simple
- No dependencies

### Cons

- Becomes unreadable with many conditions
- Hard to maintain at scale

---

## 2. Array join pattern (cleaner for multiple classes)

```tsx id="t2"
export default function Button({ isActive, isDisabled }: any) {
  return (
    <button
      className={["btn", isActive && "active", isDisabled && "disabled"]
        .filter(Boolean)
        .join(" ")}
    >
      Click
    </button>
  );
}
```

### Pros

- More scalable than templates
- Easy to add/remove conditions

### Cons

- Still verbose for large apps

---

## 3. Using `clsx` (recommended in production)

Install:

```bash id="c1"
npm install clsx
```

Usage:

```tsx id="c2"
import clsx from "clsx";

export default function Button({
  isActive,
  isDisabled,
}: {
  isActive: boolean;
  isDisabled: boolean;
}) {
  return (
    <button
      className={clsx("btn", {
        active: isActive,
        disabled: isDisabled,
      })}
    >
      Click
    </button>
  );
}
```

### Why this is preferred

- Clean syntax
- Handles complex conditions
- Widely used in production apps
- Tree-shakeable and lightweight

---

## 4. Using `classnames` (older but still common)

```tsx id="c3"
import classNames from "classnames";

classNames("btn", {
  active: true,
  disabled: false,
});
```

Functionally similar to `clsx`, but slightly heavier.

---

## 5. Conditional variants with state logic

```tsx id="c4"
export default function Card({
  status,
}: {
  status: "loading" | "error" | "success";
}) {
  return (
    <div
      className={clsx("card", {
        "card-loading": status === "loading",
        "card-error": status === "error",
        "card-success": status === "success",
      })}
    >
      {status}
    </div>
  );
}
```

This pattern is widely used in **design systems**.

---

## 6. CSS Modules + dynamic class names

```css id="c5"
.button {
  padding: 10px;
}
.active {
  background: green;
}
```

```tsx id="c6"
import styles from "./Button.module.css";
import clsx from "clsx";

export default function Button({ isActive }: any) {
  return (
    <button
      className={clsx(styles.button, {
        [styles.active]: isActive,
      })}
    >
      Click
    </button>
  );
}
```

### Key benefit

- Scoped styles + dynamic behavior (best of both worlds)

---

# Rendering behavior (React internals)

- `className` is just a **string prop applied to the DOM element**
- React reconciliation compares previous vs next `className` string
- Any change triggers a DOM update (not a full re-render necessarily)
- React 18 automatic batching ensures multiple state updates affecting classes are grouped efficiently

### Performance note

- Creating new string expressions every render is cheap
- But deeply nested logic in JSX can hurt readability and maintainability
- Memoization (`React.memo`) helps when class-dependent components are expensive

---

# Example (React + TypeScript + Vite)

## Setup

```bash id="setup1"
npm create vite@latest class-demo -- --template react-ts
cd class-demo
npm install
npm run dev
```

---

## Example: Toggle button with dynamic classes

```tsx id="ex1"
import { useState } from "react";
import clsx from "clsx";
import "./styles.css";

export default function App() {
  const [isActive, setIsActive] = useState(false);
  const [isDisabled, setIsDisabled] = useState(false);

  return (
    <div>
      <button
        className={clsx("btn", {
          active: isActive,
          disabled: isDisabled,
        })}
        onClick={() => setIsActive((v) => !v)}
        disabled={isDisabled}
      >
        Toggle Active
      </button>

      <button onClick={() => setIsDisabled((v) => !v)}>Toggle Disabled</button>
    </div>
  );
}
```

---

## styles.css

```css id="styles1"
.btn {
  padding: 10px 16px;
  border-radius: 6px;
  border: none;
}

.active {
  background: green;
  color: white;
}

.disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

---

# Tooling & Setup

- Use **Vite** for fast HMR and modern ESM-based builds
- Use **Next.js** when SSR or SEO is required
- Prefer **CSS Modules or Tailwind CSS** in large applications
- Use **clsx** for conditional class composition (standard in modern React stacks)
- Avoid CRA (Create React App is deprecated)

---

# Performance

- Avoid heavy inline string concatenation logic inside JSX for complex conditions
- Prefer extracting class logic:

```tsx
const buttonClass = clsx("btn", { active: isActive });
```

- Use `React.memo` for components that depend heavily on class props
- Use React DevTools Profiler to detect unnecessary re-renders
- Avoid recalculating large class logic inside deeply nested render trees

---

# Testing

Install:

```bash id="test1"
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx id="test2"
import { render, screen } from "@testing-library/react";
import App from "./App";

test("applies active class", () => {
  render(<App />);
  const btn = screen.getByRole("button");
  expect(btn.className).toContain("btn");
});
```

For E2E:

- Use **Playwright** to assert class changes on interactions

---

# Ops & Deployment

- Ensure class names are stable in production builds (especially with CSS Modules)
- Use PurgeCSS / Tailwind content scanning to remove unused styles
- Avoid runtime-heavy class computations in SSR (Next.js hydration consistency matters)
- Monitor bundle size if using large styling libraries

---

# Pitfalls

- ❌ Overusing template literals for complex logic → unreadable code
- ❌ Forgetting to filter falsy values in array-based class building
- ❌ Mixing global CSS and CSS Modules inconsistently
- ❌ Recomputing expensive class logic inside deeply nested renders

## Question 6. What is the difference between state and props immutability?

## Question 7. How do you combine multiple states into a single object in functional components?

## Question 8. What are `defaultProps` and when are they useful?

## Question 9. How do you pass props to nested child components?

## Question 10. How do you handle button click events in functional components?

## Question 11. How do you render HTML content from a string safely?

## Question 12. How do you stop event propagation in React?

## Question 13. How do you create a reusable button component?

## Question 14. How do you conditionally render multiple components in a parent component?

## Question 15. How do you handle component styling with CSS modules?

## Question 16. What is the difference between `React.StrictMode` in development vs production?

## Question 17. How do you use comments inside JSX?

## Question 18. How do you render an array of elements with dynamic content?

## Question 19. What is the difference between class component state and functional component state?
