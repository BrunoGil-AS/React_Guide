# State Management in React: Context, Redux Toolkit, and Zustand

A comprehensive guide to managing application state in React using modern state management solutions.

> **Estimated reading time:** 12 min

## Overview

As React applications grow, managing state becomes increasingly complex. While local component state serves well for simple use cases, larger applications benefit from more robust state management solutions. This guide explores three popular approaches: React's built-in Context API, Redux Toolkit for complex state logic, and Zustand for a simpler alternative.

Each solution has its strengths: Context for prop drilling prevention, Redux Toolkit for predictable state updates, and Zustand for minimal boilerplate.

## Key Concepts

- **Global State**: Application state that needs to be accessed by multiple components
- **State Provider**: Component that makes state available to a component tree
- **Action**: A plain object describing what happened in the application
- **Reducer**: Pure function that determines how state updates in response to actions
- **Store**: Central location where state is held and managed
- **Selector**: Function that extracts specific pieces of state
- **Middleware**: Code that runs between dispatching an action and reaching the reducer

## Examples

### 1. Theme Toggle with Context API

```jsx
import { createContext, useContext, useState } from "react";

// Create context
const ThemeContext = createContext();

// Provider component
function ThemeProvider({ children }) {
  const [isDark, setIsDark] = useState(false);

  const toggleTheme = () => setIsDark((prev) => !prev);

  return (
    <ThemeContext.Provider value={{ isDark, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Consumer component
function ThemedButton() {
  const { isDark, toggleTheme } = useContext(ThemeContext);

  return (
    <button
      onClick={toggleTheme}
      style={{
        background: isDark ? "#333" : "#fff",
        color: isDark ? "#fff" : "#333",
      }}
    >
      Toggle Theme
    </button>
  );
}

// Usage
function App() {
  return (
    <ThemeProvider>
      <ThemedButton />
    </ThemeProvider>
  );
}
```

### 2. Redux Toolkit Counter

```jsx
import { createSlice, configureStore } from "@reduxjs/toolkit";
import { Provider, useSelector, useDispatch } from "react-redux";

// Create slice
const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => {
      state.value += 1;
    },
    decrement: (state) => {
      state.value -= 1;
    },
  },
});

// Create store
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer,
  },
});

// Counter component
function Counter() {
  const count = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(counterSlice.actions.increment())}>
        Increment
      </button>
      <button onClick={() => dispatch(counterSlice.actions.decrement())}>
        Decrement
      </button>
    </div>
  );
}

// App with store provider
function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

### 3. Zustand Store

```jsx
import create from "zustand";

// Create store
const useStore = create((set) => ({
  bears: 0,
  increasePopulation: () => set((state) => ({ bears: state.bears + 1 })),
  removeAllBears: () => set({ bears: 0 }),

  // Example of derived state
  doubledBears: 0,
  // Subscribe to changes
  subscribe: () => {
    useStore.subscribe(
      (state) => state.bears,
      (bears) => set({ doubledBears: bears * 2 })
    );
  },
}));

// Component using the store
function BearCounter() {
  const { bears, doubledBears, increasePopulation, removeAllBears } =
    useStore();

  return (
    <div>
      <p>Bears: {bears}</p>
      <p>Doubled: {doubledBears}</p>
      <button onClick={increasePopulation}>Add bear</button>
      <button onClick={removeAllBears}>Remove all</button>
    </div>
  );
}
```

## Example Explanations

1. The Context example demonstrates a simple theme toggle implementation. The context provider wraps the application and makes the theme state and toggle function available to any nested component, eliminating prop drilling.

2. The Redux Toolkit example shows how to create and manage a counter with modern Redux. It uses the `createSlice` API to reduce boilerplate and implements immutable updates implicitly through Immer.

3. The Zustand example illustrates its minimalist approach to state management. It creates a custom hook that provides state and actions, with built-in support for derived state and subscriptions.

## Common Pitfalls & Solutions

- **Context Performance**: Don't put frequently changing values in context, split contexts by update frequency
- **Redux Overuse**: Not every state needs to be in Redux - use local state for UI-specific data
- **Store Structure**: Keep state normalized and avoid deeply nested structures
- **Selector Memoization**: Use memoized selectors (e.g., `createSelector`) for expensive computations
- **State Updates**: Always treat state as immutable, use proper update patterns
- **Provider Hell**: Combine providers into a single component to avoid nesting multiple providers

## Exercises

### Exercise 1: Redux Counter with Async Actions

Task: Extend the Redux counter to include an async action that increments after a delay.
Expected result: Add a "Delayed Increment" button that increases the counter after 1 second using Redux Thunk.

### Exercise 2: Zustand Migration

Task: Convert the Redux counter example to use Zustand.
Expected result: Implement the same functionality (increment, decrement) using Zustand's simpler API.

## Further Reading

- [Redux Toolkit Documentation](https://redux-toolkit.js.org/)
- [Zustand GitHub Repository](https://github.com/pmndrs/zustand)
- [React Context Performance](https://github.com/facebook/react/issues/15156)
- [You Might Not Need Redux - Dan Abramov](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)
