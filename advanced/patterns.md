# Advanced React Patterns & Best Practices

This guide covers advanced React patterns and best practices for building scalable, maintainable applications. It assumes familiarity with React basics (components, props, state, hooks).

## Best Practices Overview

### 1 — Component design & composition

**Definition (1–2 lines):**
Design components as small, focused, and reusable units. Favor composition over inheritance: build complex UIs by composing small components.

**When & why:**

- Use small components to make logic easier to test, reuse, and reason about.
- Clear separation between _presentational_ (UI-only) and _container_ (data + orchestration) makes maintenance and testing simpler.
- Imposes Single Responsibility Principle at the UI level.

**Code example — Presentational vs Container:**

```jsx
// Presentational: pure UI, receives data and callbacks via props
export function UserCard({ name, email, onSendMessage }) {
  return (
    <article className="user-card">
      <h3>{name}</h3>
      <p>{email}</p>
      {/* callback supplied by container */}
      <button onClick={() => onSendMessage(name)}>Message</button>
    </article>
  );
}

// Container: handles data fetching / state, passes props down
import { useEffect, useState } from "react";
export function UserCardContainer({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // side-effect: fetch user info when userId changes
    let canceled = false;
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((data) => {
        if (!canceled) setUser(data);
      });
    return () => {
      canceled = true;
    };
  }, [userId]);

  if (!user) return <div>Loading…</div>;

  // supply pure presentation with data + callbacks
  return (
    <UserCard
      name={user.name}
      email={user.email}
      onSendMessage={(name) => alert(`Message to ${name}`)}
    />
  );
}
```

**Visual:**
`App` → `UserList` → (`UserCardContainer` → `UserCard`)

**Pitfall:**: Avoid huge “God components” that hold UI + fetching + formatting responsibilities.

Reference: the “Thinking in React” pattern — break UI into hierarchy, find minimum state, lift state up. ([react.dev][2])

---

### 2 — State: local vs global vs server-state

**Definition:**

- _Local state_: component-level ephemeral state (`useState`, `useReducer`).
- _Global state_: app-scoped state (Context, Redux, Zustand).
- _Server-state_: remote data that must be cached/synchronized (use TanStack Query / React Query / SWR).

**When & why:**

- Use local state for UI toggles, form inputs, ephemeral UI.
- Use Context + `useReducer` for grouped app state (auth, theme) when you’d otherwise prop-drill.
- Use a server-state library for caching, deduping, automatic refetching and background updates — it drastically reduces boilerplate for async data.

**Code example — useReducer + Context (global-ish app state):**

```jsx
// authContext.js
import React, { createContext, useContext, useReducer } from "react";

// define actions and state shape
const initialState = { user: null, token: null };

function reducer(state, action) {
  switch (action.type) {
    case "LOGIN":
      return { ...state, user: action.user, token: action.token };
    case "LOGOUT":
      return initialState;
    default:
      throw new Error(`Unknown action ${action.type}`);
  }
}

const AuthStateContext = createContext();
const AuthDispatchContext = createContext();

export function AuthProvider({ children }) {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <AuthStateContext.Provider value={state}>
      <AuthDispatchContext.Provider value={dispatch}>
        {children}
      </AuthDispatchContext.Provider>
    </AuthStateContext.Provider>
  );
}

// hooks for convenient consumption
export function useAuthState() {
  return useContext(AuthStateContext);
}
export function useAuthDispatch() {
  return useContext(AuthDispatchContext);
}
```

**Where to apply:**: Use this for app-level concerns (current user, theme, selected tenant). Don’t put frequently-changing UI counters into global state — local state is better.

**Pitfall:**: Avoid overusing Context for very high-frequency updates (will re-render many consumers). Use selectors/memoization or split contexts.

For server-state, prefer a dedicated library (caching, invalidation, retries) — see TanStack Query docs. ([TanStack][3])

---

### 3 — Hooks: rules & custom hooks

**Definition:**
Hooks are the primary API for state + lifecycle in functional components. Follow the Rules of Hooks: call them only at top-level and from React functions. ([react.dev][4])

**When & why:**

- Use built-in hooks (`useState`, `useEffect`, `useRef`, `useReducer`, `useMemo`, `useCallback`) for local logic.
- Create _custom hooks_ to extract and reuse logic across components.

**Code example — custom hook: `useDebouncedValue`:**

