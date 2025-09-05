# React Patterns and Best Practices

Essential patterns and practices for writing maintainable, efficient React applications.

> **Estimated reading time:** 12 min

## Overview

As React applications grow in complexity, certain patterns emerge as solutions to common problems. Understanding these patterns helps write more maintainable and scalable code. This guide covers essential React patterns including custom hooks, component composition, and state management strategies.

These patterns have evolved from real-world experience and community best practices, focusing on code reuse, separation of concerns, and performance optimization.

## Key Concepts

- **Custom Hooks**: Reusable stateful logic between components
- **Composition**: Building complex UIs from simple components
- **Render Props**: Sharing code between components using a prop whose value is a function
- **Higher-Order Components**: Functions that take a component and return a new component
- **Compound Components**: Components that work together to form a complete UI
- **Controlled vs Uncontrolled**: Patterns for form input handling
- **State Colocation**: Keeping state as close as possible to where it's used

## Examples

### 1. Custom Hook for Local Storage

```jsx
import { useState, useEffect } from "react";

// Custom hook for persistent state
function useLocalStorage(key, initialValue) {
  // State to store value
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  // Update localStorage when state changes
  useEffect(() => {
    try {
      window.localStorage.setItem(key, JSON.stringify(storedValue));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);

  return [storedValue, setStoredValue];
}

// Usage example
function App() {
  const [name, setName] = useLocalStorage("name", "");
  const [dark, setDark] = useLocalStorage("darkMode", false);

  return (
    <div style={{ background: dark ? "#333" : "#fff" }}>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter your name"
      />
      <button onClick={() => setDark((prev) => !prev)}>Toggle theme</button>
    </div>
  );
}
```

### 2. Prop Drilling Solution with Context

```jsx
import { createContext, useContext, useState } from "react";

// Create context
const UserContext = createContext();

// Provider component
function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const login = (userData) => setUser(userData);
  const logout = () => setUser(null);

  return (
    <UserContext.Provider value={{ user, login, logout }}>
      {children}
    </UserContext.Provider>
  );
}

// Custom hook for accessing context
function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error("useUser must be used within a UserProvider");
  }
  return context;
}

// Components
function App() {
  return (
    <UserProvider>
      <Header />
      <MainContent />
    </UserProvider>
  );
}

function Header() {
  const { user, logout } = useUser();
  return user ? (
    <header>
      Welcome, {user.name}!<button onClick={logout}>Logout</button>
    </header>
  ) : null;
}

function MainContent() {
  const { user, login } = useUser();

  return user ? (
    <div>Protected content</div>
  ) : (
    <button onClick={() => login({ name: "John" })}>Login</button>
  );
}
```

## Example Explanations

1. The `useLocalStorage` custom hook demonstrates encapsulating complex logic for persistent state management. It handles localStorage interactions, JSON parsing/stringifying, and error handling in a reusable way.

2. The Context example shows how to avoid prop drilling by creating a centralized state that can be accessed by any component in the tree. It also demonstrates proper context usage with a custom hook for better error handling and type safety.

## Common Pitfalls & Solutions

- **Prop Drilling**: Use Context or state management libraries for deeply nested state
- **Component Bloat**: Break down large components into smaller, focused pieces
- **Hook Dependencies**: Always include all dependencies in useEffect dependency arrays
- **Premature Optimization**: Don't optimize before measuring performance
- **Context Overuse**: Don't put everything in context; use local state when possible
- **Tight Coupling**: Use composition over inheritance for component relationships
- **Side Effect Management**: Keep side effects contained and cleanup properly

## Exercises

### Exercise 1: Custom Hook Creation

Task: Create a `useFetch` custom hook that handles loading, error states, and caching.
Expected result: A reusable hook that can be used to fetch data with automatic caching and loading/error state management.

### Exercise 2: Prop Drilling Refactor

Task: Take a deeply nested component tree using prop drilling and refactor it to use Context.
Expected result: A more maintainable component structure without passing props through intermediate components.

## Further Reading

- [React Patterns - Official Docs](https://reactjs.org/docs/design-principles.html)
- [React Hooks Patterns](https://usehooks.com/)
- [Kent C. Dodds Blog - Advanced React Patterns](https://kentcdodds.com/blog/advanced-react-patterns)
- [React Component Composition](https://reactjs.org/docs/composition-vs-inheritance.html)
