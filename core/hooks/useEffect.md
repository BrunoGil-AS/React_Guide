# 📌 What is `useEffect`?

- `useEffect` is a React hook that lets you **perform side effects** in a functional component.
- A _side effect_ means **anything that affects something outside React’s render cycle**.
  Examples: data fetching, subscriptions, timers, manipulating the DOM, logging.

---

## 📌 Why do we need it?

Without `useEffect`, components would only re-render UI. But real apps often need to:

- Fetch data from an API when the component loads
- Listen to window resize events or WebSocket updates
- Set up and clear timers (like `setInterval`)
- Manually manipulate DOM elements (rare but sometimes needed)

Before hooks (React 16.8), this was done in **class component lifecycle methods** (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`). `useEffect` unifies these into **one API for functional components**.

---

## 📌 How to use it

The syntax:

```jsx
import { useEffect, useState } from "react";

function Example() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    // This runs after the component renders
    console.log("Effect ran: count is", count);

    // Optional cleanup function
    return () => {
      console.log("Cleanup: before next effect or unmount");
    };
  }, [count]); // Dependency array
  // Effect runs when 'count' changes

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

---

## 📌 The 3 key modes of `useEffect`

### 1. **Run once on mount**

```jsx
useEffect(() => {
  console.log("Component mounted!");
}, []); // empty dependency array = only once
```

👉 Useful for fetching data when the component loads.

---

### 2. **Run when dependencies change**

```jsx
useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

👉 Runs every time `count` changes.

---

### 3. **Run on every render**

```jsx
useEffect(() => {
  console.log("Effect runs after every render");
});
```

👉 No dependency array = runs _after every render_. Rarely needed.

---

## 📌 Cleanup

If your effect creates something that needs **cleanup** (like event listeners, subscriptions, or timers), return a cleanup function:

```jsx
useEffect(() => {
  const id = setInterval(() => console.log("Tick"), 1000);

  return () => clearInterval(id); // cleanup
}, []);
```

This is like `componentWillUnmount`.

---

## 📌 Visual Aid

```plain
Render -> Commit -> useEffect runs -> [user interacts] -> Re-render -> Cleanup (old effect) -> New effect runs
```

---

## 📌 Common mistakes

- ❌ Forgetting dependencies → stale values or bugs.
- ❌ Adding too many dependencies → effect runs too often.
- ✅ Use ESLint plugin (`eslint-plugin-react-hooks`) to guide dependency arrays.

---

## 📌 React 18 behavior

In **React 18 Strict Mode (dev only)**, `useEffect` runs twice on mount to help you find side effect bugs.
👉 Don’t panic — it won’t happen in production.

---

✅ **In short**: `useEffect` lets you synchronize your component with the outside world (APIs, DOM, timers, subscriptions).

---
