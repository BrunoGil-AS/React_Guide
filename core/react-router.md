# React Router

Master client-side routing in React applications using React Router v6.

> **Estimated reading time:** 6 min

## Overview

React Router enables navigation and routing in single-page applications (SPAs) without full page reloads. Version 6 introduces a simpler, more intuitive API with powerful features like nested routes, dynamic parameters, and programmatic navigation.

The library follows React's component-based philosophy, making routing declarative and enabling you to think about navigation in terms of UI composition rather than URL manipulation.

## Key Concepts

- **BrowserRouter**: Provider component that enables routing functionality
- **Routes & Route**: Components that define your application's routing structure
- **Link**: Component for declarative navigation without page reloads
- **useNavigate**: Hook for programmatic navigation
- **Route Parameters**: Dynamic segments in URLs (e.g., /users/:id)
- **Nested Routes**: Child routes that share UI elements with parent routes

## Examples

### 1. Basic Route Setup

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

> The basic route setup demonstrates the fundamental Router components and how to create a simple navigation structure. The `*` path catches any unmatched routes for 404 handling.

### 2. Programmatic Navigation

```jsx
import { useNavigate } from "react-router-dom";

function LoginForm() {
  const navigate = useNavigate();
  const [formData, setFormData] = useState({
    username: "",
    password: "",
  });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await loginUser(formData);
      // Redirect after successful login
      navigate("/dashboard");
    } catch (error) {
      // Handle error
    }
  };

  return <form onSubmit={handleSubmit}>{/* form fields */}</form>;
}
```

> The programmatic navigation example shows how to use the `useNavigate` hook for navigation after form submissions or other events, providing a way to move between routes from within your component logic.

### 3. Nested Routes with Layout

```jsx
import { Outlet, Link, useParams } from "react-router-dom";

// Parent layout component
function DashboardLayout() {
  return (
    <div>
      <header>
        <h1>Dashboard</h1>
        <nav>
          <Link to="/dashboard/profile">Profile</Link>
          <Link to="/dashboard/settings">Settings</Link>
        </nav>
      </header>

      {/* Renders the child route's element */}
      <Outlet />
    </div>
  );
}

// App.jsx with nested routes
function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<DashboardLayout />}>
          <Route index element={<DashboardHome />} />
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

> The nested routes example illustrates how to create a shared layout with the Outlet component, allowing child routes to render within a parent layout while maintaining their own content.

## Common Pitfalls & Solutions

- **Missing BrowserRouter**: Always wrap your app with BrowserRouter at the root
- **Route Ordering**: More specific routes should come before less specific ones
- **Hash vs Browser Router**: Use HashRouter for static file hosting without server configuration
- **Memory Leaks**: Clean up subscriptions when navigating away from routes
- **404 Handling**: Always include a catch-all route for unmatched paths

## Exercises

1. **Protected Routes**

   - Task: Create a PrivateRoute component that redirects to /login if user is not authenticated
   - Expected Solution: Create a wrapper component that checks auth state and uses Navigate component to redirect

2. **Dynamic Routes**
   - Task: Implement a product detail page using route parameters (/products/:id)
   - Expected Solution: Use useParams hook to access the id parameter and fetch product details

## Further Reading

- [React Router Documentation](https://reactrouter.com/docs/en/v6)
- [React Router Tutorial](https://reactrouter.com/docs/en/v6/getting-started/tutorial)
- [Understanding React Router 6](https://ui.dev/react-router-tutorial)