```jsx
import { useEffect, useState } from "react";

/:**
 * useDebouncedValue - returns `value` but debounced by `delay` ms.
 * Good for search inputs to avoid rapid requests.
 */
export function useDebouncedValue(value, delay = 300) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    // set a timer whenever `value` changes
    const id = setTimeout(() => setDebounced(value), delay);
    // cleanup cancels pending timer if value changes before delay
    return () => clearTimeout(id);
  }, [value, delay]); // include all dependencies

  return debounced;
}
```

**Best practices for hooks:**

- Name custom hooks `useXxx`.
- Keep them focused (one responsibility).
- Ensure dependency arrays in `useEffect` are complete — prefer lint rules (`eslint-plugin-react-hooks`) to catch mistakes.

**Pitfall:**: Avoid recreating stateful logic as HOCs if a hook is a cleaner solution. Hooks must be pure in render (no side-effects inside render).

---

### 4 — Data fetching patterns (vanilla vs library)

**Definition:**
Two common approaches: fetch inside `useEffect` (manual) or use a data-fetching library (TanStack Query) that manages caching, retries, background refetch, invalidation.

**When & why:**

- Small apps / simple endpoints: `useEffect` + `fetch` may be fine.
- Medium+ apps with many endpoints: use TanStack Query (React Query) — it handles caching, background refresh, optimistic updates, SSR hydration. ([TanStack][3])

**Code example — manual fetch in useEffect:**

```jsx
import { useEffect, useState } from "react";

export function Todos({ userId }) {
  const [todos, setTodos] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let mounted = true;
    setLoading(true);
    fetch(`/api/users/${userId}/todos`)
      .then((res) => {
        if (!res.ok) throw new Error("Network response was not ok");
        return res.json();
      })
      .then((data) => {
        if (mounted) setTodos(data);
      })
      .catch((err) => {
        if (mounted) setError(err);
      })
      .finally(() => {
        if (mounted) setLoading(false);
      });

    return () => {
      mounted = false;
    }; // cancel state updates after unmount
  }, [userId]);

  if (loading) return <div>Loading…</div>;
  if (error) return <div>Error: {String(error)}</div>;
  return (
    <ul>
      {todos.map((t) => (
        <li key={t.id}>{t.title}</li>
      ))}
    </ul>
  );
}
```

**Code example — using TanStack Query:**

```jsx
// Installation: npm i @tanstack/react-query
import {
  useQuery,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";

const queryClient = new QueryClient();

function Todos({ userId }) {
  // useQuery takes a unique key and an async function that returns data
  const { data, error, isLoading } = useQuery(
    ["todos", userId],
    async () => {
      const res = await fetch(`/api/users/${userId}/todos`);
      if (!res.ok) throw new Error("Network error");
      return res.json();
    },
    {
      staleTime: 60_000, // example: data is fresh for 60s
      retry: 1, // retry failed requests once
    }
  );

  if (isLoading) return <div>Loading…</div>;
  if (error) return <div>Error: {String(error)}</div>;
  return (
    <ul>
      {data.map((t) => (
        <li key={t.id}>{t.title}</li>
      ))}
    </ul>
  );
}

// App root
export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Todos userId="123" />
    </QueryClientProvider>
  );
}
```

**Pitfall:**: Don’t roll your own cache/coherency if your app benefits from deduping, background refresh, pagination — use a battle-tested library.

---

### 5 — Performance patterns & React 18 concurrency

**Definition:**
Optimize re-rendering and responsiveness using memoization, virtualization, code-splitting, and React 18’s concurrent features (`startTransition`, `useDeferredValue`).

**When & why:**

- Use memoization (`React.memo`, `useMemo`, `useCallback`) to avoid expensive recomputations or child re-renders when props/state don’t change.
- Use virtualization for long lists (react-window / react-virtual).
- Use `startTransition` / `useTransition` to mark non-urgent updates so UI stays responsive during heavy updates (React 18 feature). ([react.dev][1])

**Code example — startTransition for search:**

