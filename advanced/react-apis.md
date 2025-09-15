# React APIs

## Overview

The “React APIs” category (besides Hooks & Components) are those extra exports in `react` that are used for defining behaviour, rendering optimizations, lazy loading, transitions, contexts, resource reading, test scaffolding, etc. Some are fairly new, some are experimental. Many are related to performance or asynchronous behaviour.

Here are the main items / subtopics, with definitions, when you’d use them, and code examples.

---

## Items & Subtopics

Here are the APIs listed under React’s “APIs” in React 19.1:

| API                 | What it is / Definition                                                                                                                                                                     | When & Why Use It                                                                                                                                                                                                                     | Example                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **createContext**   | Creates a _Context object_ which can hold a value and allow consumers in the component tree to read it without passing props down manually. ([react.dev])                                   | Use when you have data that many components deeply nested need (e.g. theme, locale, auth user), and you want to avoid “prop drilling”.                                                                                                | `js import React, { createContext, useContext } from 'react'; const ThemeContext = createContext('light'); function App() { return <ThemeContext.Provider value="dark"><Toolbar /></ThemeContext.Provider>; } function Toolbar() { return <div><ThemedButton /></div>; } function ThemedButton() { const theme = useContext(ThemeContext); return <button style={{ background: theme === 'dark' ? 'black' : 'white', color: theme === 'dark' ? 'white' : 'black' }}>I am {theme}</button>; }` |
| **lazy**            | Lets you lazily load a React component — code-splitting. The component is loaded when it’s actually rendered (on demand). ([react.dev])                                                     | Use to reduce initial bundle size, or delay loading of heavy UI parts until needed (e.g. admin panel, or modals, etc.).                                                                                                               | `js import React, { Suspense, lazy } from 'react'; const SomeHeavyComponent = lazy(() => import('./HeavyComponent')); function App() { return <div><Suspense fallback={<div>Loading…</div>}><SomeHeavyComponent /></Suspense></div>; }`                                                                                                                                                                                                                                                       |
| **memo**            | Wraps a component so that it only re-renders when its props change (shallow comparison), avoiding unnecessary renders. ([react.dev])                                                        | Use when a component is “pure” (output depends only on props), and re-rendering is expensive. Combined with `useMemo`/`useCallback` to manage props/data.                                                                             | `` js import React, { memo } from 'react'; function MyComponent({ count, onClick }) { console.log('Rendering MyComponent'); return <button onClick={onClick}>Count: {count}</button>; } const Memoized = memo(MyComponent); // will skip render if props `count` and `onClick` are referentially same. ``                                                                                                                                                                                     |
| **startTransition** | Lets you mark state updates as non-urgent / low priority, so React can give priority to urgent updates (e.g. input, clicking) and defer non-urgent updates to avoid janky UI. ([react.dev]) | Use when triggering state changes that are computationally heavy or that you don’t need reflected immediately — e.g. filtering large lists, rendering big trees, etc. Improves responsiveness.                                        | `js import React, { useState, startTransition } from 'react'; function Search() { const [query, setQuery] = useState(''); const [results, setResults] = useState([]); function handleChange(e) { const q = e.target.value; setQuery(q); startTransition(() => { // non-urgent update: fetch / filter results based on q setResults(doHeavySearch(q)); }); } return <><input value={query} onChange={handleChange} /><ResultsList items={results} /></>; }`                                    |
| **act**             | A test utility to wrap rendering and interactions so tests wait for updates to be flushed. Ensures that when you assert results, all state updates / effects have settled. ([react.dev])    | Use in unit/integration tests when you render components and simulate events / state changes, to avoid “warning: updates not wrapped in act” and to make assertions reliable.                                                         | `js import { act, render, screen } from '@testing-library/react'; import MyComponent from './MyComponent'; test('updates on click', () => { render(<MyComponent />); const btn = screen.getByRole('button'); act(() => { btn.click(); }); expect(screen.getByText('Clicked!')).toBeInTheDocument(); });`                                                                                                                                                                                      |
| **use**             | New resource-reading API: lets you “read” from a resource (like a Promise or a context) directly inside a component. The component suspends until the Promise resolves. ([react.dev])       | Use when you want to declaratively fetch or consume asynchronous resources and integrate with Suspense / resource abstractions. It simplifies patterns of data fetching, but is (as of this version) newer and somewhat experimental. | `js function MessageComponent({ messagePromise }) { const message = use(messagePromise); return <div>{message}</div>; } // usage: <Suspense fallback={<div>Loading message...</div>}><MessageComponent messagePromise={fetch('/msg').then(res=>res.text())} /></Suspense>`                                                                                                                                                                                                                    |

---

## Some Notes / Additional APIs

- The “React APIs” page mentions only “modern” ones under current React versions, and some experimental ones. ([react.dev])
- Some APIs like `captureOwnerStack`, `cache`, etc., are more advanced / lower-level or experimental; I omitted them above for clarity, but I can explain those too if you want.
- There are also React-DOM APIs, server APIs, static APIs, etc., but those are in separate sections (for rendering, SSR, etc.). ([react.dev])

---

## Code Example: Combining Several APIs

Here’s a sample component illustrating use of `createContext`, `lazy`, `memo`, `startTransition`, and `use`. This shows how these APIs might work together in a typical React app.

```jsx
import React, {
  createContext,
  useContext,
  useState,
  lazy,
  Suspense,
  memo,
  startTransition,
  use,
} from "react";

// 1. Context for sharing theme
const ThemeContext = createContext("light");

// 2. Lazy load a heavy component
const HeavyComponent = lazy(() => import("./HeavyComponent"));

// 3. Memoized child component
const MemoChild = memo(function MemoChild({ value }) {
  console.log("MemoChild render");
  return <div>Value: {value}</div>;
});

function App() {
  const [theme, setTheme] = useState("light");
  const [showHeavy, setShowHeavy] = useState(false);
  const [dataPromise, setDataPromise] = useState(() =>
    fetch("/api/data").then((r) => r.json())
  );

  function toggleHeavy() {
    // heavy UI; mark as non-urgent so UI stays responsive
    startTransition(() => {
      setShowHeavy((s) => !s);
    });
  }

  function changeTheme() {
    setTheme((t) => (t === "light" ? "dark" : "light"));
  }

  return (
    <ThemeContext.Provider value={theme}>
      <div>
        <button onClick={changeTheme}>Toggle Theme</button>
        <button onClick={toggleHeavy}>Toggle Heavy Component</button>

        <Suspense fallback={<div>Loading…</div>}>
          {showHeavy && <HeavyComponent />}
          <DataDisplay promise={dataPromise} />
        </Suspense>

        <MemoChild value="Some static prop" />
      </div>
    </ThemeContext.Provider>
  );
}

function DataDisplay({ promise }) {
  // new resource API: this will suspend while the promise resolves
  const data = use(promise);
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}

export default App;
```

> In this example:
>
> - We create a `ThemeContext` to share theme data.
> - We lazily load a `HeavyComponent` to reduce initial load time.
> - We memoize `MemoChild` to avoid unnecessary re-renders.
> - We use `startTransition` to mark toggling the heavy component as non-urgent.
> - We use the new `use` API to read from a Promise and suspend rendering until it resolves.
>   This illustrates how these React APIs can be combined to build performant, responsive UIs.

---

## References

- [React.dev: Built-in React APIs](https://react.dev/reference/react/apis)
