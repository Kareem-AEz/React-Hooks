# React Hooks Cheat Sheet

React Hooks are functions that let you use state and other React features without writing a class. They allow you to manage state and side effects in functional components, making your code cleaner and more reusable.

## Table of Contents

1. [useState](#1-usestate)
2. [useEffect](#2-useeffect)
3. [useRef](#3-useref)
4. [useReducer](#4-usereducer)
5. [useContext](#5-usecontext)
6. [useMemo](#6-usememo)
7. [useCallback](#7-usecallback)
8. [memo](#8-memo)

---

## 1. `useState`

Used for managing state in functional components.

```javascript
import { useState } from "react";

function Counter() {
	const [count, setCount] = useState(0);

	const increment = () => setCount((prevCount) => prevCount + 1);

	return <button onClick={increment}>Count: {count}</button>;
}
```

### Best Practices

- **DO:** Use functional updates (`prevState`) when the new state depends on the old state.
- **DO:** Initialize state with a function if the calculation is expensive.
- **DON'T:** Update state directly (e.g., `count++` instead of `setCount(count + 1)`).

### Common Pitfalls

- Forgetting to use the functional form of `setState` when the new state depends on the previous state can lead to bugs.

<div class="page"/>

---

## 2. `useEffect`

Performs side effects in functional components (e.g., fetching data, subscriptions).

```javascript
import { useEffect, useState } from "react";

function UserProfile({ userId }) {
	const [profile, setProfile] = useState(null);

	useEffect(() => {
		let isMounted = true;

		fetch(`/api/user/${userId}`)
			.then((response) => response.json())
			.then((data) => {
				if (isMounted) setProfile(data);
			});

		return () => {
			isMounted = false;
		};
	}, [userId]);

	return <div>{profile ? profile.name : "Loading..."}</div>;
}
```

### Best Practices

- **DO:** Include all dependencies in the dependency array to avoid stale closures.
- **DO:** Clean up effects by returning a cleanup function (e.g., unsubscribing).
- **DON'T:** Omit dependencies or add functions inline without wrapping them in `useCallback`.

<div class="page"/>

---

## 3. `useRef`

Maintains a mutable reference to an element or a value that doesn’t trigger re-renders.

```javascript
import { useRef } from "react";

function TextInput() {
	const inputRef = useRef(null);

	const focusInput = () => inputRef.current.focus();

	return (
		<>
			<input
				ref={inputRef}
				type="text"
			/>
			<button onClick={focusInput}>Focus Input</button>
		</>
	);
}
```

### Best Practices

- **DO:** Use `useRef` for DOM elements or storing mutable values.
- **DON'T:** Use `useRef` to store derived or computed state.

<div class="page"/>

---

## 4. `useReducer`

Manages complex state logic with actions.

```javascript
import { useReducer } from "react";

function counterReducer(state, action) {
	switch (action.type) {
		case "increment":
			return { count: state.count + 1 };
		case "decrement":
			return { count: state.count - 1 };
		default:
			throw new Error(`Unhandled action type: ${action.type}`);
	}
}

function Counter() {
	const [state, dispatch] = useReducer(counterReducer, { count: 0 });

	return (
		<>
			<button onClick={() => dispatch({ type: "decrement" })}>-</button>
			<span>{state.count}</span>
			<button onClick={() => dispatch({ type: "increment" })}>+</button>
		</>
	);
}
```

### Best Practices

- **DO:** Use `useReducer` for state logic that involves multiple sub-values.
- **DO:** Define actions and reducer logic separately for clarity.
- **DON'T:** Use `useReducer` for simple state updates (prefer `useState`).

<div class="page"/>

---

## 5. `useContext`

Shares state globally between components without prop drilling.

### Setting Up Context with Best Practices

```javascript
import { createContext, useContext, useState } from "react";

const MyContext = createContext();

export function MyProvider({ children }) {
	const [value, setValue] = useState("Default Value");

	return (
		<MyContext.Provider value={{ value, setValue }}>
			{children}
		</MyContext.Provider>
	);
}

export function useMyContext() {
	const context = useContext(MyContext);
	if (!context) {
		throw new Error("useMyContext must be used within a MyProvider");
	}
	return context;
}
```

### Using the Custom Hook

```javascript
import { MyProvider, useMyContext } from "./MyContext";

function ChildComponent() {
	const { value, setValue } = useMyContext();
	return (
		<div>
			<p>{value}</p>
			<button onClick={() => setValue("New Value")}>Change Value</button>
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
import { useMemo } from "react";

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

<div class="page"/>

---

## 7. `useCallback`

Memoizes functions to prevent unnecessary re-creation.

```javascript
import { useCallback, useState } from "react";

function ParentComponent() {
	const [count, setCount] = useState(0);

	const increment = useCallback(() => {
		setCount((prevCount) => prevCount + 1);
	}, []);

	return <ChildComponent onClick={increment} />;
}

function ChildComponent({ onClick }) {
	return <button onClick={onClick}>Increment</button>;
}
```

### Best Practices

- **DO:** Use `useCallback` when passing functions to child components.
- **DON'T:** Use it for functions that don’t depend on props or state.

<div class="page"/>

---

## 8. `memo`

Prevents unnecessary re-renders by memoizing components.

```javascript
import React, { memo } from "react";

const ChildComponent = memo(({ value }) => {
	console.log("Rendered");
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

<div>

### Created with ❤️ by [Kareem](https://github.com/Kareem-AEz)

<img src="https://img.shields.io/badge/React-Hooks-blue?style=for-the-badge&logo=react&color=61dafb&labelColor=20232a" alt="React Hooks" />

</div>
