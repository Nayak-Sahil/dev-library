# React Strict Mode

A short note about React's Strict Mode and why it's useful in development.

## Index
- What Strict Mode does
- Example

---

**Strict Mode** in React is a tool for highlighting potential problems in your application. It helps with **identifying unsafe lifecycles**, **legacy API usage**, and **unexpected side effects**.

```ts
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

In strict mode, React may intentionally double-invoke certain functions like `useEffect` and `componentDidMount` in development to help surface bugs caused by side effects.
