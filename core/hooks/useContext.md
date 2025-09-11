# 📌 What is `useContext`?

- `useContext` is a React hook that lets you **read and subscribe to context values**.
- **Context** is React’s built-in way to **avoid prop drilling** (passing props manually through many component layers).

Instead of passing data step by step, you put it in a **Context Provider**, and any child can directly access it.

---

## 📌 Why use it?

- When multiple components need the same global state (like **theme, authentication, language settings, user data, etc.**)
- To avoid deeply nested props:

Without context:

```jsx
<App theme="dark">
  <Layout theme="dark">
    <Header theme="dark" />
  </Layout>
</App>
```

With context:

```jsx
<App>
  <Layout>
    <Header /> {/* Header gets theme from context */}
  </Layout>
</App>
```

---

## 📌 How to use `useContext`

### Step 1: Create the context

```jsx
import { createContext } from "react";

export const ThemeContext = createContext("light");
// default value = "light"
```

---

### Step 2: Provide the context

```jsx
import { ThemeContext } from "./ThemeContext";

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Layout />
    </ThemeContext.Provider>
  );
}
```

---

### Step 3: Consume the context

```jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext";

function Header() {
  const theme = useContext(ThemeContext); // get value

  return <h1 style={{ color: theme === "dark" ? "white" : "black" }}>Hello</h1>;
}
```

---

## 📌 Visual Aid

```shell
ThemeContext.Provider (value = "dark")
│
├── Layout
│   └── Header -> useContext(ThemeContext) -> "dark"
```

---

## 📌 Combining with `useState`

Usually, you combine context with state so you can update values globally:

```jsx
import { createContext, useContext, useState } from "react";

const UserContext = createContext();

function App() {
  const [user, setUser] = useState(null);

  return (
    <UserContext.Provider value={{ user, setUser }}>
      <Login />
      <Profile />
    </UserContext.Provider>
  );
}

function Login() {
  const { setUser } = useContext(UserContext);
  return <button onClick={() => setUser("Bruno")}>Login</button>;
}

function Profile() {
  const { user } = useContext(UserContext);
  return <p>{user ? `Hello, ${user}` : "Please log in"}</p>;
}
```

---

## 📌 When to use Context vs other state managers

- ✅ Good for **global state that changes infrequently** (theme, auth, locale).
- ❌ Not great for **highly dynamic or large state** (like a shopping cart with 100 items) → consider **Redux, Zustand, Jotai, or Recoil**.

---

## 📌 React 18 Notes

No major API change. But in concurrent rendering, **context updates are automatically batched** (just like state updates).

---

✅ **In short**: `useContext` is your tool to share state globally without prop drilling.

---

## Further Reading

- [`Application Context`](../../advanced/application_context.md)
