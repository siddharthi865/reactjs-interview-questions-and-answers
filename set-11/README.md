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

## Question 5. How do you handle multiple class names dynamically?

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
