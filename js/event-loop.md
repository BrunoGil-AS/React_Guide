# 📘 Guide: Event Loop, Asynchronous (Non-Blocking), and Concurrency in JavaScript & React

---

## 🔹 1. Event Loop

### Definition

The **event loop** is the mechanism that allows JavaScript to manage multiple tasks asynchronously, even though it runs on a single thread.

- **Call Stack**: Where synchronous code is executed line by line. If busy, nothing else can run.
- **Callback/Task Queue**: Where async tasks (like timers or fetch responses) wait until the stack is free.
- **Browser/Web APIs**: External APIs (like `fetch`, `setTimeout`, DOM events) that handle async work outside the call stack and push results back into the queues when ready.
- **Event Loop**: The orchestrator that checks if the call stack is empty and, if so, moves tasks from the queues into the stack.

### Diagram

```
 ┌───────────────┐
 |   Call Stack   |  ← runs sync code
 └──────▲─────────┘
        |
        ▼
 ┌───────────────┐
 |   Event Loop   |  ← the "traffic cop"
 └──────▲─────────┘
        |
        ▼
 ┌───────────────┐
 | Callback Queue |  ← async tasks wait here
 └───────────────┘
```

### Example

```js
console.log("1");

setTimeout(() => {
  console.log("2"); // goes into callback queue, runs later
}, 0);

console.log("3");

// Output: 1, 3, 2
```

---

## 🔹 2. Asynchronous (Non-Blocking)

### Definition

**Asynchronous** code allows tasks to start now and finish later without stopping the rest of the program. JavaScript is **non-blocking**, meaning long tasks (like network requests) don’t freeze the main thread.

### Diagram

```
[ Call API ] → handled by Web APIs (browser)
        ↓
  response ready → placed in Task Queue
        ↓
 Event Loop → moves it into Call Stack when free
```

### Example (React)

```jsx
import { useEffect, useState } from "react";

function Users() {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    // Async: doesn't block rendering
    fetch("https://jsonplaceholder.typicode.com/users")
      .then((res) => res.json())
      .then((data) => setUsers(data));
  }, []);

  return (
    <ul>
      {users.map((u) => (
        <li key={u.id}>{u.name}</li>
      ))}
    </ul>
  );
}
```

✅ UI renders immediately, data loads later.

### 🔧 Guide: Implementing Async in React

1. \*\*Fetching Data with \*\*\`\`
   Use `fetch` or `axios` inside `useEffect` to trigger requests after render.

   ```jsx
   useEffect(() => {
     async function loadData() {
       const res = await fetch("/api/data");
       const json = await res.json();
       setData(json);
     }
     loadData();
   }, []);
   ```

2. **Handling Loading States**
   Maintain a `loading` state to inform the user.

   ```jsx
   const [loading, setLoading] = useState(true);

   useEffect(() => {
     fetch("/api/data")
       .then((r) => r.json())
       .then((data) => {
         setData(data);
         setLoading(false);
       });
   }, []);

   if (loading) return <p>Loading...</p>;
   ```

3. **Error Handling**
   Wrap async calls in `try/catch` or handle `.catch` to display error UI.

   ```jsx
   try {
     const res = await fetch("/api/data");
     if (!res.ok) throw new Error("Failed to fetch");
   } catch (e) {
     setError(e.message);
   }
   ```

4. **React 18 Tools**

   - Use `startTransition` to keep urgent updates smooth while performing expensive state updates.
   - Use `Suspense` with `React.lazy` or data fetching libraries to declaratively handle async loading UI.

---

## 🔹 3. Concurrency

### Definition

**Concurrency** is the ability to handle multiple tasks _in progress_ at the same time. JavaScript doesn’t run code in parallel (single-threaded), but it **switches between tasks efficiently** using the event loop.

In React 18+, concurrency also means React can _pause, resume, and prioritize rendering_.

### Diagram

```
User typing ──────────┐
                      │ urgent (immediate update)
Filtering large list ─┘ non-urgent (concurrent update)
```

### Example (React 18 `startTransition`)

```jsx
import { useState, startTransition } from "react";

function Search({ items }) {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState(items);

  const handleChange = (e) => {
    const value = e.target.value;
    setQuery(value); // urgent update (UI stays responsive)

    startTransition(() => {
      // concurrent update (can be paused/resumed)
      setResults(items.filter((item) => item.includes(value)));
    });
  };

  return (
    <>
      <input value={query} onChange={handleChange} />
      <ul>
        {results.map((r, i) => (
          <li key={i}>{r}</li>
        ))}
      </ul>
    </>
  );
}
```

✅ Typing stays smooth, even if filtering is expensive.

---

## 🔹 4. Concurrency vs Asynchronous

### Key Differences

| Feature     | Asynchronous           | Concurrency                              |
| ----------- | ---------------------- | ---------------------------------------- |
| Focus       | Non-blocking execution | Managing multiple tasks efficiently      |
| Threading   | Still single-threaded  | Still single-threaded, interleaved tasks |
| JS Example  | `setTimeout`, `fetch`  | `startTransition`, `useDeferredValue`    |
| React Usage | Fetching data, effects | Smooth UI updates, interruptible renders |

---

**_So..._**

Concurrency builds on top of Asynchronous behavior:

- Async allows tasks to run without blocking the main thread.

- Concurrency takes those async tasks (and even synchronous ones that can be paused) and decides the order, priority, and interleaving of execution.

**_In React 18+ terms:_**

- Async: Your fetch call or setTimeout doesn’t block the UI.

- Concurrent Rendering: React can pause a low-priority render, continue handling urgent user input, and resume the paused render later.

- Tools like startTransition and useDeferredValue are how you define priority criteria.

**_Analogy:_**

- Async = “I’ll put this task on hold while other stuff continues.”

- Concurrency = “I have multiple tasks on hold; I’ll choose which one to run first so the UI feels fast.”

## 🔹 5. Key Takeaways

- **Event Loop**: The scheduler that makes async possible in JS.
- **Asynchronous (Non-blocking)**: Tasks can finish later without freezing the UI.
- **Concurrency**: Handling multiple tasks in progress; in React 18, rendering can be interrupted and resumed.

👉 Together, these concepts explain **why React apps feel responsive**, even with heavy async logic.

### Further Reading

[`What is Axios?`](./misc/what-is-axios.md)

---
