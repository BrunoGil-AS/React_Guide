## 1. **What is Axios?**

- **Definition:** Axios is a **popular JavaScript library** for making HTTP requests (GET, POST, PUT, DELETE).
- **Why use it:**

  - Returns **Promises**, making async code clean with `.then()` or `async/await`.
  - Automatically parses **JSON** responses.
  - Handles **request cancellation**, **timeouts**, and **interceptors**.
  - Works both in **browser** and **Node.js**.

Compared to `fetch`:

| Feature               | Axios     | Fetch                          |
| --------------------- | --------- | ------------------------------ |
| JSON parsing          | Automatic | Requires `res.json()`          |
| Timeout               | Built-in  | Requires manual setup          |
| Request cancel        | Built-in  | Requires AbortController       |
| Older browser support | Yes       | Needs polyfill in old browsers |

---

## 2. **Installing Axios**

```bash
npm install axios
# or
yarn add axios
```

---

## 3. **Basic Usage in React**

### GET Request

```jsx
import { useState, useEffect } from "react";
import axios from "axios";

function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get(
          "https://jsonplaceholder.typicode.com/users"
        );
        setUsers(response.data); // Axios automatically parses JSON
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

export default Users;
```

---

### POST Request Example

```jsx
const createUser = async (newUser) => {
  try {
    const response = await axios.post("/api/users", newUser);
    console.log("User created:", response.data);
  } catch (err) {
    console.error(err);
  }
};
```

---

## 4. **Why Axios is often preferred in React**

- Cleaner syntax than `fetch` (no need to call `res.json()` every time).
- Supports **interceptors**, so you can automatically attach tokens or handle errors globally.
- Works seamlessly with `async/await` and React hooks (`useEffect`, `useState`).

---
