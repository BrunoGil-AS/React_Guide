# TypeScript with React: Types, Generics, and Best Practices

A comprehensive guide to using TypeScript effectively in React applications.

> **Estimated reading time:** 12 min

## Overview

TypeScript adds static typing to JavaScript, providing better tooling, earlier error detection, and improved maintainability for React applications. This guide covers essential TypeScript patterns and best practices specific to React development.

Understanding how to properly type React components, hooks, and events is crucial for building robust applications. We'll explore common patterns and solutions for typical React + TypeScript scenarios.

## Key Concepts

- **Type Inference**: TypeScript's ability to deduce types
- **Interfaces vs Types**: Different ways to define types
- **Generics**: Type-safe containers and functions
- **Union Types**: Combining multiple type possibilities
- **Type Assertions**: Telling TypeScript about types it can't infer
- **Type Guards**: Runtime checks that narrow types
- **Utility Types**: Built-in type transformations

## Examples

### 1. Typed Components and Props

```tsx
import { ReactNode } from "react";

// Define interfaces for props
interface ButtonProps {
  onClick: () => void;
  disabled?: boolean;
  variant: "primary" | "secondary";
  children: ReactNode;
}

// Component with typed props
function Button({ onClick, disabled = false, variant, children }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children}
    </button>
  );
}

// Example usage
function App() {
  return (
    <Button variant="primary" onClick={() => console.log("clicked")}>
      Click me
    </Button>
  );
}
```

### 2. Generic Components and Hooks

```tsx
import { useState, useEffect } from "react";

// Generic async data hook
function useAsync<T>(
  asyncFn: () => Promise<T>,
  deps: any[] = []
): {
  data: T | null;
  loading: boolean;
  error: Error | null;
} {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<Error | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        const result = await asyncFn();
        setData(result);
      } catch (e) {
        setError(e instanceof Error ? e : new Error(String(e)));
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, deps);

  return { data, loading, error };
}

// Generic list component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// Usage example
interface User {
  id: number;
  name: string;
}

function UserList() {
  const {
    data: users,
    loading,
    error,
  } = useAsync<User[]>(() => fetch("/api/users").then((res) => res.json()));

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!users) return null;

  return <List items={users} renderItem={(user) => <span>{user.name}</span>} />;
}
```

### 3. Typed Context

```tsx
import { createContext, useContext, useState } from "react";

// Define theme interface
interface Theme {
  primary: string;
  secondary: string;
  background: string;
}

// Define context value interface
interface ThemeContextValue {
  theme: Theme;
  toggleTheme: () => void;
}

// Create typed context
const ThemeContext = createContext<ThemeContextValue | null>(null);

// Create provider with proper typing
export function ThemeProvider({ children }: { children: ReactNode }) {
  const [isDark, setIsDark] = useState(false);

  const theme: Theme = {
    primary: isDark ? "#fff" : "#000",
    secondary: isDark ? "#ccc" : "#666",
    background: isDark ? "#000" : "#fff",
  };

  const toggleTheme = () => setIsDark(!isDark);

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Custom hook with type safety
export function useTheme(): ThemeContextValue {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }
  return context;
}
```

## Example Explanations

1. The typed components example shows how to properly type props using interfaces and literal types. It demonstrates TypeScript's ability to enforce prop types and provide autocompletion.

2. The generic components example illustrates how to create reusable components and hooks that work with any data type while maintaining type safety. It shows proper error handling and loading state management.

3. The typed context example demonstrates how to create type-safe context providers and consumers, including proper error handling for missing providers.

## Common Pitfalls & Solutions

- **any Type**: Avoid using `any`, prefer unknown for truly unknown types
- **Type Assertions**: Use sparingly, prefer type guards when possible
- **Generic Constraints**: Use extends to limit generic types
- **Event Types**: Use correct event types from @types/react
- **Props Typing**: Don't forget to type children and optional props
- **Union Types**: Use discriminated unions for complex state
- **Type Guards**: Implement proper runtime checks

## Exercises

### Exercise 1: Form Component Types

Task: Create a typed form component that handles different input types and form submission.
Expected result: A reusable form component with proper typing for inputs, validation, and submission handling.

### Exercise 2: Custom Hook Types

Task: Build a custom hook for managing pagination state with TypeScript.
Expected result: A type-safe pagination hook that handles page numbers, limits, and data fetching.

## Further Reading

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [React + TypeScript: Best Practices](https://www.sitepoint.com/react-with-typescript-best-practices/)
