# Conditional Rendering and Lists in React

Master the patterns for rendering dynamic content and collections in React components.

## Overview

React provides several patterns for conditionally showing content and rendering lists of data. Understanding these patterns is crucial for building dynamic UIs that respond to state changes and can display collections of data efficiently.

The key to effective conditional rendering is choosing the right pattern for readability and maintainability, while proper list rendering requires understanding React's reconciliation process and the importance of keys.

## Key Concepts

- **Conditional Rendering**: Using JavaScript operators (`&&`, ternary, IIFE) to conditionally show content
- **Short-Circuit Evaluation**: Using `&&` for conditional rendering, with awareness of falsy value pitfalls
- **List Rendering**: Using `map()` to transform arrays into React elements
- **Keys**: Unique identifiers that help React track which items have changed
- **Reconciliation**: React's process for determining which parts of the UI need to update

## Examples

### 1. Conditional Rendering Patterns

```jsx
function UserDashboard({ user, isLoading, error }) {
  if (isLoading) {
    return <div>Loading...</div>;
  }

  if (error) {
    return <div>Error: {error.message}</div>;
  }

  return (
    <div>
      {/* Ternary operator for conditional rendering */}
      <h1>{user ? user.name : "Guest"}</h1>

      {/* Logical AND for optional rendering */}
      {user?.isAdmin && <AdminPanel />}

      {/* IIFE for complex conditions */}
      {(() => {
        switch (user?.role) {
          case "admin":
            return <AdminView />;
          case "editor":
            return <EditorView />;
          default:
            return <UserView />;
        }
      })()}
    </div>
  );
}
```

In this example, it shows how to use different conditional rendering patterns based on the user's state. If the user is loading, it displays a loading message. If there's an error, it shows the error message. Otherwise, it renders the user information depending on their role.

### 2. List Rendering with Proper Keys

```jsx
function TodoList({ todos, onToggle, onDelete }) {
  if (!todos?.length) {
    return <p>No todos yet! Add one to get started.</p>;
  }

  return (
    <ul>
      {todos.map((todo) => (
        // Using stable, unique ID as key
        <li key={todo.id}>
          <input
            type="checkbox"
            checked={todo.completed}
            onChange={() => onToggle(todo.id)}
          />
          <span
            style={{
              textDecoration: todo.completed ? "line-through" : "none",
            }}
          >
            {todo.text}
          </span>
          <button onClick={() => onDelete(todo.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
}
```

For this code snippet, it shows how to use the `map()` function to render a list of todos, each with its own checkbox and delete button.

## Explanation of Examples

The UserDashboard example demonstrates various conditional rendering patterns:

- Early returns for loading/error states
- Ternary operator for simple either/or cases
- Logical AND for optional elements
- IIFE for complex conditional logic

The TodoList example shows proper list rendering with:

- Empty state handling
- Stable keys using unique IDs
- Proper event handling for each item
- Conditional styling based on item state

## Common Pitfalls & Solutions

- **Falsy Values**: Be careful with `&&` operator and falsy values (0, empty string). Use ternary instead.
- **Index as Keys**: Avoid using array indices as keys unless the list is static and won't reorder.
- **Missing Keys**: Always provide keys when mapping arrays to elements.
- **Key Scope**: Keys must be unique among siblings, not globally.
- **Complex Conditions**: Extract complex conditional logic into separate components or functions.

## Exercises

1. **Filtered List**

   - Task: Create a list with a search input that filters items and shows "No results found" when appropriate.
   - Expected Solution: Use useState for search term, filter array based on search, conditional render for no results.

2. **Stable Keys Fix**
   - Task: Debug a list that re-renders incorrectly when items are reordered using array indices as keys.
   - Expected Solution: Modify the data structure to include unique IDs and use those as keys instead of indices.

## Further Reading

- [Conditional Rendering in React](https://reactjs.org/docs/conditional-rendering.html)
- [Lists and Keys](https://reactjs.org/docs/lists-and-keys.html)
- [Understanding React's Key Prop](https://kentcdodds.com/blog/understanding-reacts-key-prop)
- [React Reconciliation](https://reactjs.org/docs/reconciliation.html)