```jsx
import { useState, startTransition } from "react";

function BigList({ items }) {
  // assume items is a huge array
  return (
    <>
      {items.map((i) => (
        <div key={i.id}>{i.name}</div>
      ))}
    </>
  );
}

export function Search({ allItems }) {
  const [query, setQuery] = useState("");
  const [filtered, setFiltered] = useState(allItems);

  function onChange(e) {
    const q = e.target.value;
    setQuery(q); // urgent update (input value)
    // non-urgent: expensive filtering — mark as transition
    startTransition(() => {
      setFiltered(allItems.filter((item) => item.name.includes(q)));
    });
  }

  return (
    <>
      <input value={query} onChange={onChange} />
      <BigList items={filtered} />
    </>
  );
}
```

**Tip:**: Prefer `useDeferredValue` when you want to delay updating a derived value while keeping the input responsive.

**Pitfall:**: Overusing `useMemo`/`useCallback` can add complexity without real benefit; only optimize where you measured issues.

---

### 6 — Routing, code splitting & lazy loading

**Definition:**
Use a router (React Router v6 is current mainstream) for client-side navigation. Use `React.lazy` + `Suspense` for route-level code-splitting.

**When & why:**

- For multi-page SPAs with URL-based navigation use a router. React Router v6 improved routes API and replaced many v5 patterns — follow v6 docs when migrating. ([reactrouter.com][5])
- Lazy loading reduces initial bundle size for faster first paint.

**Code example — React Router v6 + lazy:**

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";
import React, { Suspense, lazy } from "react";

const Home = lazy(() => import("./pages/Home"));
const Users = lazy(() => import("./pages/Users"));
const NotFound = () => <div>Not found</div>;

