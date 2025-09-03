# React Forms

Master form handling in React, from basic controlled inputs to complex form management.

> **Estimated reading time:** 7 min

## Overview

Forms in React can be implemented using either controlled or uncontrolled components. Controlled components maintain form data in state, providing more control and immediate access to form values. Uncontrolled components rely on DOM refs and are useful for simple forms or when integrating with non-React code.

The choice between controlled and uncontrolled components depends on your specific needs for form validation, real-time feedback, and data handling.

## Key Concepts

- **Controlled Components**: Form elements whose values are controlled by React state
- **Uncontrolled Components**: Form elements that maintain their own state in the DOM
- **Form Validation**: Client-side validation of user input
- **Form Submission**: Handling form submission and preventing default behavior
- **useRef**: Hook for accessing DOM elements directly
- **Form Libraries**: Solutions like React Hook Form for complex form management

## Examples

### 1. Controlled Form

```jsx
import { useState } from "react";

function SignupForm() {
  const [formData, setFormData] = useState({
    username: "",
    email: "",
    password: "",
  });
  const [errors, setErrors] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({
      ...prev,
      [name]: value,
    }));

    // Clear error when user types
    if (errors[name]) {
      setErrors((prev) => ({
        ...prev,
        [name]: "",
      }));
    }
  };

  const validate = () => {
    const newErrors = {};

    if (!formData.username) {
      newErrors.username = "Username is required";
    }

    if (!formData.email) {
      newErrors.email = "Email is required";
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = "Email is invalid";
    }

    if (!formData.password) {
      newErrors.password = "Password is required";
    } else if (formData.password.length < 6) {
      newErrors.password = "Password must be at least 6 characters";
    }

    return newErrors;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const newErrors = validate();

    if (Object.keys(newErrors).length === 0) {
      // Form is valid, submit data
      console.log("Form submitted:", formData);
    } else {
      setErrors(newErrors);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
        {errors.username && <span className="error">{errors.username}</span>}
      </div>

      <div>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>

      <div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>

      <button type="submit">Sign Up</button>
    </form>
  );
}
```

> This example demonstrates a controlled form implementation with comprehensive validation:
>
> - Uses `useState` to manage both form data and error states
> - Implements real-time error clearing when users type
> - Contains field-level validation for username, email, and password
> - Shows error messages below each field when validation fails
> - Prevents form submission if any validation errors exist

### 2. Uncontrolled Form with useRef

```jsx
import { useRef } from "react";

function LoginForm() {
  const emailRef = useRef();
  const passwordRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();

    const formData = {
      email: emailRef.current.value,
      password: passwordRef.current.value,
    };

    console.log("Form submitted:", formData);
    // Reset form
    e.target.reset();
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input type="email" ref={emailRef} placeholder="Email" required />
      </div>

      <div>
        <input
          type="password"
          ref={passwordRef}
          placeholder="Password"
          required
        />
      </div>

      <button type="submit">Log In</button>
    </form>
  );
}
```

> This example shows a simpler uncontrolled form approach:
>
> - Uses `useRef` hooks to directly access input DOM nodes
> - Collects form data only when the form is submitted
> - Demonstrates a more performant approach for simple forms
> - Shows how to reset the form using the native form reset method
> - Requires less code than controlled components while maintaining functionality

### 3. File Input Handling

```jsx
function FileUploadForm() {
  const [file, setFile] = useState(null);
  const [preview, setPreview] = useState("");

  const handleFileChange = (e) => {
    const selectedFile = e.target.files[0];

    if (selectedFile) {
      setFile(selectedFile);
      // Create preview URL for images
      if (selectedFile.type.startsWith("image/")) {
        const reader = new FileReader();
        reader.onloadend = () => {
          setPreview(reader.result);
        };
        reader.readAsDataURL(selectedFile);
      }
    }
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!file) return;

    const formData = new FormData();
    formData.append("file", file);

    try {
      // Example upload
      const response = await fetch("/api/upload", {
        method: "POST",
        body: formData,
      });
      // Handle response
    } catch (error) {
      // Handle error
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="file" onChange={handleFileChange} accept="image/*" />

      {preview && (
        <img src={preview} alt="Preview" style={{ maxWidth: "200px" }} />
      )}

      <button type="submit" disabled={!file}>
        Upload
      </button>
    </form>
  );
}
```

> This example showcases file upload handling with image preview:
>
> - Manages file selection and preview state with `useState`
> - Creates image previews using FileReader API
> - Demonstrates proper FormData usage for file uploads
> - Shows how to handle asynchronous file submission
> - Includes basic error handling and loading states
> - Implements conditional rendering for the image preview

## Explanation of Examples

The controlled form example demonstrates complete form control with validation. It shows how to:

- Manage form state with useState
- Handle changes for multiple inputs
- Implement validation logic
- Display error messages
- Process form submission

The uncontrolled form example shows a simpler approach using refs. It's useful when you:

- Don't need immediate validation
- Want to reduce re-renders
- Are integrating with non-React code

The file upload example illustrates handling file inputs and previews, demonstrating:

- File selection handling
- Image preview generation
- FormData usage for uploads

## Common Pitfalls & Solutions

- **Unnecessary Re-renders**: Use controlled components only when needed
- **Memory Leaks**: Clean up file preview URLs with useEffect
- **Form Reset**: Remember to reset both state and refs after submission
- **Validation Timing**: Choose between onChange vs onBlur validation
- **Large Forms**: Consider React Hook Form for complex forms

## Exercises

1. **Contact Form**

   - Task: Create a contact form with name, email, subject, and message fields, including validation
   - Expected Solution: Implement as a controlled form with validation for all fields, error messages, and loading state during submission

2. **React Hook Form Migration**
   - Task: Convert the signup form to use React Hook Form
   - Expected Solution: Install react-hook-form, use useForm hook, register inputs, and implement the same validation rules with less code

## Further Reading

- [React Forms Documentation](https://reactjs.org/docs/forms.html)
- [React Hook Form Documentation](https://react-hook-form.com/)
- [Form Validation in React](https://www.smashingmagazine.com/2020/10/react-validation-formik-yup/)
- [Uncontrolled Components](https://reactjs.org/docs/uncontrolled-components.html)

---
