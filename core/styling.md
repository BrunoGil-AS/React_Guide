# Styling in React

Learn different approaches to styling React components, from traditional CSS to modern solutions like CSS Modules, Styled Components, and Tailwind CSS.

> **Estimated reading time:** 7 min

## Overview

React offers multiple ways to style components, each with its own advantages and trade-offs. From traditional global CSS to modern CSS-in-JS solutions and utility-first frameworks, choosing the right styling approach depends on your project's needs, team size, and performance requirements.

Understanding these different approaches helps you make informed decisions about styling architecture and maintainability in your React applications.

## Key Concepts

- **Global CSS**: Traditional CSS files imported into components
- **CSS Modules**: Locally scoped CSS files that prevent naming conflicts
- **CSS-in-JS**: JavaScript-based styling solutions like Styled Components
- **Utility Classes**: Functional CSS approach used by Tailwind CSS
- **Inline Styles**: React's built-in style prop for dynamic styles

## Examples

### 1. CSS Modules

```jsx
// Button.module.css
.button {
  padding: 8px 16px;
  border-radius: 4px;
  border: none;
  cursor: pointer;
}

.primary {
  background: #0066cc;
  color: white;
}

.secondary {
  background: #f0f0f0;
  color: #333;
}

// Button.jsx
import styles from './Button.module.css';

function Button({ variant = 'primary', children }) {
  return (
    <button
      className={`${styles.button} ${styles[variant]}`}
    >
      {children}
    </button>
  );
}
```

### 2. Styled Components

```jsx
import styled from "styled-components";

const Card = styled.div`
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);

  // Props-based styling
  background: ${(props) => (props.theme === "dark" ? "#333" : "#fff")};
  color: ${(props) => (props.theme === "dark" ? "#fff" : "#333")};

  // Nested selectors
  &:hover {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  }

  // Child elements
  h2 {
    margin-top: 0;
    font-size: 1.5em;
  }
`;

function ArticleCard({ title, content, theme }) {
  return (
    <Card theme={theme}>
      <h2>{title}</h2>
      <p>{content}</p>
    </Card>
  );
}
```

### 3. Tailwind CSS

```jsx
function ProductCard({ product }) {
  return (
    <div className="rounded-lg shadow-md p-6 hover:shadow-lg transition-shadow">
      <img
        src={product.image}
        alt={product.name}
        className="w-full h-48 object-cover rounded-md"
      />
      <h3 className="mt-4 text-xl font-semibold text-gray-800">
        {product.name}
      </h3>
      <p className="mt-2 text-gray-600">{product.description}</p>
      <div className="mt-4 flex justify-between items-center">
        <span className="text-2xl font-bold text-blue-600">
          ${product.price}
        </span>
        <button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
          Add to Cart
        </button>
      </div>
    </div>
  );
}
```

## Explanation of Examples

The CSS Modules example demonstrates locally scoped CSS with composition. It shows how to create reusable, modular styles that won't conflict with other components, while maintaining the familiar CSS syntax.

The Styled Components example shows how to create styled elements with dynamic props-based styling. It demonstrates the power of CSS-in-JS with template literals and component-based styling logic.

The Tailwind example illustrates utility-first CSS, where styles are composed through class names. It shows how to build complex layouts and responsive designs without writing custom CSS.

## Common Pitfalls & Solutions

- **CSS Specificity**: Use CSS Modules or styled-components to avoid specificity wars
- **Performance**: Be cautious with dynamic styles in styled-components; prefer static styles when possible
- **Bundle Size**: Configure proper tree-shaking for CSS-in-JS libraries
- **Maintainability**: Don't mix different styling approaches without good reason
- **SSR**: Ensure your styling solution works with server-side rendering if needed

## Exercises

1. **CSS Module Migration**

   - Task: Convert a button component from global CSS to CSS Modules, including hover states and variants.
   - Expected Solution: Create a .module.css file, import styles, and use dynamic className composition.

2. **Tailwind Card Component**
   - Task: Build a responsive card component with Tailwind CSS that includes image, title, description, and hover effects.
   - Expected Solution: Use Tailwind's utility classes for layout, spacing, and interactivity.

## Further Reading

- [CSS Modules Documentation](https://github.com/css-modules/css-modules)
- [Styled Components Documentation](https://styled-components.com/docs)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [A Complete Guide to Styling in React](https://www.joshwcomeau.com/css/styled-components/)
