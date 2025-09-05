# React Performance Optimization

Advanced techniques for optimizing React application performance through code splitting, lazy loading, and smart rendering strategies.

> **Estimated reading time:** 10 min

## Overview

Performance optimization in React applications involves various strategies, from code splitting to prevent large bundle sizes, to memoization for preventing unnecessary re-renders. This guide covers the most important techniques for improving both initial load time and runtime performance.

Understanding when and how to apply these optimizations is crucial. We'll focus on practical examples that demonstrate measurable improvements while maintaining code readability and maintainability.

## Key Concepts

- **Code Splitting**: Breaking bundle into smaller chunks
- **Lazy Loading**: Loading components only when needed
- **Suspense**: Showing fallback content while loading
- **Memoization**: Caching values to prevent recalculation
- **Virtual DOM**: React's rendering optimization strategy
- **Bundle Size**: Total size of JavaScript sent to browser
- **Performance Profiling**: Measuring and analyzing performance

## Examples

### 1. Lazy Loading with Suspense

```jsx
import { Suspense, lazy } from "react";
import { Routes, Route } from "react-router-dom";

// Lazy load route components
const Dashboard = lazy(() => import("./pages/Dashboard"));
const Profile = lazy(() => import("./pages/Profile"));
const Settings = lazy(() => import("./pages/Settings"));

function App() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/profile" element={<Profile />} />
        <Route path="/settings" element={<Settings />} />
      </Routes>
    </Suspense>
  );
}

// Example of a large component that will be lazy loaded
// pages/Dashboard.jsx
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      {/* Complex dashboard UI */}
    </div>
  );
}
```

### 2. Performance Optimization with useMemo and React.memo

```jsx
import { useMemo, useState, memo } from "react";

// Expensive calculation component
function ExpensiveList({ items, filterTerm }) {
  // Memoize filtered items
  const filteredItems = useMemo(() => {
    console.log("Filtering items..."); // Debug log
    return items.filter((item) =>
      item.name.toLowerCase().includes(filterTerm.toLowerCase())
    );
  }, [items, filterTerm]); // Only recalculate if items or filterTerm changes

  return (
    <ul>
      {filteredItems.map((item) => (
        <ListItem key={item.id} item={item} />
      ))}
    </ul>
  );
}

// Memoized list item component
const ListItem = memo(function ListItem({ item }) {
  console.log(`Rendering item ${item.id}`); // Debug log

  return (
    <li className="list-item">
      <h3>{item.name}</h3>
      <p>{item.description}</p>
    </li>
  );
});

// Parent component
function SearchableList() {
  const [items] = useState(generateLargeItemList()); // Assume this generates 1000+ items
  const [filterTerm, setFilterTerm] = useState("");

  return (
    <div>
      <input
        type="text"
        value={filterTerm}
        onChange={(e) => setFilterTerm(e.target.value)}
        placeholder="Filter items..."
      />
      <ExpensiveList items={items} filterTerm={filterTerm} />
    </div>
  );
}
```

## Example Explanations

1. The lazy loading example demonstrates how to split your application into smaller chunks that load on demand. When a user navigates to a route, only the necessary code for that route is loaded, improving initial page load time.

2. The memoization example shows how to optimize expensive calculations and prevent unnecessary re-renders. `useMemo` caches the filtered list while `React.memo` prevents re-rendering list items when their props haven't changed.

## Common Pitfalls & Solutions

- **Premature Optimization**: Measure performance before optimizing
- **Over-memoization**: Don't memoize simple calculations or components
- **Bundle Size Bloat**: Use bundle analyzer to identify large dependencies
- **Memory Leaks**: Clean up subscriptions and event listeners
- **Render Blocking**: Move expensive operations off the main thread
- **Network Waterfalls**: Implement proper loading strategies
- **Large Component Trees**: Break down into smaller, focused components

## Exercises

### Exercise 1: Code Splitting Implementation

Task: Split a large dashboard page into lazy-loaded components based on tab navigation.
Expected result: Each dashboard tab loads independently when selected, with appropriate loading states.

### Exercise 2: Render Optimization

Task: Optimize a list of 1000+ items that allows filtering and sorting.
Expected result: The list should maintain smooth performance when filtering/sorting, using appropriate memoization techniques.

## Further Reading

- [React Code Splitting Documentation](https://reactjs.org/docs/code-splitting.html)
- [Web.dev Performance Guide](https://web.dev/react/)
- [React Performance Optimization Tips](https://kentcdodds.com/blog/usememo-and-usecallback)
- [Bundle Size Optimization](https://github.com/webpack-contrib/webpack-bundle-analyzer)
