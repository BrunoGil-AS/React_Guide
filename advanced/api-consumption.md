# API Consumption in React: Fetch, Axios, and Best Practices

A comprehensive guide to making HTTP requests and handling responses in React applications.

> **Estimated reading time:** 10 min

## Overview

Making API calls is a fundamental part of most React applications. Modern web apps need to fetch data, handle loading and error states, and manage request cancellation. This guide covers the essential patterns for API consumption using both the native `fetch` API and Axios.

Understanding how to properly integrate API calls with React's component lifecycle is crucial for building robust applications. We'll explore best practices for data fetching, error handling, and request cancellation.

## Key Concepts

- **HTTP Methods**: GET, POST, PUT, DELETE for CRUD operations
- **Promise-based Requests**: Asynchronous operations returning promises
- **Loading States**: UI feedback while waiting for responses
- **Error Handling**: Graceful handling of API failures
- **Request Cancellation**: Cleaning up pending requests
- **Interceptors**: Middleware for request/response processing
- **AbortController**: Native API for request cancellation

## Examples

### 1. Data Fetching with useEffect and Cleanup

```jsx
import { useState, useEffect } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Create abort controller for cleanup
    const abortController = new AbortController();

    async function fetchUser() {
      try {
        setLoading(true);
        setError(null);

        const response = await fetch(
          `https://api.example.com/users/${userId}`,
          { signal: abortController.signal }
        );

        if (!response.ok) {
          throw new Error("User not found");
        }

        const data = await response.json();
        setUser(data);
      } catch (err) {
        if (err.name === "AbortError") {
          return; // Ignore abort errors
        }
        setError(err.message);
      } finally {
        setLoading(false);
      }
    }

    fetchUser();

    // Cleanup function
    return () => abortController.abort();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  if (!user) return null;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
    </div>
  );
}
```

### 2. Axios with Interceptors

```jsx
import axios from "axios";
import { useState, useEffect } from "react";

// Create axios instance
const api = axios.create({
  baseURL: "https://api.example.com",
});

// Add request interceptor
api.interceptors.request.use(
  (config) => {
    // Add auth header
    const token = localStorage.getItem("token");
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// Add response interceptor
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response.status === 401) {
      // Handle unauthorized
      localStorage.removeItem("token");
      window.location.href = "/login";
    }
    return Promise.reject(error);
  }
);

function PostList() {
  const [posts, setPosts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let mounted = true;

    async function fetchPosts() {
      try {
        setLoading(true);
        const { data } = await api.get("/posts");
        if (mounted) {
          setPosts(data);
        }
      } catch (err) {
        if (mounted) {
          setError(err.message);
        }
      } finally {
        if (mounted) {
          setLoading(false);
        }
      }
    }

    fetchPosts();

    return () => {
      mounted = false;
    };
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;

  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

## Example Explanations

1. The `fetch` example demonstrates a complete data fetching implementation with loading states, error handling, and cleanup. It uses `AbortController` to cancel pending requests when the component unmounts or when `userId` changes.

2. The Axios example shows how to create a configured instance with interceptors for common tasks like authentication and error handling. It also demonstrates proper cleanup using a mounted flag to prevent setting state after unmount.

## Common Pitfalls & Solutions

- **Memory Leaks**: Always cleanup pending requests in useEffect
- **Race Conditions**: Handle out-of-order responses with cleanup or request cancellation
- **Error Boundaries**: Implement error boundaries for graceful error handling
- **Network Failures**: Add retry logic for transient failures
- **State Updates**: Prevent setState on unmounted components
- **Cache Management**: Implement caching to prevent unnecessary requests
- **Loading States**: Show loading indicators for better UX

## Exercises

### Exercise 1: GitHub User Fetcher

Task: Build a component that fetches and displays GitHub user data with loading and error states.
Expected result: A component that shows a user's avatar, name, and bio, with proper loading indicator and error handling.

### Exercise 2: Cancelable Search

Task: Implement a search feature that cancels pending requests when the search term changes.
Expected result: A search input that fetches results but cancels in-flight requests when the user types, preventing race conditions.

## Further Reading

- [Axios Documentation](https://axios-http.com/docs/intro)
- [Using Fetch - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)
- [React Query Documentation](https://react-query.tanstack.com/)
- [SWR - React Hooks for Data Fetching](https://swr.vercel.app/)
