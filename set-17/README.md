# Set 17

| S.No. | Question                                                                                                                                                                 |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 1.    | [How do you toggle CSS classes dynamically in React?](#question-1-how-do-you-toggle-css-classes-dynamically-in-react)                                                    |
| 2.    | [How do you render a "loading" spinner while data is being fetched?](#question-2-how-do-you-render-a-loading-spinner-while-data-is-being-fetched)                        |
| 3.    | [How do you create a reusable input component?](#question-3-how-do-you-create-a-reusable-input-component)                                                                |
| 4.    | [How do you handle simple inline validation for an input field?](#question-4-how-do-you-handle-simple-inline-validation-for-an-input-field)                              |
| 5.    | [How do you implement a "show password" toggle?](#question-5-how-do-you-implement-a-show-password-toggle)                                                                |
| 6.    | [How do you display the length of a text input dynamically?](#question-6-how-do-you-display-the-length-of-a-text-input-dynamically)                                      |
| 7.    | [How do you clear input fields after submission?](#question-7-how-do-you-clear-input-fields-after-submission)                                                            |
| 8.    | [How do you implement simple pagination in React?](#question-8-how-do-you-implement-simple-pagination-in-react)                                                          |
| 9.    | [How do you conditionally render multiple elements inside a fragment?](#question-9-how-do-you-conditionally-render-multiple-elements-inside-a-fragment)                  |
| 10.   | [How do you use ReactDOM.hydrate for server-rendered content?](#question-10-how-do-you-use-reactdomhydrate-for-server-rendered-content)                                  |
| 11.   | [How do you handle focus and blur events in inputs?](#question-11-how-do-you-handle-focus-and-blur-events-in-inputs)                                                     |
| 12.   | [How do you update nested objects in state without mutating them?](#question-12-how-do-you-update-nested-objects-in-state-without-mutating-them)                         |
| 13.   | [How do you implement a simple rating component (e.g., stars)?](#question-13-how-do-you-implement-a-simple-rating-component-eg-stars)                                    |
| 14.   | [How do you add tooltips to buttons in React?](#question-14-how-do-you-add-tooltips-to-buttons-in-react)                                                                 |
| 15.   | [How do you prevent memory leaks in class components?](#question-15-how-do-you-prevent-memory-leaks-in-class-components)                                                 |
| 16.   | [How do you implement a countdown timer with hooks and cleanup?](#question-16-how-do-you-implement-a-countdown-timer-with-hooks-and-cleanup)                             |
| 17.   | [How do you implement a search filter that updates results as the user types?](#question-17-how-do-you-implement-a-search-filter-that-updates-results-as-the-user-types) |
| 18.   | [How do you implement dynamic class names based on multiple conditions?](#question-18-how-do-you-implement-dynamic-class-names-based-on-multiple-conditions)             |
| 19.   | [How do you handle API pagination in React?](#question-19-how-do-you-handle-api-pagination-in-react)                                                                     |
| 20.   | [How do you implement a collapsible sidebar menu?](#question-20-how-do-you-implement-a-collapsible-sidebar-menu)                                                         |

## Question 1. How do you toggle CSS classes dynamically in React?

# How do you toggle CSS classes dynamically in React?

## Short answer

In React, CSS classes are toggled dynamically by computing the `className` prop based on component state or props. The most common approaches are:

- Conditional (ternary) expressions
- Logical `&&` expressions
- Template literals
- Utility libraries like `clsx` or `classnames` (recommended for complex conditions)

React re-evaluates the `className` during each render whenever state or props change.

---

# Explanation

React does not manipulate the DOM classes directly like:

```js
element.classList.add("active");
```

Instead, React follows a **declarative approach**:

1. Store UI state.
2. Render classes based on that state.
3. React updates the DOM efficiently during reconciliation.

Example flow:

```
Button Click
      ↓
State changes
      ↓
Component re-renders
      ↓
className recalculated
      ↓
React updates DOM
```

This keeps UI predictable and avoids manual DOM manipulation.

### Common approaches

### 1. Ternary Operator (Most Common)

```jsx
className={isActive ? "active" : ""}
```

Suitable when toggling between two values.

---

### 2. Template Literals

Useful when combining fixed and conditional classes.

```jsx
className={`btn ${isActive ? "active" : ""}`}
```

Produces:

```
btn
```

or

```
btn active
```

---

### 3. Logical AND

Useful when only adding a class.

```jsx
className={`card ${selected && "selected"}`}
```

Although common, many teams prefer `clsx` because it avoids accidental `"false"` values in some patterns.

---

### 4. clsx (Recommended)

For multiple conditions, `clsx` keeps code clean.

```tsx
className={clsx(
  "btn",
  isPrimary && "btn-primary",
  disabled && "btn-disabled",
  loading && "loading"
)}
```

Much easier to maintain than nested ternaries.

---

## React Rendering Behavior (React 18)

When state changes:

```tsx
setIsActive(true);
```

React 18:

- Automatically batches multiple state updates.
- Schedules rendering efficiently with concurrent rendering capabilities.
- Recomputes `className` during render.
- Updates only the changed DOM attributes.

Example:

```tsx
setIsActive(true);
setLoading(false);
```

React performs **one render** instead of two (automatic batching).

---

## Component Architecture

For reusable components:

```tsx
<Button active disabled />
```

The component decides its own classes.

```tsx
function Button({ active, disabled }) {
  return (
    <button
      className={`btn ${active ? "active" : ""} ${disabled ? "disabled" : ""}`}
    />
  );
}
```

This keeps styling encapsulated and reusable.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

Install `clsx`:

```bash
npm install clsx
```

**`App.tsx`**

```tsx
import { useState } from "react";
import clsx from "clsx";
import "./App.css";

export default function App() {
  const [active, setActive] = useState(false);

  return (
    <div>
      <button
        className={clsx("btn", {
          active,
        })}
        onClick={() => setActive((prev) => !prev)}
      >
        {active ? "Active" : "Inactive"}
      </button>
    </div>
  );
}
```

**`App.css`**

```css
.btn {
  padding: 12px 20px;
  background: gray;
  color: white;
  border: none;
  cursor: pointer;
}

.active {
  background: royalblue;
}
```

Clicking the button toggles the `active` class without any direct DOM manipulation.

---

# Tooling & Setup

- **Use Vite** for React projects because it offers fast startup, instant hot module replacement (HMR), and native ES module development.
- Avoid **Create React App (CRA)** since it is deprecated.
- For SSR or hybrid rendering, prefer **Next.js App Router**; for nested routing and data loading, **Remix** is another strong option.
- Modern React tooling is ESM-first. Vite bundles with Rollup for production and serves native ES modules during development, resulting in fast rebuilds and efficient tree-shaking.

---

# Performance

Dynamic class toggling is inexpensive because React only updates the changed `className` attribute.

For larger applications:

- Use **React Profiler** to identify unnecessary re-renders.
- Memoize expensive child components with `React.memo`.
- Use `useCallback` when passing event handlers to memoized children.
- Use `useMemo` only for expensive class computations (rare).
- Split large routes/components using `React.lazy` and `Suspense`.
- Use caching libraries such as TanStack Query for server state to reduce unnecessary UI updates.

---

# Testing

For unit and integration testing, use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example test:

```tsx
import { render, screen, fireEvent } from "@testing-library/react";
import App from "./App";

test("toggles active class", () => {
  render(<App />);

  const button = screen.getByRole("button");

  fireEvent.click(button);

  expect(button).toHaveClass("active");
});
```

For end-to-end testing, use **Playwright**.

---

# Ops & Deployment

- Use **Error Boundaries** to isolate rendering failures.
- Log UI state transitions for debugging in development; integrate structured logging or monitoring in production.
- Choose SSR (e.g., Next.js) when SEO or faster first paint is important; CSR with Vite is ideal for many dashboards and internal apps.
- Minimize bundle size with code splitting, tree-shaking, and lazy loading.
- Deploy static Vite builds behind a CDN for efficient asset delivery.

---

# Pitfalls

- **Avoid manually changing classes with `element.classList`** in React components; it can conflict with React's virtual DOM.
- **Prefer `clsx` or `classnames`** over deeply nested ternary expressions for readability and maintainability.
- **Use functional state updates** (`setState(prev => !prev)`) when toggling booleans to avoid stale state issues.

## Question 2. How do you render a "loading" spinner while data is being fetched?

# How do you render a "loading" spinner while data is being fetched?

## Short answer

Render a loading spinner by maintaining a loading state (e.g., `isLoading`) and conditionally rendering either the spinner or the fetched content. Set `isLoading` to `true` before starting the request and `false` when it completes (or fails).

```tsx
return isLoading ? <Spinner /> : <UserList users={users} />;
```

---

# Explanation

In React, asynchronous data fetching typically involves three UI states:

1. **Loading** – Request is in progress.
2. **Success** – Data has been loaded.
3. **Error** – Request failed.

A common pattern is:

```text
Component mounts
       ↓
isLoading = true
       ↓
Fetch data
       ↓
Success → Render data
Failure → Render error
       ↓
isLoading = false
```

Instead of manually manipulating the DOM, React declaratively renders different UI based on state.

### Basic flow

```tsx
const [data, setData] = useState([]);
const [isLoading, setIsLoading] = useState(true);
const [error, setError] = useState<string | null>(null);
```

During fetch:

```tsx
setIsLoading(true);

try {
  const result = await fetch(...);
  setData(...);
} catch {
  setError("Something went wrong");
} finally {
  setIsLoading(false);
}
```

Using `finally` ensures the spinner disappears whether the request succeeds or fails.

---

## React 18 Rendering Behavior

With React 18:

- **Automatic batching** groups multiple state updates into a single render.
- Concurrent rendering keeps the UI responsive while updates are scheduled.
- The component re-renders automatically when `isLoading`, `data`, or `error` changes.

Example:

```tsx
setData(users);
setIsLoading(false);
```

These updates are batched into one render.

---

## Component Architecture

Separate loading UI into reusable components.

```tsx
<Page>
  <LoadingSpinner />
</Page>
```

instead of repeating:

```tsx
<div>Loading...</div>
```

throughout the application.

Example:

```tsx
function LoadingSpinner() {
  return <div className="spinner">Loading...</div>;
}
```

Large applications often standardize loading indicators for consistency.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { useEffect, useState } from "react";
import "./App.css";

type User = {
  id: number;
  name: string;
};

export default function App() {
  const [users, setUsers] = useState<User[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  const [error, setError] = useState("");

  useEffect(() => {
    async function fetchUsers() {
      try {
        setIsLoading(true);

        const response = await fetch(
          "https://jsonplaceholder.typicode.com/users",
        );

        if (!response.ok) {
          throw new Error("Failed to fetch users");
        }

        const data: User[] = await response.json();
        setUsers(data);
      } catch (err) {
        setError((err as Error).message);
      } finally {
        setIsLoading(false);
      }
    }

    fetchUsers();
  }, []);

  if (isLoading) {
    return <div className="spinner">Loading...</div>;
  }

  if (error) {
    return <p>{error}</p>;
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

**`App.css`**

```css
.spinner {
  font-size: 20px;
  padding: 20px;
  text-align: center;
}
```

This example displays a loading message while fetching data, then renders the user list or an error message.

---

# Tooling & Setup

- **Vite** is recommended for new React projects due to its fast dev server, HMR, and optimized production builds.
- Avoid **Create React App (CRA)** because it is deprecated.
- Use **Next.js App Router** when you need SSR, streaming, or Server Components.
- Vite is ESM-first, providing native module loading during development and Rollup-based production bundling.

---

# Performance

For production applications:

- Prefer **TanStack Query** or **SWR** over manual `useEffect` fetching. They provide built-in loading states, caching, retries, background refetching, and request deduplication.
- Use **React Profiler** to identify unnecessary re-renders.
- Memoize expensive child components with `React.memo`.
- Use `useCallback` for callbacks passed to memoized components.
- Lazy load routes/components with `React.lazy` and `Suspense`.
- Cache API responses to reduce repeated network requests.

Example with TanStack Query:

```tsx
const { data, isLoading, error } = useQuery({
  queryKey: ["users"],
  queryFn: fetchUsers,
});
```

---

# Testing

Use **Vitest** with **React Testing Library**.

Install:

```bash
npm install -D vitest @testing-library/react @testing-library/jest-dom
```

Example:

```tsx
import { render, screen } from "@testing-library/react";
import App from "./App";

test("shows loading spinner initially", () => {
  render(<App />);
  expect(screen.getByText(/loading/i)).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright** to verify loading indicators appear and disappear correctly during network requests.

---

# Ops & Deployment

- Handle loading, success, and error states consistently across the application.
- Use **Error Boundaries** for rendering errors (they do not catch async fetch errors, so handle those separately).
- For SSR frameworks like Next.js, use server-side data fetching where appropriate to reduce client-side loading states and improve SEO.
- Keep bundles small with code splitting and deploy static assets through a CDN.

---

# Pitfalls

- **Always reset `isLoading` in a `finally` block**, otherwise the spinner may never disappear after an error.
- **Don't render only a spinner indefinitely**; include proper error and empty-state UIs.
- **Avoid race conditions** by canceling or ignoring outdated requests when components unmount or when multiple fetches can overlap (e.g., using `AbortController`).

## Question 3. How do you create a reusable input component?

# How do you create a reusable input component?

## Short answer

Create a reusable input component by encapsulating common behavior (label, validation, error display, styling, accessibility, and event handling) into a single component that accepts props. In React, the component should be **controlled** by receiving its `value` and `onChange` from the parent, making it predictable and reusable across forms.

---

# Explanation

Instead of repeating the same input markup throughout your application:

```tsx
<label>Name</label>
<input type="text" />
<p className="error">Required</p>
```

create one reusable component:

```tsx
<Input label="Name" value={name} onChange={handleChange} error={errors.name} />
```

This provides several benefits:

- **Reusability** – Use the same component throughout the application.
- **Consistency** – Common styling and behavior are centralized.
- **Maintainability** – Update one component instead of dozens.
- **Accessibility** – Add labels, IDs, and ARIA attributes once.

### Controlled Component Pattern

A reusable input is usually **controlled**.

```text
User types
      ↓
onChange fires
      ↓
Parent state updates
      ↓
Input receives new value
      ↓
React re-renders
```

Example:

```tsx
const [name, setName] = useState("");

<Input value={name} onChange={(e) => setName(e.target.value)} />;
```

The input itself does not own the state.

---

## React 18 Rendering Behavior

When the user types:

```tsx
setName(e.target.value);
```

React 18:

- Automatically batches multiple state updates.
- Re-renders only components whose state/props changed.
- Efficiently updates only the changed DOM value.
- Uses concurrent rendering to keep typing responsive during larger UI updates.

A reusable input should avoid unnecessary internal state unless it is managing UI-only concerns (e.g., password visibility).

---

## Component Architecture

A reusable input should focus on a single responsibility.

```
Form
 ├── Input
 ├── Select
 ├── Checkbox
 ├── Radio
 └── TextArea
```

Each component handles its own rendering while the parent manages the form state.

Good reusable inputs usually support:

- label
- value
- onChange
- placeholder
- disabled
- required
- error message
- helper text
- different input types
- forwarded refs (for focus management)

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`Input.tsx`**

```tsx
import { ChangeEvent } from "react";

type InputProps = {
  label: string;
  value: string;
  type?: string;
  placeholder?: string;
  error?: string;
  onChange: (e: ChangeEvent<HTMLInputElement>) => void;
};

export default function Input({
  label,
  value,
  type = "text",
  placeholder,
  error,
  onChange,
}: InputProps) {
  const id = label.toLowerCase().replace(/\s+/g, "-");

  return (
    <div>
      <label htmlFor={id}>{label}</label>

      <input
        id={id}
        type={type}
        value={value}
        placeholder={placeholder}
        onChange={onChange}
        aria-invalid={!!error}
        aria-describedby={error ? `${id}-error` : undefined}
      />

      {error && (
        <p id={`${id}-error`} style={{ color: "red" }}>
          {error}
        </p>
      )}
    </div>
  );
}
```

**`App.tsx`**

```tsx
import { useState } from "react";
import Input from "./Input";

export default function App() {
  const [name, setName] = useState("");

  return (
    <Input
      label="Name"
      value={name}
      placeholder="Enter your name"
      onChange={(e) => setName(e.target.value)}
      error={name.length < 3 && name !== "" ? "Minimum 3 characters" : ""}
    />
  );
}
```

This component is reusable for any text input by changing the props.

> **Note:** For production libraries, consider using `useId()` instead of deriving IDs from the label to guarantee uniqueness.

---

# Tooling & Setup

- Prefer **Vite** for modern React development because of its fast startup, HMR, and optimized production builds.
- Avoid **Create React App (CRA)** since it is deprecated.
- Use **Next.js App Router** when building applications requiring SSR, Server Components, or SEO.
- Vite is **ESM-first**, serving native ES modules during development and using Rollup for optimized production bundles.

---

# Performance

Reusable inputs are lightweight, but forms can become expensive.

Optimize by:

- Using **React Profiler** to detect unnecessary re-renders.
- Wrapping heavy child components with `React.memo`.
- Using `useCallback` for stable event handlers when passing them to memoized children.
- Splitting large forms into smaller components.
- Using libraries like **React Hook Form** or **Formik** for complex forms to minimize re-renders and simplify validation.
- Lazy loading large form sections when appropriate.

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
import userEvent from "@testing-library/user-event";
import Input from "./Input";

test("updates input value", async () => {
  const user = userEvent.setup();
  const handleChange = vi.fn();

  render(<Input label="Name" value="" onChange={handleChange} />);

  await user.type(screen.getByLabelText("Name"), "John");

  expect(handleChange).toHaveBeenCalled();
});
```

For end-to-end testing, use **Playwright** to validate complete form interactions.

---

# Ops & Deployment

- Use **Error Boundaries** to catch rendering errors in the component tree (note: they do not catch async validation errors).
- Centralize logging for client-side form errors if needed.
- Keep reusable components framework-agnostic so they work in CSR (Vite) and SSR (Next.js).
- Optimize bundle size by exporting components individually and leveraging tree-shaking. Deploy static assets via a CDN for fast delivery.

---

# Pitfalls

- **Avoid mixing controlled and uncontrolled inputs** (`value` with `defaultValue`) in the same component.
- **Don't hardcode styles or validation logic**; pass them through props or compose with higher-level form components.
- **Remember accessibility** by associating labels with inputs (`htmlFor`/`id`) and exposing validation state with appropriate ARIA attributes.

## Question 4. How do you handle simple inline validation for an input field?

# How do you handle simple inline validation for an input field?

## Short answer

Handle inline validation by validating the input whenever its value changes (or when it loses focus), storing any validation message in state, and rendering the error message directly below the input. Keep the input as a **controlled component** so the UI always reflects the current validation state.

---

# Explanation

Inline validation provides immediate feedback as the user interacts with a form.

Typical validation flow:

```text
User types
      ↓
onChange fires
      ↓
Update input state
      ↓
Run validation
      ↓
Update error state
      ↓
React re-renders
```

For simple forms, validation is often handled inside the component using `useState`.

Example:

```tsx
const [email, setEmail] = useState("");
const [error, setError] = useState("");
```

Validate after updating the value:

```tsx
const handleChange = (value: string) => {
  setEmail(value);

  if (!value.includes("@")) {
    setError("Please enter a valid email.");
  } else {
    setError("");
  }
};
```

Then conditionally render the error:

```tsx
{
  error && <p className="error">{error}</p>;
}
```

### Validation Timing

Common strategies include:

- **On change** – Immediate feedback while typing.
- **On blur** – Validate when the user leaves the field.
- **On submit** – Validate the entire form before submission.

For a better user experience, many applications validate on blur first, then on change after the field has been touched.

---

## React 18 Rendering Behavior

Each call to `setState` schedules a re-render.

```tsx
setEmail(value);
setError(errorMessage);
```

In React 18:

- These updates are **automatically batched**, producing a single render.
- Only the affected parts of the DOM (input value and error message) are updated.
- Concurrent rendering helps keep typing responsive even in larger applications.

---

## Component Architecture

Keep validation logic close to the form for simple cases:

```text
LoginForm
 ├── EmailInput
 └── PasswordInput
```

For larger applications:

- Create reusable validation utilities.
- Share validation rules across forms.
- Use schema validation (e.g., Zod or Yup) with **React Hook Form** for complex forms.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`App.tsx`**

```tsx
import { ChangeEvent, useState } from "react";
import "./App.css";

export default function App() {
  const [username, setUsername] = useState("");
  const [error, setError] = useState("");

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;

    setUsername(value);

    if (value.trim().length < 3) {
      setError("Username must be at least 3 characters.");
    } else {
      setError("");
    }
  };

  return (
    <div>
      <label htmlFor="username">Username</label>

      <input
        id="username"
        value={username}
        onChange={handleChange}
        aria-invalid={!!error}
        aria-describedby={error ? "username-error" : undefined}
      />

      {error && (
        <p id="username-error" className="error">
          {error}
        </p>
      )}
    </div>
  );
}
```

**`App.css`**

```css
.error {
  color: red;
  font-size: 14px;
  margin-top: 4px;
}
```

This example validates the username while the user types and displays an inline error until the input contains at least three characters.

---

# Tooling & Setup

- Use **Vite** for modern React development because of its fast dev server, HMR, and optimized production builds.
- Avoid **Create React App (CRA)** since it is deprecated.
- For applications requiring SSR or Server Components, use **Next.js App Router**.
- Vite is **ESM-first**, serving native ES modules during development and bundling with Rollup for production.

---

# Performance

Simple validation is inexpensive, but large forms benefit from optimization:

- Use **React Profiler** to identify unnecessary re-renders.
- Wrap expensive child components with `React.memo`.
- Use `useCallback` when passing handlers to memoized children.
- Debounce expensive validations (e.g., server-side username availability checks).
- Use **React Hook Form** for large forms because it minimizes re-renders.
- Use **Zod** or **Yup** for reusable schema validation.

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

test("shows validation message for short username", async () => {
  const user = userEvent.setup();

  render(<App />);

  await user.type(screen.getByLabelText(/username/i), "ab");

  expect(screen.getByText(/at least 3 characters/i)).toBeInTheDocument();
});
```

For end-to-end testing, use **Playwright** to verify validation behavior during real user interactions.

---

# Ops & Deployment

- Display clear, accessible validation messages without blocking user input.
- Use **Error Boundaries** for rendering errors (they do not handle validation logic).
- Validate again on the server—client-side validation improves UX but should never be the only validation.
- Keep bundles small with code splitting and deploy static assets through a CDN.

---

# Pitfalls

- **Don't rely only on client-side validation**; always validate on the server for security and data integrity.
- **Avoid validating expensive rules on every keystroke**; debounce or validate on blur when appropriate.
- **Always associate error messages with inputs** using `aria-invalid` and `aria-describedby` for accessibility.

## Question 5. How do you implement a "show password" toggle?

# How do you implement a "show password" toggle?

## Short answer

Implement a **Show Password** toggle by storing a boolean state (e.g., `showPassword`) and switching the input's `type` between `"password"` and `"text"`.

```tsx
<input type={showPassword ? "text" : "password"} />
```

When the user clicks the toggle button, update the state:

```tsx
setShowPassword((prev) => !prev);
```

---

# Explanation

A password field normally hides user input:

```html
<input type="password" />
```

To allow users to view what they've typed, React conditionally changes the `type` attribute.

Flow:

```text
User clicks "Show"
        ↓
showPassword becomes true
        ↓
Component re-renders
        ↓
type="text"
        ↓
Password becomes visible
```

Clicking again:

```text
User clicks "Hide"
        ↓
showPassword becomes false
        ↓
type="password"
```

This is a perfect example of React's declarative rendering—React updates the DOM based on state instead of manually changing the element.

---

## React 18 Rendering Behavior

When the button is clicked:

```tsx
setShowPassword((prev) => !prev);
```

React 18:

- Automatically batches state updates.
- Re-renders only the affected component.
- Updates only the `type` attribute and button label in the DOM.
- Keeps the UI responsive through concurrent rendering.

Since this is a lightweight state update, performance impact is negligible.

---

## Component Architecture

Instead of embedding the toggle logic in every form, create a reusable password input component.

```text
LoginForm
 ├── PasswordInput
 └── SubmitButton
```

The reusable component can encapsulate:

- Password visibility toggle
- Label
- Error message
- Validation
- Accessibility
- Ref forwarding (optional)

This avoids duplicating logic across login, signup, and reset-password forms.

---

# Example

**Scaffold a modern React + TypeScript app with Vite (recommended):**

```bash
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
npm run dev
```

**`PasswordInput.tsx`**

```tsx
import { useState, ChangeEvent } from "react";

type PasswordInputProps = {
  value: string;
  onChange: (e: ChangeEvent<HTMLInputElement>) => void;
};

export default function PasswordInput({ value, onChange }: PasswordInputProps) {
  const [showPassword, setShowPassword] = useState(false);

  return (
    <div>
      <label htmlFor="password">Password</label>

      <div style={{ display: "flex", gap: "8px" }}>
        <input
          id="password"
          type={showPassword ? "text" : "password"}
          value={value}
          onChange={onChange}
        />

        <button
          type="button"
          onClick={() => setShowPassword((prev) => !prev)}
          aria-pressed={showPassword}
          aria-label={showPassword ? "Hide password" : "Show password"}
        >
          {showPassword ? "Hide" : "Show"}
        </button>
      </div>
    </div>
  );
}
```

**`App.tsx`**

```tsx
import { useState } from "react";
import PasswordInput from "./PasswordInput";

export default function App() {
  const [password, setPassword] = useState("");

  return (
    <PasswordInput
      value={password}
      onChange={(e) => setPassword(e.target.value)}
    />
  );
}
```

This implementation keeps the password field controlled by the parent while the visibility state is managed internally by the reusable component.

---

# Tooling & Setup

- Use **Vite** for modern React development because of its fast startup, Hot Module Replacement (HMR), and optimized production builds.
- Avoid **Create React App (CRA)** since it is deprecated.
- For applications requiring SSR, SEO, or Server Components, use **Next.js App Router**.
- Vite is **ESM-first**, serving native ES modules in development and bundling with Rollup for production.

---

# Performance

A password toggle is inexpensive, but general optimization practices still apply:

- Use **React Profiler** to confirm that only the password input re-renders.
- Wrap unrelated child components with `React.memo` if they're expensive to render.
- Use `useCallback` when passing handlers to memoized children.
- Lazy load large authentication pages if appropriate.
- Cache authentication-related API calls with libraries like TanStack Query where applicable (not for password visibility itself).

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
import PasswordInput from "./PasswordInput";

test("toggles password visibility", async () => {
  const user = userEvent.setup();

  render(<PasswordInput value="secret" onChange={() => {}} />);

  const input = screen.getByLabelText(/password/i);
  const button = screen.getByRole("button", { name: /show password/i });

  expect(input).toHaveAttribute("type", "password");

  await user.click(button);

  expect(input).toHaveAttribute("type", "text");
});
```

For end-to-end testing, use **Playwright** to verify the toggle behavior in a real browser.

---

# Ops & Deployment

- Ensure the toggle button uses `type="button"` to avoid accidentally submitting forms.
- Add accessible labels (`aria-label`, `aria-pressed`) so screen readers announce the current state.
- Remember that showing the password improves usability but also exposes it visually—let users choose.
- Keep authentication bundles small with code splitting and deploy assets via a CDN for faster loading.

---

# Pitfalls

- **Use `type="button"`** on the toggle button; otherwise, it may submit the form.
- **Store only the visibility state locally**; the actual password value should remain controlled by the parent form.
- **Don't mistake hiding the password for security**; changing the input type only affects the UI, not how the password is transmitted or stored.

## Question 6. How do you display the length of a text input dynamically?

## Question 7. How do you clear input fields after submission?

## Question 8. How do you implement simple pagination in React?

## Question 9. How do you conditionally render multiple elements inside a fragment?

## Question 10. How do you use ReactDOM.hydrate for server-rendered content?

## Question 11. How do you handle focus and blur events in inputs?

## Question 12. How do you update nested objects in state without mutating them?

## Question 13. How do you implement a simple rating component (e.g., stars)?

## Question 14. How do you add tooltips to buttons in React?

## Question 15. How do you prevent memory leaks in class components?

## Question 16. How do you implement a countdown timer with hooks and cleanup?

## Question 17. How do you implement a search filter that updates results as the user types?

## Question 18. How do you implement dynamic class names based on multiple conditions?

## Question 19. How do you handle API pagination in React?

## Question 20. How do you implement a collapsible sidebar menu?
