# React Hooks Cheat Sheet

React Hooks are functions that let you use state and other React features without writing a class. They allow you to manage state and side effects in functional components, making your code cleaner and more reusable.

## 🚀 Your Ultimate Guide to Mastering React Hooks!

This repository is a comprehensive cheat sheet for React Hooks, designed to help developers understand and use hooks effectively in their projects. Whether you're a beginner or an experienced developer, this guide covers everything you need to know about React's powerful hooks API, including:

- **useState** for state management
- **useEffect** for side effects
- **useRef** for mutable references
- **useReducer** for complex state logic
- **useContext** for global state sharing
- **useMemo** and **useCallback** for performance optimization
- **memo** for preventing unnecessary re-renders

Each hook is explained with clear examples, best practices, and common pitfalls to avoid. Plus, the cheat sheet is structured for easy navigation, making it a handy reference for your day-to-day development.

## Why Use This Cheat Sheet?

✅ **Beginner-Friendly:** Perfect for developers new to React Hooks.  
✅ **Quick Reference:** Easily find what you need with a well-organized table of contents.  
✅ **Best Practices:** Learn how to use hooks effectively and avoid common mistakes.  
✅ **Code Examples:** Practical examples to help you understand each hook in action.  

## Table of Contents
1. [useState](#1-usestate)
2. [useEffect](#2-useeffect)
3. [useRef](#3-useref)
4. [useReducer](#4-usereducer)
5. [useContext](#5-usecontext)
6. [useMemo](#6-usememo)
7. [useCallback](#7-usecallback)
8. [memo](#8-memo)
9. [Common Issues](#9-common-issues)
10. [Custom Hooks](#10-custom-hooks)
11. [Quick Reference Table](#11-quick-reference-table)
12. [Version Compatibility](#12-version-compatibility)
13. [Feedback Mechanism](#13-feedback-mechanism)

---

## 1. `useState`

Used for managing state in functional components.

```javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  const increment = () => setCount(prevCount => prevCount + 1);

  return (
    <button onClick={increment}>Count: {count}</button>
  );
}
```

### Best Practices

- **DO:** Use functional updates (`prevState`) when the new state depends on the old state.
- **DO:** Initialize state with a function if the calculation is expensive.
- **DON'T:** Update state directly (e.g., `count++` instead of `setCount(count + 1)`).

### Common Pitfalls

- Forgetting to use the functional form of `setState` when the new state depends on the previous state can lead to bugs.

---

## 2. `useEffect`

Performs side effects in functional components (e.g., fetching data, subscriptions).

```javascript
import { useEffect, useState } from 'react';

function UserProfile({ userId }) {
  const [profile, setProfile] = useState(null);

  useEffect(() => {
    let isMounted = true;

    fetch(`/api/user/${userId}`)
      .then(response => response.json())
      .then(data => {
        if (isMounted) setProfile(data);
      });

    return () => {
      isMounted = false;
    };
  }, [userId]);

  return <div>{profile ? profile.name : 'Loading...'}</div>;
}
```

### Best Practices

- **DO:** Include all dependencies in the dependency array to avoid stale closures.
- **DO:** Clean up effects by returning a cleanup function (e.g., unsubscribing).
- **DON'T:** Omit dependencies or add functions inline without wrapping them in `useCallback`.

### Dependency Arrays Explained

- An empty array (`[]`) means the effect runs only once after the initial render.
- Specifying dependencies (e.g., `[userId]`) means the effect runs whenever those dependencies change.

---

## 3. `useRef`

Maintains a mutable reference to an element or a value that doesn’t trigger re-renders.

```javascript
import { useRef } from 'react';

function TextInput() {
  const inputRef = useRef(null);

  const focusInput = () => inputRef.current.focus();

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </>
  );
}
```

### Best Practices

- **DO:** Use `useRef` for DOM elements or storing mutable values.
- **DON'T:** Use `useRef` to store derived or computed state.

---

## 4. `useReducer`

Manages complex state logic with actions.

```javascript
import { useReducer } from 'react';

function counterReducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}

function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <span>{state.count}</span>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

### Best Practices

- **DO:** Use `useReducer` for state logic that involves multiple sub-values.
- **DO:** Define actions and reducer logic separately for clarity.
- **DON'T:** Use `useReducer` for simple state updates (prefer `useState`).

### Example: To-Do List

```javascript
function todoReducer(state, action) {
  switch (action.type) {
    case 'add':
      return [...state, action.payload];
    case 'remove':
      return state.filter(todo => todo.id !== action.payload);
    default:
      throw new Error(`Unhandled action type: ${action.type}`);
  }
}
```

---

## 5. `useContext`

Shares state globally between components without prop drilling.

### Setting Up Context with Best Practices

```javascript
import { createContext, useContext, useState } from 'react';

const MyContext = createContext();

export function MyProvider({ children }) {
  const [value, setValue] = useState('Default Value');

  return (
    <MyContext.Provider value={{ value, setValue }}>
      {children}
    </MyContext.Provider>
  );
}

export function useMyContext() {
  const context = useContext(MyContext);
  if (!context) {
    throw new Error('useMyContext must be used within a MyProvider');
  }
  return context;
}
```

### Using the Custom Hook

```javascript
import { MyProvider, useMyContext } from './MyContext';

function ChildComponent() {
  const { value, setValue } = useMyContext();
  return (
    <div>
      <p>{value}</p>
      <button onClick={() => setValue('New Value')}>Change Value</button>
    </div>
  );
}

function App() {
  return (
    <MyProvider>
      <ChildComponent />
    </MyProvider>
  );
}
```

### Best Practices

- **DO:** Wrap consumers with a provider to ensure proper context usage.
- **DO:** Use custom hooks to encapsulate `useContext` for reusability.
- **DON'T:** Use context for state that doesn’t need to be shared globally.

---

## 6. `useMemo`

Memoizes expensive calculations to avoid re-computation.

```javascript
import { useMemo } from 'react';

function ExpensiveComponent({ items }) {
  const total = useMemo(() => {
    return items.reduce((sum, item) => sum + item.value, 0);
  }, [items]);

  return <p>Total: {total}</p>;
}
```

### Best Practices

- **DO:** Use `useMemo` to optimize expensive calculations.
- **DON'T:** Overuse it for trivial computations—it adds unnecessary complexity.

---

## 7. `useCallback`

Memoizes functions to prevent unnecessary re-creation.

```javascript
import { useCallback, useState } from 'react';

function ParentComponent() {
  const [count, setCount] = useState(0);

  const increment = useCallback(() => {
    setCount(prevCount => prevCount + 1);
  }, []);

  return (
    <ChildComponent onClick={increment} />
  );
}

function ChildComponent({ onClick }) {
  return <button onClick={onClick}>Increment</button>;
}
```

### Best Practices

- **DO:** Use `useCallback` when passing functions to child components.
- **DON'T:** Use it for functions that don’t depend on props or state.

---

## 8. `memo`

Prevents unnecessary re-renders by memoizing components.

```javascript
import React, { memo } from 'react';

const ChildComponent = memo(({ value }) => {
  console.log('Rendered');
  return <p>{value}</p>;
});

function ParentComponent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <ChildComponent value={count} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </>
  );
}
```

### Best Practices

- **DO:** Use `memo` to optimize performance for components with stable props.
- **DON'T:** Overuse `memo`—it can make debugging harder.

---

## 9. Common Issues

### Infinite Re-renders in `useEffect`

- Ensure that dependencies are correctly specified to avoid infinite loops.

### Stale State in `useState` or `useReducer`

- Use functional updates to access the latest state.

### Misuse of `useRef` for State Management

- Remember that `useRef` does not trigger re-renders.

---

## 10. Custom Hooks

Custom hooks allow you to extract component logic into reusable functions.

### Example of a Custom Hook

```javascript
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, [url]);

  return { data, loading };
}
```

---


## 12. Quick Reference Table

| Hook        | Purpose                                   | Example Use Case                     |
|-------------|-------------------------------------------|--------------------------------------|
| `useState`  | Manage state in functional components     | Counter, form inputs                 |
| `useEffect` | Perform side effects                      | Fetching data, subscriptions         |
| `useRef`    | Access DOM elements or store mutable values | Focus input, store previous value    |
| `useReducer`| Manage complex state logic                | To-do list, form validation          |
| `useContext`| Share state globally                      | Theme, authentication                 |
| `useMemo`   | Memoize expensive calculations            | Filtering or sorting large lists     |
| `useCallback`| Memoize functions                        | Pass callbacks to child components    |
| `memo`      | Prevent unnecessary re-renders           | Optimize performance of components    |

---

## 13. Version Compatibility

These hooks are available in React 16.8 and later. Ensure you are using a compatible version.

---

## 14. Feedback Mechanism

If you have suggestions or feedback, please open an issue or discussion on [GitHub](https://github.com/Kareem-AEz).

---

## Show Your Support
If you find this cheat sheet helpful, please give it a ⭐️ on GitHub! Your support motivates me to keep creating valuable resources for the developer community.

<div>

### Created with ❤️ by [Kareem](https://github.com/Kareem-AEz)

<img src="https://img.shields.io/badge/React-Hooks-blue?style=for-the-badge&logo=react&color=61dafb&labelColor=20232a" alt="React Hooks" />

</div>
