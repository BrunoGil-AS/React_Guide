# Callback Function and Listener

## **1. Callback Function**

- **Definition:** A callback is a function that you pass as an argument to another function, so it can be **called later** once some task is complete.
- **When to use:**

  - Asynchronous operations (e.g., fetching data, waiting for user input).
  - Passing logic into reusable functions.

- **Example in JavaScript:**

  ```js
  function fetchData(callback) {
    setTimeout(() => {
      const data = { id: 1, name: "Bruno" };
      callback(data); // callback executed after data is "fetched"
    }, 1000);
  }

  // Using the callback
  fetchData((result) => {
    console.log("Received:", result);
  });
  ```

  > Here, `callback` is executed **after** `fetchData` finishes its asynchronous work.

---

## **2. Listener**

- **Definition:** A listener is a **special kind of callback** that "listens" for a specific event and executes when that event happens.
- **When to use:**

  - User interactions (clicks, inputs, mouse events).
  - System or framework events (React Synthetic Events, Node.js event emitters).

- **Example in JavaScript (browser):**

  ```js
  document.getElementById("btn").addEventListener("click", () => {
    console.log("Button clicked!");
  });
  ```

  > The arrow function here is a **listener** for the `"click"` event.

---

## **3. Key Difference**

- **Callback:**

  - A generic function passed around to be executed later.
  - Can be manual or event-driven.

- **Listener:**

  - A callback specifically tied to an **event system**.
  - Usually registered with something like `.addEventListener`, `.on`, etc.

👉 You can think:

```
All listeners are callbacks,
but not all callbacks are listeners.
```

---

## **4. Categorization of the Topic**

This topic fits under:

- **JavaScript Core Concepts**

  - → Functions as First-Class Citizens
  - → Callbacks

- **Asynchronous Programming**

  - → Callback pattern (before promises/async-await)

- **Event-Driven Programming**

  - → Event listeners in DOM
  - → Synthetic events in React

If you’re structuring your notes, you could categorize it like this:

```
JavaScript Functions
 ├── Higher-Order Functions
 ├── Callbacks
 └── Event Listeners
```

---

✅ **Summary:**

- A **callback** is just a function passed to another function to be executed later.
- A **listener** is a callback that responds to an event in an event-driven system.
- Categorize under **JavaScript Functions & Event Handling**.

---
