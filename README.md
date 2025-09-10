# React Guide

A concise, didactic guide to the core concepts you need to learn React. This repository contains a focused README with introductory explanations for **JSX** and **Components**, and links to dedicated `.md` files for deeper study of the remaining topics.

---

## Table of Contents

- [JSX (JavaScript + XML)](#jsx-javascript--xml)
- [Components](#components)

---

## JSX (JavaScript + XML)

**What it is.** JSX is a syntax extension that lets you write HTML-like markup inside JavaScript. It is not required, but it is the most common and readable way to define React UI.

**Why use it.** JSX improves clarity by colocating markup and logic, and it compiles to `React.createElement` calls at build time.

**Basic rules & tips:**

- Expressions are placed inside `{}`: `{title}` or `{user.name}`.
- Use `className` instead of `class` for CSS classes.
- JSX must return a single root element (wrap siblings in a fragment `<>...</>`).
- Attribute names are camelCased (e.g. `tabIndex`, `aria-label`).

**Small example:**

```jsx
const name = "Bruno";
const element = <h1>Hello, {name}!</h1>;

function Card({ title, children }) {
  return (
    <section className="card">
      <h3>{title}</h3>
      <div>{children}</div>
    </section>
  );
}
```

**Learn more**: Official React docs on JSX — [https://reactjs.org/docs/introducing-jsx.html](https://reactjs.org/docs/introducing-jsx.html)

---

## Components

**_Definition:_**

Components are the building blocks of a React application. They are reusable pieces of UI that can be functional (functions) or class-based (ES6 classes). In modern React, functional components with hooks are preferred.

**_When & Why to Use:_**

- Break your UI into smaller, reusable pieces.
- Improve readability and maintainability.
- Separate logic from presentation.

### Functional components (recommended)

- Implemented as plain JavaScript functions.
- Use **hooks** (like `useState`) to manage local state and side effects.

```jsx
import { useState } from "react";

function Counter({ initial = 0 }) {
  const [count, setCount] = useState(initial);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount((c) => c + 1)}>+1</button>
    </div>
  );
}

export default Counter;
```

### Class components (Legacy)

- Use `extends React.Component` and `this.state`.
- Now less common, but still used in older codebases.

```jsx
import React, { Component } from "react";

class Greeting extends Component {
  render() {
    const { name } = this.props;
    return <h2>Hello, {name}</h2>;
  }
}

export default Greeting;
```

### Props (component inputs)

**_Definition:_**

Props are inputs to components. They allow you to pass data from a parent component to a child component. Props are read-only inside the child component.

- Props are read-only values passed from parent to child.
- Encourage small, focused components that receive data and callbacks via props.

**_When & Why to Use:_**

- To customize or configure a component without hardcoding data.
- To make components reusable with different data.
- To maintain unidirectional data flow (top-down).

```jsx
// Greeting.jsx
import React from "react";

function Greeting({ name, age }) {
  // Destructure props directly in the parameter
  return (
    <div>
      <h2>Hello, {name}!</h2>
      <p>You are {age} years old.</p>
    </div>
  );
}

export default Greeting;
```

_Using Props in Parent Component:_

```jsx
// App.jsx
import React from "react";
import Greeting from "./Greeting";

function App() {
  return (
    <div>
      <Greeting name="Bruno" age={26} />
      <Greeting name="Alice" age={30} />
    </div>
  );
}

export default App;
```

> **_Notes & Best Practices:_**
>
> - Props are read-only:
>   - Child components should not modify them.
>   - If you need to change them, lift state up.
> - Destructuring props in the function parameter is cleaner than props.name inside the function.
> - Default props: You can set default values if a prop isn’t provided:

### State (basic with `useState`)

- `useState` returns a state value and a setter function: `[state, setState]`.
- Keep state minimal and lift it up to a common parent when multiple components must share it.

**Quick tips:**

- Prefer functional components + hooks for new code.
- Keep components small and single-purpose.
- Use composition (children, render props) rather than deep prop drilling when possible.

**Learn more**: Official React docs on components — [https://reactjs.org/docs/components-and-props.html](https://reactjs.org/docs/components-and-props.html)

---

## Further Reading

### Miscellaneous

- [`React Versions`](./misc/versions-react.md)
- [Hooks and Selectors](./advanced/advanced-hooks.md#hooks-vs-selectors-and-their-relationship-to-redux)
- [`Best practices in React Events`](./core/events.md#3-best-practices-in-react-events)

#### JavaScript Related Topics

- [`Callback function and Listener`](./js/functions/listener-callback.md)
- [`Event Loop`](./js/event-loop.md)
- [`What is Axios?`](./js/misc/what-is-axios.md)

### Core React topics

- [`Hooks (useState, useEffect, useContext)`](./core/hooks.md)
- [`Events`](./core/events.md)
- [`Conditional rendering & lists`](./core/conditional-and-lists.md)
- [`Styling options (CSS Modules, styled-components, Tailwind)`](./core/styling.md)
- [`Routing (React Router)`](./core/react-router.md)
- [`Forms (controlled & uncontrolled)`](./core/forms.md)

### Advanced topics

- [`Advanced hooks (useReducer, useCallback, useMemo)`](./advanced/advanced-hooks.md)
- [`State management (Context, Redux, Zustand)`](./advanced/state-management.md)
- [`API consumption (fetch, axios)`](./advanced/apis.md)
- [`Patterns & best practices`](./advanced/patterns.md)
- [`Testing (Jest, React Testing Library)`](./advanced/testing.md)
- [`Performance (code splitting, lazy loading)`](./advanced/performance.md)
- [`TypeScript with React`](./advanced/typescript.md)

### Tools & ecosystem

- [`Node.js & npm / yarn / pnpm`](./tools/node-and-pm.md)
- [`Project starters (Vite, Create React App, Next.js)`](./tools/starters.md)
- [`Git & GitHub`](./tools/git.md)
- [`ESLint & Prettier`](./tools/linting.md)

---
