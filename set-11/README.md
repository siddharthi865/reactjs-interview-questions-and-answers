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

## Question 3. What is the difference between inline, internal, and external CSS in React?

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
