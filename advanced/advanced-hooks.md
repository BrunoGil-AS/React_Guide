# Advanced React Hooks: useReducer, useCallback, and useMemo

A deep dive into React's advanced hooks for state management, function memoization, and performance optimization.

> **Estimated reading time:** 10 min

## Overview

Advanced hooks in React provide powerful patterns for managing complex state logic (`useReducer`), optimizing function references (`useCallback`), and memoizing expensive computations (`useMemo`). While basic hooks like `useState` and `useEffect` handle simpler use cases, these advanced hooks become essential when building larger applications or optimizing performance.

Understanding when and how to use these hooks is crucial for writing efficient React applications. They help prevent unnecessary re-renders, manage complex state updates, and improve overall application performance.

## Key Concepts

- **useReducer**: A hook for managing complex state logic through a reducer function, similar to Redux's pattern
- **useCallback**: Memoizes function references to prevent unnecessary re-renders of child components
- **useMemo**: Caches the result of expensive computations to avoid recalculating on every render
- **Reducer Function**: A pure function that takes state and action as arguments and returns new state
- **Memoization**: Technique of caching values to avoid expensive recalculations

## Examples

### 1. Counter with useReducer

```jsx
import { useReducer } from "react";

// Define action types
const ACTIONS = {
  INCREMENT: "increment",
  DECREMENT: "decrement",
  RESET: "reset",
};

// Reducer function
function counterReducer(state, action) {
  switch (action.type) {
    case ACTIONS.INCREMENT:
      return { count: state.count + 1 };
    case ACTIONS.DECREMENT:
      return { count: state.count - 1 };
    case ACTIONS.RESET:
      return { count: 0 };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: ACTIONS.INCREMENT })}>+</button>
      <button onClick={() => dispatch({ type: ACTIONS.DECREMENT })}>-</button>
      <button onClick={() => dispatch({ type: ACTIONS.RESET })}>Reset</button>
    </div>
  );
}
```

### 2. Memoized Callback with useCallback

```jsx
import { useCallback, useState } from "react";

function ParentComponent() {
  const [count, setCount] = useState(0);

  // Memoize the callback
  const handleClick = useCallback(() => {
    setCount((c) => c + 1);
  }, []); // Empty deps array since we use functional update

  return (
    <div>
      <p>Count: {count}</p>
      <ExpensiveChild onClick={handleClick} />
    </div>
  );
}

// Child component that relies on prop stability
const ExpensiveChild = React.memo(({ onClick }) => {
  console.log("ExpensiveChild rendered");
  return <button onClick={onClick}>Increment</button>;
});
```

### 3. Expensive Calculation with useMemo

```jsx
import { useMemo, useState } from "react";

function ExpensiveCalculation({ numbers }) {
  const [filter, setFilter] = useState("");

  // Memoize expensive calculation
  const sortedNumbers = useMemo(() => {
    console.log("Calculating sorted numbers...");
    return numbers
      .filter((n) => n.toString().includes(filter))
      .sort((a, b) => b - a);
  }, [numbers, filter]); // Recalculate only when numbers or filter changes

  return (
    <div>
      <input
        value={filter}
        onChange={(e) => setFilter(e.target.value)}
        placeholder="Filter numbers..."
      />
      <ul>
        {sortedNumbers.map((n) => (
          <li key={n}>{n}</li>
        ))}
      </ul>
    </div>
  );
}
```

## Example Explanations

1. The `useReducer` example demonstrates managing complex state transitions through a reducer pattern. The reducer function processes actions to update state in a predictable way, making it easier to track state changes and debug issues.

2. The `useCallback` example shows how to prevent unnecessary re-renders of child components that depend on function props. By memoizing the callback, we ensure the function reference remains stable across re-renders unless dependencies change.

3. The `useMemo` example illustrates caching expensive computations. The sorting and filtering operation only runs when the input array or filter string changes, not on every render.

## Common Pitfalls & Solutions

- **Over-optimization**: Don't wrap everything in `useMemo` or `useCallback` - only use when there's a measurable performance benefit
- **Missing dependencies**: Always include all values used in the calculation/callback in the dependencies array
- **Mutating reducer state**: Always return new state objects from reducers, never mutate existing state
- **Complex reducer actions**: Keep actions simple and focused - split complex state logic into multiple reducers if needed
- **Unnecessary memoization**: Using `useMemo` for simple calculations can be slower than just doing the calculation directly

## Exercises

### Exercise 1: Refactor useState to useReducer

Task: Convert a shopping cart using multiple `useState` hooks into a single `useReducer`.
Expected result: A shopping cart component that manages items, total, and shipping method through a reducer with actions for ADD_ITEM, REMOVE_ITEM, and UPDATE_SHIPPING.

### Exercise 2: Optimize Rendering

Task: Fix a parent-child component where the child re-renders unnecessarily when receiving a function prop.
Expected result: Use `useCallback` and `React.memo` to prevent child re-renders when only the parent's local state changes.

## Further Reading

- [useReducer - React Docs](https://react.dev/reference/react/useReducer)
- [useCallback - React Docs](https://react.dev/reference/react/useCallback)
- [A Complete Guide to useCallback - Dan Abramov's Blog](https://overreacted.io/a-complete-guide-to-useeffect/)
- [React Performance Optimization - Kent C. Dodds](https://kentcdodds.com/blog/usememo-and-usecallback)
