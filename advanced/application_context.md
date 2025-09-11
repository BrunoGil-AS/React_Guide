# 📌 What is Application Context?

An **Application Context** is a **React Context designed to hold global state or functionality for your entire app**.

- It’s essentially a centralized place to store data or functions that **multiple components across different layers need to access**.
- Think of it as a **mini global store**, but lightweight and built-in, without needing Redux or other libraries.

---

## 📌 Why is it helpful?

1. **Avoids prop drilling**

   - Normally, if a parent component holds state, you’d pass it down through props through every child until the component that needs it:

     ```jsx
     <App>
       <Layout user={user} setUser={setUser}>
         <Header user={user} setUser={setUser} />
       </Layout>
     </App>
     ```

   - With context, you **provide the state once** and any nested component can consume it directly.

2. **Centralized state management for common data**

   - Auth info, theme, language, notifications, cart items, settings, etc.

3. **Cleaner, more maintainable code**

   - Components no longer need to “know” the full parent hierarchy to get the data.

---

## 📌 Implementation Step by Step

### Step 1: Create the App Context

```jsx
// AppContext.js
import { createContext } from "react";

export const AppContext = createContext(null);
```

---

### Step 2: Create a Context Provider Component

```jsx
// AppProvider.js
import { useState } from "react";
import { AppContext } from "./AppContext";

export function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState("light");

  const value = { user, setUser, theme, setTheme };

  return <AppContext.Provider value={value}>{children}</AppContext.Provider>;
}
```

---

### Step 3: Wrap your app with the Provider

```jsx
// index.js or App.js
import { AppProvider } from "./AppProvider";
import App from "./App";

const Root = () => (
  <AppProvider>
    <App />
  </AppProvider>
);

export default Root;
```

---

### Step 4: Consume the context in any component

```jsx
import { useContext } from "react";
import { AppContext } from "./AppContext";

function Header() {
  const { user, setUser, theme, setTheme } = useContext(AppContext);

  return (
    <header style={{ background: theme === "dark" ? "#333" : "#eee" }}>
      <p>{user ? `Hello, ${user}` : "Not logged in"}</p>
      <button onClick={() => setTheme(theme === "dark" ? "light" : "dark")}>
        Toggle Theme
      </button>
    </header>
  );
}
```

---

## 📌 Difference Between `useState + Props` vs `Context`

| Aspect         | `useState + props`                      | `Context (Application Context)`                               |
| -------------- | --------------------------------------- | ------------------------------------------------------------- |
| Scope          | Only parent → child                     | Global / any descendant                                       |
| Prop drilling  | Required (pass through multiple layers) | Not required                                                  |
| Ease of access | Each component needs props              | Any component can consume directly                            |
| Maintenance    | Harder with many layers/components      | Cleaner, centralized                                          |
| Performance    | Normal                                  | Context updates re-render consumers; can optimize with `memo` |

---

### Example: Prop drilling vs Context

**Prop drilling:**

```jsx
function App() {
  const [user, setUser] = useState("Bruno");
  return <Layout user={user} setUser={setUser} />;
}
function Layout({ user, setUser }) {
  return <Header user={user} setUser={setUser} />;
}
function Header({ user, setUser }) {
  return <p>{user}</p>;
}
```

**Using Context:**

```jsx
function Header() {
  const { user } = useContext(AppContext);
  return <p>{user}</p>;
}
```

✅ No need to pass `user` through `Layout` or any intermediate components.

---

## 📌 Best Practices

1. **Use context for global, shared state only**

   - Don’t put every small local state in context — keeps re-renders under control.

2. **Split contexts if needed**

   - One for Auth, one for Theme, one for Notifications — prevents unnecessary re-renders.

3. **Memoize provider values if complex**

```jsx
const value = useMemo(() => ({ user, setUser }), [user]);
```

---

✅ **In short:** Application Context is your built-in, lightweight global state system. It avoids prop drilling, centralizes important data, and keeps your components clean.

---
