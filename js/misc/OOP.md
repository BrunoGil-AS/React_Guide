# **OOP in React: Where it fits**

React historically supported **two main ways to define components**:

1. **Class Components** – This is where OOP is most directly applied:

   ```jsx
   import React, { Component } from "react";

   class Counter extends Component {
     constructor(props) {
       super(props);
       this.state = { count: 0 };
     }

     increment = () => {
       this.setState({ count: this.state.count + 1 });
     };

     render() {
       return (
         <div>
           <p>Count: {this.state.count}</p>
           <button onClick={this.increment}>Increment</button>
         </div>
       );
     }
   }

   export default Counter;
   ```

   **OOP features used here**:

   - **Encapsulation**: State is private to the class (`this.state`).
   - **Inheritance**: React provides `Component` as a base class.
   - **Methods**: `increment()` is an object method.

2. **Functional Components + Hooks** – This is the modern React standard (React 16.8+):

   ```jsx
   import { useState } from "react";

   function Counter() {
     const [count, setCount] = useState(0);

     const increment = () => setCount(count + 1);

     return (
       <div>
         <p>Count: {count}</p>
         <button onClick={increment}>Increment</button>
       </div>
     );
   }

   export default Counter;
   ```

   Here, **OOP is not used explicitly**, but you achieve the same behavior using **stateful functions and closures**. React encourages **composition over inheritance**, which is a shift away from traditional OOP.

---

## 2. **Is OOP useful in React?**

- **Class components are still valid**, so OOP concepts like inheritance, methods, and encapsulation can be used.
- **Modern React favors composition and hooks**, which are often more flexible than classical OOP patterns.
- **Use OOP when**:

  - You are porting an old codebase.
  - You want to model complex objects with methods (e.g., a game character, physics simulation, or a library for internal use).

- **Avoid OOP-heavy patterns when**:

  - Building standard UI components.
  - Using state, effects, and context – hooks and functional patterns are simpler and more idiomatic.

---

## 3. **Hybrid approach**

Even in functional components, you can still use OOP **internally**, for example to model data or utility classes:

```jsx
class User {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hello, ${this.name}!`;
  }
}

function UserCard({ name }) {
  const user = new User(name);
  return <p>{user.greet()}</p>;
}
```

This keeps **data modeling OOP-style** but the React components remain functional and idiomatic.

---

✅ **Bottom line**:

- React **supports OOP**, but modern best practices lean towards **functional components + hooks + composition**.
- OOP can still be useful for modeling domain-specific objects inside your React app, but rarely for the UI components themselves.

---

## 1. **Encapsulation**

- **OOP idea:** Keep internal state and methods private inside a class, exposing only what’s necessary.
- **React Class Components:** Encapsulation exists naturally:

```jsx
class Counter extends React.Component {
  #privateValue = 42; // ES2020 private field
  state = { count: 0 };

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return <button onClick={this.increment}>{this.state.count}</button>;
  }
}
```

- `#privateValue` is truly private.

- `state` is encapsulated; outside components **cannot modify it directly**.

- **Functional Components:** No `private` fields, but you achieve encapsulation via **closures**:

```jsx
function Counter() {
  const [count, setCount] = useState(0);
  let privateValue = 42; // not accessible outside the function scope

  const increment = () => setCount(count + 1);

  return <button onClick={increment}>{count}</button>;
}
```

- `privateValue` is hidden because it only exists in the component’s closure.

---

## 2. **Abstraction**

- **OOP idea:** Expose only necessary interfaces, hide complexity.
- **React usage:** Create **reusable components** with clean props interface:

```jsx
function Button({ label, onClick }) {
  // implementation details hidden inside
  return <button onClick={onClick}>{label}</button>;
}
```

- Consumers of `Button` don’t care about the internal JSX or styles.
- Hooks can also abstract logic:

```jsx
function useCounter(initial = 0) {
  const [count, setCount] = useState(initial);
  const increment = () => setCount((c) => c + 1);
  return { count, increment };
}
```

---

## 3. **Inheritance**

- **OOP idea:** Share code via class hierarchies.
- **React usage:**

  - **Class components**: Can extend `React.Component`. Beyond that, React avoids deep hierarchies.
  - **Functional components**: Prefer **composition** over inheritance:

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

