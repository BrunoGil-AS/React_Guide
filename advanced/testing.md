# Testing React Applications with Jest and React Testing Library

A practical guide to testing React components using modern testing tools and best practices.

> **Estimated reading time:** 10 min

## Overview

Testing is a crucial part of building reliable React applications. This guide focuses on using Jest as the test runner and React Testing Library for component testing. These tools encourage testing your applications in the way users actually use them, focusing on behavior over implementation details.

We'll cover unit testing components, mocking API calls, and testing user interactions. The emphasis is on writing maintainable tests that give you confidence in your application's behavior.

## Key Concepts

- **Jest**: A delightful JavaScript testing framework
- **React Testing Library**: Library for testing React components
- **Unit Tests**: Testing individual components in isolation
- **Integration Tests**: Testing multiple components working together
- **Mocking**: Replacing real implementations for testing
- **User Events**: Simulating user interactions
- **Query Methods**: Different ways to find elements in the DOM
- **Assertions**: Verifying expected outcomes

## Examples

### 1. Basic Component Test

```jsx
// Button.jsx
function Button({ onClick, children }) {
  return (
    <button onClick={onClick} className="primary-button">
      {children}
    </button>
  );
}

// Button.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";

describe("Button", () => {
  test("renders with correct text", () => {
    render(<Button>Click me</Button>);

    const button = screen.getByText("Click me");
    expect(button).toBeInTheDocument();
  });

  test("calls onClick when clicked", () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click me</Button>);

    const button = screen.getByText("Click me");
    fireEvent.click(button);

    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

### 2. Form Submission Test

```jsx
// LoginForm.jsx
import { useState } from "react";

function LoginForm({ onSubmit }) {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <label htmlFor="email">Email</label>
      <input
        id="email"
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Enter email"
      />

      <label htmlFor="password">Password</label>
      <input
        id="password"
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Enter password"
      />

      <button type="submit">Login</button>
    </form>
  );
}

// LoginForm.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import userEvent from "@testing-library/user-event";

describe("LoginForm", () => {
  test("submits form with user credentials", async () => {
    const handleSubmit = jest.fn();
    render(<LoginForm onSubmit={handleSubmit} />);

    // Get form elements
    const emailInput = screen.getByLabelText(/email/i);
    const passwordInput = screen.getByLabelText(/password/i);
    const submitButton = screen.getByText(/login/i);

    // Fill out the form
    await userEvent.type(emailInput, "test@example.com");
    await userEvent.type(passwordInput, "password123");

    // Submit the form
    fireEvent.click(submitButton);

    // Assert
    expect(handleSubmit).toHaveBeenCalledWith({
      email: "test@example.com",
      password: "password123",
    });
  });
});
```

## Example Explanations

1. The Button test demonstrates simple component testing: rendering a component and verifying both its content and behavior. It uses Jest's mock functions to verify the onClick handler is called.

2. The LoginForm test shows a more complex example involving form interactions. It demonstrates how to test form submissions, type into inputs, and verify the correct data is submitted. It uses `userEvent` for more realistic user interaction simulation.

## Common Pitfalls & Solutions

- **Implementation Details**: Test behavior, not implementation
- **Snapshot Tests**: Use sparingly, prefer explicit assertions
- **Query Selection**: Use accessible queries (getByRole, getByLabelText) over getByTestId
- **Async Operations**: Always await async actions and events
- **Setup/Teardown**: Clean up after tests using afterEach
- **Mock Data**: Use factories or fixtures for consistent test data
- **Over-mocking**: Only mock what's necessary

## Exercises

### Exercise 1: Toggle Button Test

Task: Write tests for a toggle button component that switches between two states.
Expected result: Tests that verify the button toggles correctly and displays the appropriate text/state.

### Exercise 2: API Mock Test

Task: Test a component that fetches and displays data from an API.
Expected result: Tests that verify loading states, successful data display, and error handling using mocked API calls.

## Further Reading

- [React Testing Library Documentation](https://testing-library.com/docs/react-testing-library/intro/)
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Common Testing Mistakes](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library)
- [Testing JavaScript with Kent C. Dodds](https://testingjavascript.com/)