export default function AppRouter() {
  return (
    <BrowserRouter>
      {/* Suspense shows fallback while lazy chunk downloads */}
      <Suspense fallback={<div>Loading page...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/users" element={<Users />} />
          <Route path="*" element={<NotFound />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

**Pitfall:**: When server-rendering, ensure hydration and Suspense usage is supported by your SSR framework (Next.js / Remix patterns differ).

---

### 7 — Error boundaries & graceful degradation

**Definition:**
Error boundaries catch rendering errors in components and allow you to show fallback UIs instead of a broken app. They must be class components or use libraries that provide a wrapper.

**When & why:**

- Wrap large portions of your UI (route-level) with an ErrorBoundary to keep the rest of the app functioning when one subtree errors.

**Code example — class-based ErrorBoundary:**

```jsx
import React from "react";

export class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }
  componentDidCatch(error, info) {
    // log error to monitoring service
    console.error(error, info);
  }

  render() {
    if (this.state.hasError) {
      return <div>Something went wrong. Try refreshing the page.</div>;
    }
    return this.props.children;
  }
}
```

**Tip:**: For hook-style usage, consider `react-error-boundary` library which provides hooks + components.

---

### 8 — TypeScript + React (recommended)

**Definition:**
Use TypeScript to add static types to components and hooks — improves DX and catches many bugs at compile time.

**When & why:**

- Use TS in projects of any medium size — it helps collaboration and refactoring. For small prototypes you may skip it.

**Code example — typing props and refs:**

```tsx
// Button.tsx (TypeScript)
import React from "react";

type ButtonProps = {
  children: React.ReactNode;
  onClick?: (e: React.MouseEvent<HTMLButtonElement>) => void;
  primary?: boolean;
};

// prefer function components with explicit prop types
export function Button({ children, onClick, primary = false }: ButtonProps) {
  return (
    <button
      aria-pressed={primary}
      onClick={onClick}
      className={primary ? "btn-primary" : "btn"}
    >
      {children}
    </button>
  );
}
```

**Tip:**: Avoid `React.FC` if you want to avoid implicit `children` and prefer explicit typing. (This is opinionated but common in modern codebases.)

---

### 9 — Testing: unit, integration, E2E

**Definition:**

- Unit/integration: Jest + React Testing Library (RTL).
- E2E: Playwright or Cypress.

**When & why:**

- RTL encourages testing behavior (what user sees/does) rather than implementation details. Use E2E for critical flows (login, checkout).

**Code example — simple RTL test:**

```jsx
// Counter.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "./Counter";

test("increments on click", () => {
  render(<Counter initial={0} />);
  const btn = screen.getByRole("button", { name: /increment/i });
  fireEvent.click(btn);
  expect(screen.getByText("Count: 1")).toBeInTheDocument();
});
```

**Pitfall:**: Avoid testing implementation details (internal state) — assert on DOM and behavior.

---

### 10 — Styling & theming

**Definition:**
Pick a styling approach that fits team needs: CSS Modules, Tailwind CSS, CSS-in-JS (styled-components / emotion). Keep style logic separate from business logic.

**When & why:**

- CSS Modules or Tailwind for maintainability and predictable class names.
- Styled-components for dynamic styling coupled to components.

**Code example — CSS Module:**

```css
/* Button.module.css */
.btn {
  padding: 8px 12px;
  border-radius: 6px;
}
.primary {
  background: #0b6;
  color: white;
}
```

```jsx
import styles from "./Button.module.css";
export function Button({ children }) {
  return (
    <button className={`${styles.btn} ${styles.primary}`}>{children}</button>
  );
}
```

**Tip:**: Avoid huge inline style objects unless dynamic performance or theming demands it.

---

### 11 — Tooling & workflow (linting, formatting, CI)

**Recommendations:**

- Use Vite for fast dev builds (React 18 friendly), or Next.js when SSR/SSG is needed.
- Linting: `eslint` + `eslint-plugin-react-hooks`.
- Formatting: `prettier`.
- Pre-commit hooks: `husky` + `lint-staged`.
- Component library documentation: Storybook for visual testing and docs.

(These are stable industry practices; pick what fits team constraints.)

---

### 12 — Folder structure (recommended starter)

```plain
src/
  index.tsx          // app bootstrap
  App.tsx
  pages/             // route pages (for router/next)
    Home.tsx
    Users/
      UsersPage.tsx
  components/        // reusable components (small, focused)
    Button/
      Button.tsx
      Button.module.css
      Button.test.tsx
      index.ts
  hooks/              // custom hooks (useAuth, useDebounce)
  services/           // API clients, adapters
    api.ts
  features/           // feature-scoped folders for medium apps
    todos/
      TodoList.tsx
      todo-api.ts
  types/              // TS types & interfaces
  utils/              // small helpers
  assets/
```

**Why:**: Feature folders scale well — group all files related to one feature together.

---

### 13 — Common pitfalls & checklist (for PRs)

Quick checklist you can add to PR template:

- [ ] Does the component have a single responsibility?
- [ ] Are hooks used correctly (no conditional hook calls)?
- [ ] Are dependency arrays complete in `useEffect`?
- [ ] Are heavy computations memoized only if measured?
- [ ] Are keys stable for list rendering?
- [ ] Accessibility checks: semantic elements, labels, keyboard focusable.
- [ ] Tests added for new behavior (unit/integration).
- [ ] Linting & formatting passes.
- [ ] No sensitive data in client bundles (API keys, secrets).

---

### 14 — Version-specific notes & migration tips

- :**React 18:**: automatic batching, `startTransition` / `useTransition`, improved Suspense + streaming SSR. Use these to improve responsiveness & server rendering. ([react.dev][1])
- :**React Router v6:**: new `Routes` / `Route` element API and improvements vs v5; check migration docs if upgrading. ([reactrouter.com][5])
- :**Server-state libraries:**: TanStack Query (React Query) is the widely recommended approach for caching/invalidating remote data. ([TanStack][3])

---

### Sources (high-level)

- React 18 release notes (automatic batching, startTransition, SSR + Suspense). ([react.dev][1])
- Thinking in React (component hierarchy, where state should live). ([react.dev][2])
- Rules of React (hooks rules and constraints). ([react.dev][4])
- TanStack Query (React Query) docs — recommended server-state patterns. ([TanStack][3])
- React Router docs (v6 guidance & migration notes). ([reactrouter.com][5])

---

[1]: https://react.dev/blog/2022/03/29/react-v18?utm_source=chatgpt.com "React v18.0"
[2]: https://react.dev/learn/thinking-in-react?utm_source=chatgpt.com "Thinking in React"
[3]: https://tanstack.com/query/v5/docs/react/guides/queries?utm_source=chatgpt.com "TanStack Query React Docs"
[4]: https://react.dev/reference/rules?utm_source=chatgpt.com "Rules of React"
[5]: https://reactrouter.com/6.30.1?utm_source=chatgpt.com "Docs v6.30.1"

Got it — let’s explore **React Patterns**.
Since you already know JavaScript and are learning React at an intermediate level, I’ll treat this as a structured _guide to patterns you’ll actually use in production_.

---

## Patterns Overview

### 🎯 What Are React Patterns?

**Definition:** Patterns in React are _reusable solutions_ for common problems in component design, state management, and data flow.
They don’t add new features; they’re conventions and approaches that make apps **scalable, readable, and maintainable**.

---

### 📚 Core React Patterns

I’ll go step by step, one at a time, so you can tell me if you want to go deeper in any of them:

#### 1. **Container & Presentational Components**

- **When/Why:** Separates **logic (data fetching, state)** from **UI rendering**. Keeps components clean and focused.
- **Structure:**

  - Container → handles API calls, state, Redux, etc.
  - Presentational → receives props, only displays UI.

- **Example:**

```jsx
// Presentational (UI only)
function UserList({ users }) {
  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}

// Container (logic + data fetching)
function UserListContainer() {
  const [users, setUsers] = React.useState([]);

  React.useEffect(() => {
    fetch("/api/users")
      .then((res) => res.json())
      .then(setUsers);
  }, []);

  return <UserList users={users} />;
}
```

---

#### 2. **Higher-Order Components (HOC)**

- **When/Why:** For reusing **logic across components** before hooks existed. Still used in some libraries (like React Router, Redux).
- **Definition:** A function that takes a component and returns a new component with added props/behavior.
- **Example:**

```jsx
function withLogger(WrappedComponent) {
  return function (props) {
    console.log("Props:", props);
    return <WrappedComponent {...props} />;
  };
}

const UserWithLogger = withLogger(UserList);
```

> (Hooks replaced many HOC use cases, but you’ll still see them in codebases.)

---

#### 3. **Render Props**

- **When/Why:** Share reusable logic between components by **passing a function as a child**.
- **Example:**

```jsx
function DataFetcher({ url, children }) {
  const [data, setData] = React.useState(null);

  React.useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then(setData);
  }, [url]);

  return children(data); // children is a function
}

// Usage
<DataFetcher url="/api/users">
  {(data) => (data ? <UserList users={data} /> : <p>Loading...</p>)}
</DataFetcher>;
```

---

#### 4. **Compound Components**

- **When/Why:** Create a set of components that work together naturally (great for UI libraries).
- **Example:**

```jsx
function Modal({ children }) {
  return <div className="modal">{children}</div>;
}

Modal.Header = function ({ children }) {
  return <h2>{children}</h2>;
};

Modal.Body = function ({ children }) {
  return <div>{children}</div>;
};

Modal.Footer = function ({ children }) {
  return <footer>{children}</footer>;
};

// Usage
<Modal>
  <Modal.Header>Confirm</Modal.Header>
  <Modal.Body>Are you sure?</Modal.Body>
  <Modal.Footer>
    <button>Cancel</button>
    <button>OK</button>
  </Modal.Footer>
</Modal>;
```

---

#### 5. **Controlled vs Uncontrolled Components**

- **When/Why:**

  - _Controlled:_ Form state lives in React (easier validation, predictable).
  - _Uncontrolled:_ DOM manages the state (simpler, less re-rendering).

- **Example (Controlled):**

```jsx
function NameInput() {
  const [value, setValue] = React.useState("");
  return <input value={value} onChange={(e) => setValue(e.target.value)} />;
}
```

---

#### 6. **Custom Hooks**

- **When/Why:** Reuse logic (state + effects) across components without HOC or render props.
- **Example:**

```jsx
function useFetch(url) {
  const [data, setData] = React.useState(null);

  React.useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then(setData);
  }, [url]);

  return data;
}

// Usage
function UserListContainer() {
  const users = useFetch("/api/users");
  return users ? <UserList users={users} /> : <p>Loading...</p>;
}
```

---

#### 7. **Context + Provider Pattern**

- **When/Why:** Avoids **prop drilling** for global/shared state (e.g., theme, auth).
- **Example:**

```jsx
const ThemeContext = React.createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = React.useState("light");
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function Button() {
  const { theme } = React.useContext(ThemeContext);
  return <button className={theme}>Click me</button>;
}
```

---

#### 8. **Error Boundary Pattern**

- **When/Why:** Catch JavaScript errors in React components and display a fallback UI.
- **Note:** Only works in **class components** (as of React 18).
- **Example:**

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError() {
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) return <h1>Something went wrong.</h1>;
    return this.props.children;
  }
}
```

---

✅ These 8 are the **core patterns** you’ll run into most often in real-world React apps.

---