function ProfileCard() {
  return (
    <Card>
      <p>Profile info</p>
    </Card>
  );
}
```

### 3.1 **Diamond Problem recap**

In classic OOP:

```
     A
    / \
   B   C
    \ /
     D
```

- Class `D` inherits from `B` and `C`.
- Both `B` and `C` inherit from `A`.
- **Problem**: If `A` has a method `foo()`, which version does `D` inherit if `B` and `C` override it? This is the "diamond problem".

---

### 3.2 **JavaScript inheritance**

JS **does not support multiple inheritance** for classes. You **can only extend one class**:

```js
class A {
  foo() {
    console.log("A");
  }
}
class B extends A {
  foo() {
    console.log("B");
  }
}
class C extends B {
  foo() {
    console.log("C");
  }
}

const obj = new C();
obj.foo(); // C
```

- There’s **no diamond problem** here because JS prevents multiple class inheritance. You cannot do `class D extends B, C` — it’s a syntax error.

---

### 3.3 **Workarounds / patterns**

Instead of multiple inheritance, JS uses **mixins** or **composition**, which avoid the diamond problem:

```js
const sayHelloMixin = (Base) =>
  class extends Base {
    sayHello() {
      console.log("Hello");
    }
  };

const sayByeMixin = (Base) =>
  class extends Base {
    sayBye() {
      console.log("Bye");
    }
  };

class Base {}
class MyClass extends sayByeMixin(sayHelloMixin(Base)) {}

const obj = new MyClass();
obj.sayHello(); // Hello
obj.sayBye(); // Bye
```

- Order matters in mixins. If two mixins define the same method, the **last applied mixin wins**, which is **deterministic**, not ambiguous like the diamond problem.

---

### 3.4 **React context**

In **React**, you usually **don’t use class inheritance** for UI components:

- Functional components + hooks replace complex inheritance hierarchies.
- Composition replaces multiple inheritance:

```jsx
function Button({ children }) {
  return <button>{children}</button>;
}

function IconButton(props) {
  return <Button {...props}>⭐ {props.children}</Button>;
}
```

- Here, no diamond problem arises. You **compose behaviors**, instead of inheriting from multiple base components.

---

✅ **Summary:**

- JavaScript **does not have the diamond problem**, because it doesn’t allow multiple inheritance.
- Conflicts only happen with **mixins**, but they are resolved in a clear linear order.
- React **avoids inheritance-heavy patterns**, so this problem almost never occurs in practice.

---

## 4. **Polymorphism**

- **OOP idea:** Same interface, different implementations.
- **React usage:** Achieved with **props** or **render props / hooks**:

```jsx
function Button({ variant, label }) {
  const style =
    variant === "primary" ? { color: "white", background: "blue" } : {};
  return <button style={style}>{label}</button>;
}
```

- Same `Button` interface (`label` prop), but behavior/styles change based on props.

---

## 5. **Composition > Inheritance**

- React **intentionally avoids OOP-heavy hierarchies**.
- Preferred approach:

  - Reusable UI: Small functional components
  - Shared logic: Hooks (`useCounter`, `useAuth`)
  - Shared UI wrapper: Higher-order components or render props (if needed)

```jsx
function withLoading(Component) {
  return function Wrapped({ isLoading, ...props }) {
    if (isLoading) return <p>Loading...</p>;
    return <Component {...props} />;
  };
}
```

---

### ✅ **Summary Table**

| OOP Concept   | React Class Components    | React Functional Components | Notes                              |
| ------------- | ------------------------- | --------------------------- | ---------------------------------- |
| Encapsulation | `state` + private fields  | Closures + `useState`       | Works in both styles               |
| Abstraction   | Methods + props           | Props + hooks               | Preferred functional pattern       |
| Inheritance   | `extends React.Component` | Composition                 | Composition > inheritance          |
| Polymorphism  | Overriding methods        | Props or render props       | Same interface, different behavior |
| Reusability   | Methods in base classes   | Custom hooks / HOCs         | Hooks are idiomatic way            |

---

💡 **Bottom line:**

- You **can apply OOP concepts** in React, especially with class components.
- Modern React favors **composition and hooks**, which provide the same benefits **without heavy inheritance hierarchies**.
- Encapsulation, abstraction, and polymorphism are still very much achievable, just in **functional and composable ways**.

---
