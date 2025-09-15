# 📘 Redux Guide

## **Redux: Overview**

**Definition:**
Redux is a **state management library** for JavaScript apps (React, Angular, Vue, or vanilla JS). It helps you **manage global state** in a predictable way. It’s especially useful for large apps where passing props becomes cumbersome.

**Core Idea:**
Instead of passing state down the component tree, Redux keeps a **single source of truth** (the store) that any component can access or update via actions. All changes go through **reducers**, which are pure functions, ensuring predictable state updates.

---

## **Core Concepts**

### 1. **Store**

- **Definition:** A JavaScript object that holds the entire app state.
- **Purpose:** Single source of truth for your app’s state.
- **Analogy:** Imagine the store as the **whole tree** that contains all the data of your app.
- **Example Use:** React components read from the store instead of using local state.

```javascript
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";

const store = configureStore({
  reducer: {
    counter: counterReducer, // connects a slice of state
  },
});

export default store;
```

---

### 2. **State**

- **Definition:** The current data snapshot in the store.
- **In Redux:** State is **read-only**; you never modify it directly.
- **Why:** Predictability & easier debugging.

```javascript
const currentState = store.getState(); // read state from store
console.log(currentState.counter.value);
```

---

### 3. **Slices**

- **Definition:** In Redux Toolkit, a _slice_ is a bundle of one feature's state, its reducers, and the auto-generated actions.
- **Analogy:** If the store is the whole tree 🌳, a slice is a **branch** or **piece** of that tree. Each slice focuses on a specific feature (like `user`, `cart`, or `counter`).
- **Signature:** `createSlice({ name, initialState, reducers })` returns an object containing:

  - `reducer`: the slice reducer function to add to the store.
  - `actions`: auto-generated action creators based on the reducers you defined.

- **Example:**

```javascript
import { createSlice } from "@reduxjs/toolkit";

const userSlice = createSlice({
  name: "user",
  initialState: { loggedIn: false },
  reducers: {
    login: (state) => {
      state.loggedIn = true;
    },
    logout: (state) => {
      state.loggedIn = false;
    },
  },
});

export const { login, logout } = userSlice.actions;
export default userSlice.reducer;
```

---

### 4. **Actions**

- **Definition:** Plain JavaScript objects that **describe what happened** in the app.
- **Properties:** Usually have a `type` property (string) and optionally a `payload`.
- **Example:**

```javascript
const incrementAction = { type: "counter/increment" };
const addAmountAction = { type: "counter/addAmount", payload: 5 };
```

- Actions **do not change state themselves**. They only describe an intention.

---

### 5. **Reducers**

- **Definition:** Pure functions that **determine how state changes** in response to actions.
- **Signature:** `(state, action) => newState`

#### With Redux Toolkit (using `createSlice`)

```javascript
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter", // slice name (used in action types: 'counter/increment')
  initialState: { value: 0 }, // slice state
  reducers: {
    // functions to handle state changes
    increment: (state) => {
      state.value += 1; // "mutating" is allowed thanks to Immer
    },
    addAmount: (state, action) => {
      state.value += action.payload; // use payload for dynamic updates
    },
  },
});

// Export the actions (auto-generated)
export const { increment, addAmount } = counterSlice.actions;

// Export the reducer (to connect to store)
export default counterSlice.reducer;
```

---

### 6. **Dispatch**

- **Definition:** A function to **send actions** to the store.
- **Example:**

```javascript
store.dispatch(increment());
store.dispatch(addAmount(10));
```

---

### 7. **Selectors**

- **Definition:** Functions that **extract or compute** state from the store.
- **Why:** Keeps components clean & reusable.

```javascript
const selectCounterValue = (state) => state.counter.value;
```

In React:

```javascript
import { useSelector } from "react-redux";

const CounterComponent = () => {
  const count = useSelector(selectCounterValue);
  return <div>{count}</div>;
};
```

---

### 8. **Provider**

- **Definition:** React component that **makes the store available** to all child components.
- **Usage:**

```javascript
import { Provider } from "react-redux";
import store from "./store";
import App from "./App";

const Root = () => (
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

## **Workflow Diagram (Redux Flow)**

```pseudo
   User Interacts
          |
          v
     React Component
          |
          v
Component Dispatches Action
          |
          v
        Action
          |
          v
       Reducer (state, action) => new state
          |
          v
        Store updates
          |
          v
     Components re-render via useSelector
```

---

## **Key Principles**

1. **Single source of truth:** One store holds all state.
2. **State is read-only:** Only way to change is through actions.
3. **Changes via pure functions:** Reducers are predictable and testable.

---

## **Modern Redux Tips**

- Always use **Redux Toolkit** (`@reduxjs/toolkit`), it removes boilerplate.
- Use **TypeScript** for better type safety with slices & selectors.
- Combine Redux with **React-Redux hooks**: `useSelector` and `useDispatch`.
- For async operations, use **createAsyncThunk**.

---

✅ **Summary:**
Redux helps you manage complex state in a predictable way. Modern Redux (with slices) makes it much easier by bundling **state + actions + reducers** together for each feature. Think of your app state as a tree 🌳: the store is the whole tree, and each slice is a **branch** that manages a specific part of your app.

---

## Further Reading

- in progress...
