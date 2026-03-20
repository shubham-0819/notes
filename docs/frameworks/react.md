# React

> **TODO:** This document is a stub. Expand with hooks deep-dive, state management, performance patterns, and real-world examples.

## What is React?

React is a JavaScript library for building user interfaces, developed by Meta. It uses a component-based architecture and a virtual DOM for efficient UI updates.

## Core Concepts

### Components

```jsx
// Functional Component
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Usage
<Greeting name="World" />
```

### State with useState

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <button onClick={() => setCount(count + 1)}>
      Count: {count}
    </button>
  );
}
```

### Side Effects with useEffect

```jsx
import { useEffect, useState } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then(res => res.json())
      .then(setData);
  }, []); // empty array = run once on mount

  return <div>{data ? JSON.stringify(data) : 'Loading...'}</div>;
}
```

### Props

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      {children}
    </div>
  );
}
```

## Common Hooks

| Hook | Purpose |
|------|---------|
| `useState` | Local component state |
| `useEffect` | Side effects (fetch, subscriptions) |
| `useContext` | Consume React context |
| `useRef` | Mutable ref / DOM access |
| `useMemo` | Memoize expensive computations |
| `useCallback` | Memoize functions |
| `useReducer` | Complex state logic |

## Key Patterns

- **Lifting State Up** - Move shared state to common ancestor
- **Controlled Components** - Form elements driven by state
- **Custom Hooks** - Extract reusable stateful logic into `use*` functions
- **Context API** - Share global state without prop drilling

## References

- [React Official Docs](https://react.dev/)
- [React GitHub](https://github.com/facebook/react)
