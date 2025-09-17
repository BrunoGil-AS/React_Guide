# **Error and Exception Handling in React**

## **1. Definition**

- **Error Handling:** The process of responding to runtime errors or unexpected conditions in a program, preventing crashes, and providing feedback.
- **Exception Handling:** In JavaScript/React, exceptions are errors thrown during execution. Exception handling is done using `try/catch` blocks or React-specific mechanisms.
- **In React:** Error handling has two main layers:

  1. **JavaScript-level**: Handling exceptions inside event handlers or async calls.
  2. **React Component-level**: Handling errors in rendering, lifecycle, or children components using **Error Boundaries**.

---

## **2. Why It’s Needed**

- Prevents app crashes and blank screens when a component fails.
- Allows graceful UI degradation and error reporting.
- Improves debugging by logging errors with context.
- Helps in asynchronous tasks like API calls (fetch/axios) to handle failed requests.

---

## **3. JavaScript-Level Error Handling in React**

### **3.1 Using `try/catch` in Event Handlers**

```jsx
import React, { useState } from "react";

function App() {
  const [message, setMessage] = useState("");

  const handleClick = () => {
    try {
      // simulate error
      throw new Error("Something went wrong!");
    } catch (error) {
      console.error("Caught error:", error);
      setMessage(error.message);
    }
  };

  return (
    <div>
      <button onClick={handleClick}>Trigger Error</button>
      {message && <p>Error Message: {message}</p>}
    </div>
  );
}

export default App;
```

**Explanation:**

- `try/catch` captures errors thrown in the event handler.
- `console.error` logs the error for debugging.
- `setMessage` updates the UI with error info.

---

### **3.2 Handling Async Errors (API Calls)**

```jsx
import React, { useState } from "react";
import axios from "axios";

function DataFetcher() {
  const [data, setData] = useState(null);
  const [error, setError] = useState("");

  const fetchData = async () => {
    try {
      const response = await axios.get(
        "https://jsonplaceholder.typicode.com/users"
      );
      setData(response.data);
    } catch (err) {
      console.error("API Error:", err);
      setError("Failed to fetch data");
    }
  };

  return (
    <div>
      <button onClick={fetchData}>Fetch Data</button>
      {error && <p>{error}</p>}
      {data && <pre>{JSON.stringify(data, null, 2)}</pre>}
    </div>
  );
}

export default DataFetcher;
```

**Key Notes:**

- Always `try/catch` async operations.
- State updates inside `catch` allow displaying errors in UI.

---

## **4. Component-Level Error Handling (Error Boundaries)**

### **4.1 What is an Error Boundary?**

- A **React component** that catches **rendering errors in its children**.
- Prevents the whole app from crashing.
- Only works for **class components** (React 18+ still doesn’t support functional component error boundaries natively).

---

### **4.2 Creating an Error Boundary**

```jsx
import React, { Component } from "react";

// Error Boundary Component
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, errorMessage: "" };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render shows fallback UI
    return { hasError: true, errorMessage: error.message };
  }

  componentDidCatch(error, errorInfo) {
    // Log error to external service
    console.error("Error Boundary caught:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h2>Something went wrong: {this.state.errorMessage}</h2>;
    }

    return this.props.children;
  }
}

export default ErrorBoundary;
```

---

### **4.3 Using Error Boundary**

```jsx
import React from "react";
import ErrorBoundary from "./ErrorBoundary";

function BuggyComponent() {
  // Simulate a rendering error
  throw new Error("I crashed!");
  return <div>This will never render</div>;
}

function App() {
  return (
    <ErrorBoundary>
      <BuggyComponent />
    </ErrorBoundary>
  );
}

export default App;
```

**Key Notes:**

- Catches errors in **child components**, not in itself.
- You can wrap specific sections of the app instead of the whole app.

---

### **4.4 Fallback UI with Hooks (React 18+ Approach)**

While error boundaries must be classes, you can **combine them with state/hooks**:

```jsx
function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
    </ErrorBoundary>
  );
}
```

- `Suspense` + Error Boundaries handle **async errors during lazy loading**.

---

## **5. Best Practices**

1. **Don’t overuse try/catch** in rendering—let error boundaries handle it.
2. **Use Error Boundaries at high-level** (App, Layout) but also selectively for critical sections.
3. **Log errors externally** (Sentry, LogRocket) for production monitoring.
4. **Use fallback UI** to maintain user experience.
5. **Async errors** always need `try/catch` or `.catch()` on promises.

---

## **6. Bonus: Error Handling with React Router**

```jsx
import { BrowserRouter, Routes, Route } from "react-router-dom";

<BrowserRouter>
  <ErrorBoundary>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  </ErrorBoundary>
</BrowserRouter>;
```

- Wrapping routes prevents the entire app from crashing if a page component fails.

---

## **7. Quick Reference Table**

| Scenario                    | Recommended Handling                   |
| --------------------------- | -------------------------------------- |
| Event handler errors        | `try/catch` inside handler             |
| Async/API errors            | `try/catch` + state update             |
| Rendering errors            | Error Boundary (class component)       |
| Lazy-loaded component error | `Suspense` + Error Boundary            |
| Global logging              | `componentDidCatch` / external logging |

---

✅ **Summary:**

- React separates **JS errors** and **component render errors**.
- **Error Boundaries** are essential for render-time safety.
- **try/catch** is used for events and async tasks.
- Use **fallback UIs**, **logging**, and **selective boundaries** for robust error handling.

---
