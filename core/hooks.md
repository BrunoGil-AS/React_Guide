# React Hooks Fundamentals

Understanding and effectively using React's core hooks (`useState`, `useEffect`, `useContext`) to manage state, side effects, and context in functional components.

## Overview

React Hooks enable functional components to use state and other React features without writing class components. They provide a more direct way to handle component logic, side effects, and state management. Hooks must be called at the top level of your component and cannot be conditional or placed inside loops.

Since React 16.8, hooks have become the standard way to write React components, offering better code organization, reusability, and avoiding the complexity of lifecycle methods and `this` binding found in class components.

## Key Concepts

- **useState**: Adds state management to functional components. Returns a stateful value and a setter function.
- **useEffect**: Handles side effects in components (data fetching, subscriptions, DOM mutations).
- **useContext**: Subscribes to React context without introducing nesting.
- **Dependencies**: Arrays passed to hooks that control when effects run or values update.
- **Cleanup Functions**: Optional returns from effects that prevent memory leaks and clean up resources.

## Examples

### 1. Counter with useState

```jsx
import { useState } from "react";

function Counter({ initialCount = 0 }) {
  // Using initializer function for computed initial state
  const [count, setCount] = useState(() => initialCount);

  // Functional updates ensure we work with latest state
  const increment = () => setCount((prev) => prev + 1);
  const decrement = () => setCount((prev) => prev - 1);

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={decrement}>-</button>
      <button onClick={increment}>+</button>
    </div>
  );
}
```

> The Counter example demonstrates `useState` with both an `initializer` function and functional updates. The `initializer` function is useful when the initial state is expensive to compute, while functional updates ensure we're always working with the latest state value, especially in rapid succession updates.

### 2. Data Fetching with useEffect

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Keep track of component mount state
    let mounted = true;

    async function fetchUser() {
      try {
        const response = await fetch(`/api/users/${userId}`);
        const data = await response.json();

        // Only update state if component is mounted
        if (mounted) {
          setUser(data);
          setLoading(false);
        }
      } catch (err) {
        if (mounted) {
          setError(err.message);
          setLoading(false);
        }
      }
    }

    fetchUser();

    // Cleanup function runs before effect reruns or unmount
    return () => {
      mounted = false;
    };
  }, [userId]); // Re-run when userId changes

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return null;

  return <div>Hello, {user.name}!</div>;
}
```

> The UserProfile example shows a complete data fetching flow using useEffect. It handles loading states, error boundaries, and proper cleanup to prevent memory leaks from setting state on unmounted components. The dependency array [userId] ensures the effect reruns when the userId prop changes.
>
> This example demonstrates a complete data fetching flow using `useEffect`:
>
> - Manages loading and error states
> - Cleans up subscriptions to prevent memory leaks
> - Uses a dependency array to control when the effect runs

### 3. Theme Context

```jsx
import { createContext, useContext, useState } from "react";

// Create context with default value
const ThemeContext = createContext("light");

// Provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme((current) => (current === "light" ? "dark" : "light"));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Consumer component
function ThemedButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button
      onClick={toggleTheme}
      style={{
        background: theme === "light" ? "#fff" : "#333",
        color: theme === "light" ? "#333" : "#fff",
      }}
    >
      Current theme: {theme}
    </button>
  );
}
```

> The Theme Context example illustrates the Provider/Consumer pattern with `useContext`. It shows how to share state across components without prop drilling, while maintaining type safety and providing an easy way to update shared state.

## Common Pitfalls & Solutions

- **Infinite Effect Loops**: Always include all dependencies in useEffect's dependency array. Use eslint-plugin-react-hooks to catch missing dependencies.
- **Stale Closures**: Use functional updates with useState when new state depends on previous state.
- **Missing Cleanup**: Always clean up subscriptions, timers, and abort controllers in useEffect returns.
- **Context Performance**: Don't put frequently changing values in context. Split context by data type/update frequency.
- **Dependency Arrays**: Don't lie to React about dependencies. Restructure the effect if needed.

## Exercises

1. **Debounced Input**

   - Task: Create an input field that only triggers an API call after the user stops typing for 500ms.
   - Expected Solution: Use useState for input value, useEffect with setTimeout for debounce, and cleanup to clear timeout.

2. **Global State Migration**
   - Task: Convert a deeply nested prop-drilling scenario to use Context.
   - Expected Solution: Create a context provider at the top level, move state and setters to context, consume with useContext in nested components.

## Further Reading

- [React Hooks Documentation](https://reactjs.org/docs/hooks-intro.html)
- [A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/) by Dan Abramov
- [Using the Effect Hook](https://beta.reactjs.org/learn/synchronizing-with-effects)
- [Hooks FAQ](https://reactjs.org/docs/hooks-faq.html)
