# React Events and Synthetic Events - Guide

## 1. What are Events in React?

**Definition:**
Events are interactions that occur in the browser which your app can respond to—like clicks, typing in an input, hovering, scrolling, or submitting a form.

**Common React Events:**

- **Mouse Events:** `onClick`, `onDoubleClick`, `onMouseEnter`, `onMouseLeave`
- **Keyboard Events:** `onKeyDown`, `onKeyUp`
- **Form Events:** `onChange`, `onSubmit`, `onFocus`, `onBlur`

**Example:**

```jsx
function Button() {
  const handleClick = () => {
    alert("Button clicked!");
  };

  return <button onClick={handleClick}>Click me</button>;
}
```

---

## 2. What are Synthetic Events?

**Definition:**
React wraps native browser events into a `SyntheticEvent` object. This normalizes differences between browsers and provides a consistent API.

**Key Points:**

- Works the same in all browsers.
- Event objects are pooled for performance.
- Access native event via `event.nativeEvent` if needed.

**Example:**

```jsx
function Input() {
  const handleChange = (event) => {
    console.log(event.type); // 'change'
    console.log(event.target.value); // input value
  };

  return <input type="text" onChange={handleChange} />;
}
```

**React 18 Note:** `event.persist()` is usually unnecessary now due to improved pooling.

---

## 3. Best Practices in React Events

1. **Use Synthetic Events**

   - Use React’s event system instead of native DOM events.

2. **Avoid Inline Functions in JSX When Possible**

   ```jsx
   // Less ideal
   <button onClick={() => doSomething(item.id)}>Click</button>;

   // Better
   const handleClick = useCallback(() => onClick(item.id), [item.id, onClick]);
   <button onClick={handleClick}>Click</button>;
   ```

3. **Event Delegation**

   ```jsx
   <ul onClick={handleClick}>
     {items.map((item) => (
       <li key={item.id} data-id={item.id}>
         {item.name}
       </li>
     ))}
   </ul>
   ```

4. **Pass Function References, Not Calls**

   ```jsx
   // Wrong
   <button onClick={handleClick()}>Click</button>
   // Correct
   <button onClick={handleClick}>Click</button>
   ```

5. **useCallback for Handlers Passed to Children**

   ```jsx
   const handleSubmit = useCallback(() => {
     console.log("Submitted!");
   }, []);
   ```

6. **Keyboard Accessibility**

   ```jsx
   <button onClick={handleClick}>Submit</button> // preferred
   <div role="button" tabIndex={0} onKeyDown={handleClick}>Submit</div> // if needed
   ```

7. **Prevent Default Actions**

   ```jsx
   const handleSubmit = (e) => {
     e.preventDefault();
     console.log("Form submitted!");
   };
   ```

8. **Debounce Expensive Handlers**

   - Use for `input`, `scroll`, or `resize` events to prevent frequent state updates.

---

## 4. Visual Analogy of React Event Flow

```
[Browser Event (click, keypress)]
          │
          ▼
   [Native Event Object]
          │
          ▼
   [React SyntheticEvent]  <-- normalized, cross-browser safe
          │
          ▼
   [Your Event Handler Function]
```

## 5. Key Concepts

- **Synthetic Events**: React's cross-browser wrapper for native browser events
- **Event Delegation**: React attaches event listeners at the root level for better performance
- **Event Handlers**: Functions that receive events and handle user interactions
- **Event Prevention**: Methods like `preventDefault()` and `stopPropagation()`
- **Handler Binding**: Different ways to bind event handlers to maintain correct `this` context

## 6. Examples

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

> The `ActionButton` example demonstrates handling click events with both direct handler functions and inline arrow functions. It shows how to access event properties and pass additional parameters to parent components.

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

> The LoginForm example showcases form handling with controlled inputs. It prevents default form submission, manages form state with useState, and demonstrates a reusable change handler for multiple inputs.

## 7. Common Pitfalls & Solutions

- **Event Pooling**: In React 17+, event pooling was removed. You no longer need to call `event.persist()`.
- **Handler Binding**: Use arrow functions or constructor binding to avoid `this` context issues.
- **Memory Leaks**: Avoid creating new handler functions in each render unless necessary.
- **Event Bubbling**: Use `stopPropagation()` carefully, as it might break event delegation.
- **Synthetic vs Native**: Access `event.nativeEvent` when you need the browser's native event object.

## 8. Exercises

1. **Form Logger**

   - Task: Create a form with three inputs (name, email, message) that logs all values on submit.
   - Expected Solution: Use preventDefault(), useState for form data, and a single handleChange function for all inputs.

2. **Toggle Button**
   - Task: Create a button that toggles a "dark" class on a parent div when clicked.
   - Expected Solution: Use useState for toggle state, and apply/remove the class based on the state.

**Summary:**

- **Event:** User interaction like click or key press.
- **Synthetic Event:** React’s wrapper around native events, consistent and optimized.
- **Best Practices:** Use callbacks, delegate when possible, avoid inline functions, ensure accessibility, and debounce heavy events.
