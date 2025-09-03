# React Event Handling

Master React's event system and learn how to handle user interactions effectively in your components.

## Overview

React implements a synthetic event system that normalizes browser differences while maintaining full access to the native browser events. This system provides consistent behavior across different browsers and platforms, while offering better performance through event delegation.

Events in React are named using camelCase and pass SyntheticEvent objects to your handlers, wrapping the native browser event while providing the same interface.

## Key Concepts

- **Synthetic Events**: React's cross-browser wrapper for native browser events
- **Event Delegation**: React attaches event listeners at the root level for better performance
- **Event Handlers**: Functions that receive events and handle user interactions
- **Event Prevention**: Methods like `preventDefault()` and `stopPropagation()`
- **Handler Binding**: Different ways to bind event handlers to maintain correct `this` context

## Examples

### 1. Button Click Handler with Parameters

```jsx
function ActionButton({ item, onAction }) {
  const handleClick = (event) => {
    // Access event properties
    console.log("Clicked at:", event.clientX, event.clientY);

    // Pass additional data to parent
    onAction(item.id);
  };

  return (
    <button
      onClick={handleClick}
      // Alternative inline approach with parameters
      onDoubleClick={(e) => onAction(item.id, e)}
    >
      {item.label}
    </button>
  );
}
```

In this sample, we demonstrate how to handle click events with parameters in a button component.

### 2. Form Submission with Controlled Inputs

```jsx
import { useState } from "react";

function LoginForm({ onSubmit }) {
  const [formData, setFormData] = useState({
    email: "",
    password: "",
  });

  const handleSubmit = (event) => {
    event.preventDefault(); // Prevent browser form submission
    onSubmit(formData);
  };

  const handleChange = (event) => {
    const { name, value } = event.target;
    setFormData((prev) => ({
      ...prev,
      [name]: value,
    }));
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        required
      />
      <input
        type="password"
        name="password"
        value={formData.password}
        onChange={handleChange}
        required
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

In this example, we demonstrate how to handle click events with parameters in a button component.

## Explanation of Examples

The ActionButton example demonstrates handling click events with both direct handler functions and inline arrow functions. It shows how to access event properties and pass additional parameters to parent components.

The LoginForm example showcases form handling with controlled inputs. It prevents default form submission, manages form state with useState, and demonstrates a reusable change handler for multiple inputs.

## Common Pitfalls & Solutions

- **Event Pooling**: In React 17+, event pooling was removed. You no longer need to call `event.persist()`.
- **Handler Binding**: Use arrow functions or constructor binding to avoid `this` context issues.
- **Memory Leaks**: Avoid creating new handler functions in each render unless necessary.
- **Event Bubbling**: Use `stopPropagation()` carefully, as it might break event delegation.
- **Synthetic vs Native**: Access `event.nativeEvent` when you need the browser's native event object.

## Exercises

1. **Form Logger**

   - Task: Create a form with three inputs (name, email, message) that logs all values on submit.
   - Expected Solution: Use preventDefault(), useState for form data, and a single handleChange function for all inputs.

2. **Toggle Button**
   - Task: Create a button that toggles a "dark" class on a parent div when clicked.
   - Expected Solution: Use useState for toggle state, and apply/remove the class based on the state.

## Further Reading

- [React Synthetic Events](https://reactjs.org/docs/events.html)
- [Handling Events in React](https://beta.reactjs.org/learn/responding-to-events)
- [React Event System Deep Dive](https://www.youtube.com/watch?v=dRo_egw7tBc)
