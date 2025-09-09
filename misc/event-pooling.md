# **Event Pooling in React**

## **1. Concise Definition**

Event Pooling is a performance optimization in **React (pre-React 17)** where React reuses **SyntheticEvent objects** instead of creating a new one for every event.

- A **SyntheticEvent** is React’s cross-browser wrapper around native events.
- In event pooling, once the event handler has finished executing, the event object is **returned to a pool** and its properties are nullified.

---

## **2. Why It Exists / Use Case**

- **Performance:** Creating a new event object for every user interaction (clicks, keypresses, etc.) could become costly in a large application. Pooling reuses objects to save memory and reduce GC pressure.
- **Consistency:** React ensures that all events behave the same across different browsers.

**Key point:** After the event handler executes, the event object’s properties are **cleared**. If you try to access them asynchronously (e.g., inside a `setTimeout` or Promise), you’ll get `null`.

---

## **3. Example with Problem**

```jsx
import React from "react";

function MyButton() {
  const handleClick = (event) => {
    console.log(event.type); // Works here
    setTimeout(() => {
      console.log(event.type); // ❌ This will be null because of pooling
    }, 1000);
  };

  return <button onClick={handleClick}>Click Me</button>;
}

export default MyButton;
```

---

## **4. How to Handle Asynchronous Access**

Use `event.persist()` to remove the event from the pool:

```jsx
const handleClick = (event) => {
  event.persist(); // Prevents React from nullifying it
  setTimeout(() => {
    console.log(event.type); // Works correctly
  }, 1000);
};
```

- **React 17+:** Event pooling is **removed**, so SyntheticEvent objects are not reused. You can safely access event properties asynchronously without `persist()`.

---

## **5. Summary Table**

| Concept              | Details                                              |
| -------------------- | ---------------------------------------------------- |
| Event Type           | SyntheticEvent                                       |
| Purpose              | Performance optimization (memory reuse)              |
| Access after handler | Cleared unless `persist()` is called                 |
| React 17+            | Pooling removed; events behave like native JS events |

---

## **Visual Aid: Event Pooling Flow**

```
User clicks button
       │
       ▼
React creates SyntheticEvent object
       │
       ▼
Event handler runs -> you can use event properties
       │
       ▼
Event returned to pool -> properties nullified
       │
       ▼
Next event reuses the same object
```

---

**Takeaways:**

- Event pooling exists for **performance**, mostly in React 16.
- Use `event.persist()` if you need the event asynchronously.
- In React 17+, you don’t need to worry about pooling anymore.

---
