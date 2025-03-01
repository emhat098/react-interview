# React interview

#### Can you explain the difference between React and Next.js? When would you choose one over the other?

React is a JavaScript library for building UI components, primarily focused on client-side rendering (CSR). It provides a component-based structure but does not include built-in routing, server-side rendering, or API handling.

On the other hand, Next.js is a full-stack React framework that offers features like server-side rendering (SSR), static site generation (SSG), API routes, and built-in performance optimizations.

When to choose React.js:

- If you’re building a purely client-side application that doesn’t require server-side rendering or SEO optimizations, such as SPAs (Single Page Applications) or progressive web apps (PWAs).
- If you prefer full control over your app’s routing, data fetching, and server configuration.

When to choose Next.js:

- If your application requires SEO optimization, faster load times with SSG/ISR, and pre-rendered pages.
- If your app involves server-side operations, like handling cookies, authentication (session-based or JWT), and API integration.
- If you want better performance and automatic code-splitting without manual configuration.

For example, in an e-commerce website where SEO is critical, I would choose Next.js to ensure product pages are server-rendered for better search engine visibility. But for a dashboard application that does not rely on SEO, I would use React.js with client-side rendering.

---

#### What are React Hooks? Can you explain useEffect and how it differs from componentDidMount and componentDidUpdate?

React Hooks are special functions that allow functional components to manage state and lifecycle features without using class components. Hooks always start with use (e.g., useState, useEffect, useContext). Introduced in React 16.8, they replaced lifecycle methods like componentDidMount, componentDidUpdate, and componentWillUnmount.

**useEffect Hook**

The `useEffect` Hook is used to perform side effects in functional components, such as fetching data, subscribing to events, or manually updating the DOM. It runs after the component renders and depends on the dependency array to determine when it should execute.

How `useEffect` Replaces Class Lifecycle Methods?

| Class Lifecycle Method |  Equivalent useEffect Implementation | Description |
| --- | --- | --- |
| componentDidMount | `useEffect(() => { /* Effect code */ }, [])` | Runs once after the first render |
| componentDidUpdate | `useEffect(() => { /* Effect code */ }, [])`  | Runs when dependencies change |
| componentWillUnMount | `useEffect(() => { return () => { /* Cleanup code */ } }, [])` | Runs cleanup when component unmounts |

---

#### What is React Context API? How does it compare to Redux, and when would you use one over the other?

React Context API is a built-in feature in React that allows us to manage and share state globally without needing to pass props manually through multiple levels of components (also known as “prop drilling”). We can access the context value using the useContext hook. Context is useful for managing theme, authentication, and user preferences in an application.

```jsx
const ThemeContext = React.createContext("light"); // Creating Context

const App = () => {
  return (
    <ThemeContext.Provider value="dark">
      <ChildComponent />
    </ThemeContext.Provider>
  );
};

const ChildComponent = () => {
  const theme = useContext(ThemeContext); // Access context
  return <div>Current Theme: {theme}</div>;
};
```

React Context vs. Redux

| Feature |  React Context API | Redux |
| --- | --- | --- |
| Purpose | Passing data without prop drilling | Global state management with predictable updates |
| Best for | Light state sharing (theme, auth, user preferences) | Complex state (caching, undo/redo, middlewares) |
| Performance | Can cause unnecessary re-renders | Optimized with reducers and selectors |
| Tools | useContext, useReducer | Redux DevTools, middleware (Redux Thunk, Redux Saga) |

When to Choose Context vs. Redux:

Use React Context if:

- You only need to share simple state like authentication, theme, or user preferences.
- You don’t need advanced debugging tools or performance optimizations.
- Your state is not frequently updated.

Use Redux if:

- You have complex state management, like shopping carts, dashboards, or API caching.
- You need centralized, structured state management across multiple components.
- You require time-travel debugging (Redux DevTools) or middleware support.

---

#### What are React Suspense and React Lazy? How do they help in code splitting and performance optimization?

React Suspense is a built-in React component that allows handling asynchronous rendering without blocking the UI. It is mainly used to show a fallback UI (like a loading spinner) while waiting for async operations to complete. Suspense works with React.lazy on the client side and with server components in Next.js for streaming rendering.

```jsx

import React, { lazy, Suspense } from "react";

const HeavyComponent = lazy(() => import("./HeavyComponent"));

const App = () => {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  );
};

```

- React.lazy loads HeavyComponent only when needed (code splitting).
- Next.js streams the final content without blocking the whole page.

React Lazy vs. React Suspense

| Feature | React Lazy | React Suspense |
| --- | --- | --- |
| Purpose | Load components dynamically at runtime | Handle async rendering with fallback UI |
| Code Splitting? | Yes | No (But works with React.lazy for splitting) |
| Use Case | Large components (editor, charts, video players) | Data fetching, Server Components, Lazy Components |

When to Use Each?

- Use React.lazy when you want to split large components into separate files and load them only when needed (good for improving bundle size).
- Use Suspense to gracefully handle loading states without blocking rendering, especially in Next.js for server streaming.

---

#### What are the differences between useMemo and useCallback? When should you use them?

| Feature | useMemo | useCallback |
| --- | --- | --- |
| Purpose | Memoizes the return value of a function to avoid expensive recalculations | Memoizes the function definition to prevent unnecessary re-creations |
| Use case | Used when a function returns a computed value (e.g., filtering a large list, complex calculations) | Used when passing a function as a prop to prevent unnecessary re-renders of child components |
| Return type | Returns a memoized value | Returns a memoized function |

```jsx
import { useMemo } from "react";

const Example = ({ numbers }) => {
  const expensiveCalculation = useMemo(() => {
    console.log("Calculating...");
    return numbers.reduce((acc, num) => acc + num, 0);
  }, [numbers]); // Recomputes only when numbers change

  return <p>Sum: {expensiveCalculation}</p>;
};
```

Why use useMemo?

- If the numbers array is large, recalculating the sum on every render is slow.
- useMemo caches the result and only recalculates when numbers changes.

```jsx
import { useState, useCallback } from "react";

const ChildComponent = ({ handleClick }) => {
  console.log("Child re-rendered!");
  return <button onClick={handleClick}>Click me</button>;
};

const Parent = () => {
  const [count, setCount] = useState(0);

  const memoizedHandleClick = useCallback(() => {
    console.log("Button clicked!");
  }, []); // Function stays the same across renders

  return (
    <div>
      <p>Count: {count}</p>
      <ChildComponent handleClick={memoizedHandleClick} />
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};
```

Why use useCallback?

- Without useCallback, handleClick would be recreated on every render, causing ChildComponent to re-render unnecessarily.
- With useCallback, the function remains the same reference across renders unless dependencies change.

When to Use useMemo vs. useCallback?

Use useMemo when:

- You need to memoize a computed value (like filtering, sorting, calculations).
- You want to optimize performance-heavy computations.

Use useCallback when:

- You need to memoize a function reference (especially when passing functions as props to child components).
- You want to prevent unnecessary re-renders in child components.

---

#### What are the differences between controlled and uncontrolled components in React?

In React, controlled and uncontrolled components refer to how form elements handle their state.

Controlled Components

- The state of the input is managed by React using useState.
- Every time the user types, React updates and controls the state.
- The input value is fully controlled by React, not the DOM.

```jsx
import { useState } from "react";

const ControlledInput = () => {
  const [value, setValue] = useState(""); // React state controls input

  return (
    <input
      type="text"
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
};
```

Why use Controlled Components?

- Useful for form validation, dynamic updates, and real-time feedback.
- Makes state predictable and easy to debug.

Uncontrolled Components

- The input’s value is managed by the DOM itself, not React.
- React does not track state; instead, ref is used to access the value when needed.
- Best for simple form elements where React does not need full control.

```jsx
import { useRef } from "react";

const UncontrolledInput = () => {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    alert(inputRef.current.value); // Access value from DOM
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
};
```

Why use Uncontrolled Components?

- Useful when integrating third-party libraries that manage their own state.
- Good for simple, non-reactive form inputs that don’t need React control.

Controlled vs. Uncontrolled Components Comparison

|  Feature | Controlled Component | Uncontrolled Component |
| --- | --- | --- |
| State Management | Controlled by React (useState) | Managed by the DOM (ref) |
| Real-time Updates | Yes (on every keystroke) | No (value accessed when needed) |
| Best Use Case | Forms with validation, real-time updates | Simple forms, integrating non-React libraries |
| Performance | May cause extra re-renders | Better for performance when React state is not needed |

When to Use Each?

Use Controlled Components if:

- You need form validation, dynamic updates, or instant state changes.
- You want React to fully manage the input value.

Use Uncontrolled Components if:

- You are handling simple form elements where React state is not necessary.
- You are working with third-party form libraries that already manage state.

---

#### What are React Portals, and when should you use them?

React Portals allow rendering a component outside its parent DOM hierarchy while keeping it within React’s virtual DOM.

Normally, when you render a React component, it is placed inside the parent component’s DOM structure. But sometimes, you need to render elements outside of the main React root node—like modals, tooltips, or popups.

React Portals help achieve this by rendering components into a different part of the DOM, while still keeping them within React’s state and event system.

How to Use React Portals

1. Create a target div in your HTML (outside the root)

```html
<!-- Public index.html -->
<body>
  <div id="root"></div>   <!-- Normal React App -->
  <div id="portal-root"></div> <!-- Separate Portal Container -->
</body>
```

2. Use ReactDOM.createPortal to render inside portal-root

```jsx
import React from "react";
import ReactDOM from "react-dom";

const Modal = ({ children }) => {
  return ReactDOM.createPortal(
    <div className="modal">
      {children}
    </div>,
    document.getElementById("portal-root") // Renders outside #root
  );
};

const App = () => {
  return (
    <div>
      <h1>Main App</h1>
      <Modal>
        <h2>This is inside a Portal!</h2>
      </Modal>
    </div>
  );
};

export default App;
```

When Should You Use React Portals?

- Modals, Dialog Boxes, Popups → Prevents issues with z-index and overflow clipping.
- Tooltips → Ensures tooltips are positioned correctly without being affected by parent elements.
- Dropdown Menus → Avoids CSS positioning conflicts when nested deep in components.

Key Benefits of React Portals

- Avoids CSS overflow issues (e.g., modals inside a div with overflow: hidden).
- Keeps event bubbling inside React (even though rendered outside).
- Helps separate UI concerns while keeping the virtual DOM intact.

---

#### What are Higher-Order Components (HOCs) in React? Can you provide an example?

A Higher-Order Component (HOC) is a function that takes a component as an argument and returns a new enhanced component. It is used to reuse logic, modify behavior, or inject additional props into a component.

HOCs follow the pattern:

```jsx
const EnhancedComponent = HOC(WrappedComponent);
```

Why Use HOCs?

- Code reusability: Share logic between multiple components without duplicating code.
- Separation of concerns: Keep UI and logic separate.
- Enhancing components: Modify component behavior without changing its original structure.

HOCs vs. React Hooks

- Before Hooks (React <16.8), HOCs were the main way to share logic.
- Now, Hooks (useContext, useEffect) often replace HOCs for reusability.
- HOCs are still useful for component wrapping, logic separation, and injecting props dynamically.

---

#### What are Render Props in React, and how are they different from HOCs?

Render Props is a pattern where a component receives a function as a prop and calls it to determine what to render. It allows components to share logic without changing their structure.

Instead of wrapping a component (like HOCs do), Render Props use a function to control rendering.

```jsx
import React, { useState } from "react";

const MouseTracker = ({ render }) => {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  const handleMouseMove = (e) => {
    setPosition({ x: e.clientX, y: e.clientY });
  };

  return <div onMouseMove={handleMouseMove}>{render(position)}</div>;
};

const App = () => {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <h1>Mouse position: {x}, {y}</h1>
      )}
    />
  );
};

export default App;
```

How This Works?

1. MouseTracker manages the logic (tracking the mouse position).
2️. Instead of rendering UI itself, it calls the render function prop to pass x, y coordinates.
3️. The parent component (App) controls what UI is displayed.

Render Props vs. Higher-Order Components (HOCs)

| Feature | Render Props | Higher-Order Components (HOCs) |
| --- | --- | --- |
| How it works | Passes a function as a prop to determine rendering | Wraps a component to modify its behavior |
| Code Structure | More flexible (UI is controlled by parent component) | More abstract (logic is hidden inside HOC)|
| Use Case | Sharing logic where UI needs customization | Reusing logic where UI structure remains the same |
| Example Use Case | Mouse tracking, data fetching, animations | Authentication, theming, state management |

When to Use Render Props?

- When multiple components need the same logic but different UI.
- When you need more flexibility in rendering compared to HOCs.
- When you want to avoid deep component wrapping (common issue in HOCs).

HOCs vs. Render Props: Which One to Choose?

- Use HOCs when logic doesn’t affect the UI structure (e.g., authentication, permissions).
- Use Render Props when UI customization is needed while keeping the logic in a shared component.

---

#### What are React Refs? How do they differ from state, and when would you use them?

Refs in React are a way to access DOM elements directly or store mutable values without triggering re-renders. They are created using useRef and can be assigned to elements or hold values across renders.

How to Use React Refs?

1. Accessing DOM Elements

Refs are commonly used for direct DOM manipulation, like focusing an input.

```jsx
import { useRef } from "react";

const InputFocus = () => {
  const inputRef = useRef(null); // Creating a ref

  const handleFocus = () => {
    inputRef.current.focus(); // Directly access DOM element
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleFocus}>Focus Input</button>
    </div>
  );
};
```

Why use ref?

- Without refs, we’d have to rely on React state to track focus, which isn’t ideal for direct manipulation.
- Refs do not cause re-renders when their value changes.

2. Storing Mutable Values Without Re-Renders

Refs can store values that persist across renders without causing re-renders.

```jsx
import { useRef, useState } from "react";

const Counter = () => {
  const countRef = useRef(0); // Mutable ref value
  const [count, setCount] = useState(0); // React state

  const increment = () => {
    countRef.current += 1; // Ref value updates but does NOT trigger re-render
    setCount(count + 1); // State update triggers re-render
  };

  return (
    <div>
      <p>State Count: {count}</p>
      <p>Ref Count: {countRef.current}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
};
```

Difference between useRef and useState

| Feature | useRef | useState |
| --- | --- | --- |
| Triggers re-render? | No | Yes |
| Use Case | Storing values without causing re-renders | Updating state that should trigger re-renders |
| Common Usage | DOM access, timers, persisting values across renders | UI updates, dynamic state changes |

When Should You Use useRef Instead of useState?

- Accessing and modifying DOM elements directly (e.g., focus, scroll, play video).
- Storing previous values or mutable variables without re-renders.
- Managing timers, intervals, or event listeners that shouldn’t trigger re-renders.

Key Takeaways

- Refs are not two-way binding (Unlike Vue.js, React does not have native two-way binding).
- Use useRef when you don’t want updates to cause re-renders.
- Use useState for UI updates that should trigger re-renders.

---

#### What are keys in React, and why are they important in lists?

Keys in React are unique identifiers used to help React efficiently update and render lists. They allow React to track which items have changed, been added, or removed.

Why Are Keys Important?

- React uses keys to identify elements in a list and decide whether to re-render them.
- Without keys, React re-renders everything, making updates inefficient.
- With keys, React can optimize rendering by updating only the changed elements.

Example: Rendering a List with Keys

```jsx
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" },
];

const UserList = () => {
  return (
    <ul>
      {users.map((user) => (
        <li key={user.id}>{user.name}</li> // Best practice: Use a unique ID as the key
      ))}
    </ul>
  );
};

```

React uses user.id as the key to efficiently update only changed elements.

Why Shouldn’t You Use Index as Key?

If you use the array index `(map((item, index) => <li key={index}>)`, React may update the wrong item, causing unexpected behavior.

Example: The Problem with Using Index as Key

```jsx
const users = ["Alice", "Bob", "Charlie"];

const UserList = () => {
  return (
    <ul>
      {users.map((user, index) => (
        <li key={index}>{user}</li> // ⚠️ Bad Practice
      ))}
    </ul>
  );
};
```

- If the list changes (e.g., an item is added or removed), React might assign the wrong key, causing:
- Incorrect UI updates (wrong item updates instead of reordering properly).
- Performance issues (unnecessary re-renders).

When Is It Safe to Use Index as Key?

- If the list is static (does not change, add, or remove items).
- If the list is small, and performance impact is negligible.

Best Practices for Using Keys in React

- Always use a unique and stable identifier (like a database id).
- Avoid using index as key unless the list is static.
- Ensure keys do not change between renders (changing keys forces re-mounting).

Key Takeaways

- Keys help React efficiently track and update list elements.
- Unique keys prevent unnecessary re-renders and incorrect updates.
- Avoid using index as a key unless the list is static.

---

#### What is Prop Drilling in React, and how do you solve it?

Prop Drilling happens when data needs to be passed down through multiple nested components via props, even when some intermediate components don’t need it.

- This makes the code hard to maintain and less scalable as the application grows.
- Prop drilling occurs before React Context and Redux were commonly used for state management.


Example of Prop Drilling Problem

```jsx
const App = () => {
  const user = "Alice"; // Data from top-level component

  return <Parent user={user} />; // Passing user prop down
};

const Parent = ({ user }) => {
  return <Child user={user} />; // Passing again
};

const Child = ({ user }) => {
  return <p>Hello, {user}!</p>; // Finally used here
};
```

- Problem: Even though Parent does not use user, it still has to pass it to Child.
- As the app scales, this “props drilling” becomes harder to manage.

How to Solve Prop Drilling?

Solution 1: Use React Context API (Best for Simple State Sharing)

Instead of passing props through every component, Context API allows any component to directly access data.

```jsx
import React, { createContext, useContext } from "react";

const UserContext = createContext(); // Creating Context

const App = () => {
  return (
    <UserContext.Provider value="Alice"> 
      <Child /> {/* No need to pass props manually */}
    </UserContext.Provider>
  );
};

const Child = () => {
  const user = useContext(UserContext); // Directly access user from Context
  return <p>Hello, {user}!</p>;
};
```

Why use Context?

- Avoids unnecessary prop passing.
- Keeps the code clean and maintainable.
- Best for global app settings (e.g., theme, auth, language).

Solution 2: Use Redux (or Zustand, Recoil) for Global State (Best for Complex State)

For large-scale apps, Redux centralizes state management so any component can access data without prop drilling.

```jsx
import { Provider, useSelector } from "react-redux";
import { createStore } from "redux";

const reducer = (state = { user: "Alice" }) => state;
const store = createStore(reducer);

const App = () => (
  <Provider store={store}>
    <Child />
  </Provider>
);

const Child = () => {
  const user = useSelector((state) => state.user); // Get user from Redux
  return <p>Hello, {user}!</p>;
};
```

Why use Redux?

- Best for large applications with complex state.
- Provides centralized state management.
- Includes Redux DevTools for debugging.

Prop Drilling vs. Alternative Solutions

| Approach | Best For | How It Works |
| --- | --- | --- |
| Prop Drilling | Small apps with few components | Pass data manually via props |
| React Context | Medium apps, global state (theme, auth) | Provides a shared state that can be accessed anywhere |
| Redux / Zustand | Large apps with complex state | Stores and manages state globally, avoids prop drilling |

Key Takeaways

- Prop Drilling happens when props are passed too deeply into nested components.
- React Context solves this issue for lightweight state sharing.
- Redux (or Zustand) is best for complex, large-scale state management.

#### What is React Fiber, and how does it improve React’s performance?

React Fiber is the new core reconciliation engine in React (introduced in React 16) that improves rendering performance by making it asynchronous and prioritizing UI updates.

Before Fiber (React 15 and earlier):
- React used a synchronous rendering model, which meant large component updates could block the main thread, causing UI lag.

With Fiber (React 16+):
- React breaks the UI rendering into small units of work (fiber nodes).
- Allows interruptible rendering, meaning React can pause low-priority updates and focus on urgent ones (e.g., animations, user input).
- Enables Concurrent Mode, improving responsiveness in large applications.

How React Fiber Improves Performance

- Asynchronous Rendering: React can pause, resume, or discard work if needed.
- Prioritization of Updates: Urgent UI updates (like typing) happen before background updates (like data fetching).
- Concurrent Mode: Improves responsiveness by rendering components in the background without blocking the UI.

Example: Without Fiber (Before React 16)

```jsx
const BlockUI = () => {
  let start = Date.now();
  while (Date.now() - start < 3000) {} // Blocking operation
  return <p>UI freezes for 3 seconds</p>;
};
```

Issue: The browser completely freezes while this component renders.

Example: With React Fiber (React 16+)

```jsx
const BlockUI = () => {
  const [loading, setLoading] = React.useState(true);

  React.useEffect(() => {
    setTimeout(() => setLoading(false), 3000); // Non-blocking async update
  }, []);

  return <p>{loading ? "Loading..." : "Finished!"}</p>;
};
```

With Fiber, React can pause this update and keep the UI responsive.

Key Features of React Fiber

| Feature | Before Fiber (React 15) | After Fiber (React 16+) |
| --- | --- | --- |
| Rendering Type | Synchronous (Blocking) | Asynchronous (Non-blocking) |
| Performance | Slow for large UIs | Faster with rendering prioritization |
| Concurrent Mode | Not supported | Supported (background rendering) |
| Animations & Transitions | Can lag | Smooth & responsive |

Real-World Benefits of Fiber

- Prevents UI from freezing during heavy rendering tasks.
- Smooth animations and transitions without frame drops.
- Better user experience for large-scale React applications.

Key Takeaways

- React Fiber is not about reducing bundle size—it’s a new rendering engine for better performance.
- It enables non-blocking, asynchronous rendering for a smoother UI.
- React Fiber makes features like Concurrent Mode and Suspense possible.

---

#### What is Concurrent Mode in React, and how does it improve user experience?

What is Concurrent Mode in React, and how does it improve user experience?

Concurrent Mode is an optional React feature that allows rendering to be non-blocking and interruptible, improving performance and user experience by prioritizing important updates.

- Normally, React renders synchronously—once a render starts, it must finish before the browser can handle user interactions.
- With Concurrent Mode, React can pause rendering, prioritize urgent updates, and resume when needed (e.g., animations stay smooth even if data is loading).

How Concurrent Mode Improves UI Performance

| Feature | Before Concurrent Mode | With Concurrent Mode |
| --- | --- | --- |
| Rendering Type | Blocking (once started, must finish) | Interruptible (pauses & resumes when needed) |
| UI Freezing? | Yes (Large renders can freeze UI) | No (UI stays responsive) |
| Prioritization | No priority control | High-priority updates (like typing) happen first |
| Smooth Animations? | Can lag when re-rendering | Animations run smoothly |

Example: Without Concurrent Mode (UI Freezes During Rendering)

```jsx
const SlowComponent = () => {
  let start = Date.now();
  while (Date.now() - start < 3000) {} // Blocks UI for 3 seconds
  return <p>Loaded!</p>;
};

const App = () => {
  const [show, setShow] = React.useState(false);
  return (
    <div>
      <button onClick={() => setShow(true)}>Load</button>
      {show && <SlowComponent />}
    </div>
  );
};
```

Issue: Clicking the button freezes the UI for 3 seconds before rendering the component.

Example: With Concurrent Mode (UI Stays Responsive)

Using React Suspense and Concurrent Mode, we can prevent UI freezing.

```jsx
import React, { Suspense, lazy } from "react";

const SlowComponent = lazy(() => {
  return new Promise((resolve) =>
    setTimeout(() => resolve({ default: () => <p>Loaded!</p> }), 3000)
  );
});

const App = () => {
  const [show, setShow] = React.useState(false);

  return (
    <div>
      <button onClick={() => setShow(true)}>Load</button>
      <Suspense fallback={<p>Loading...</p>}>
        {show && <SlowComponent />}
      </Suspense>
    </div>
  );
};
```

With Concurrent Mode:

- The button click is not delayed (UI remains interactive).
- React renders the “Loading…” message while fetching the component.
- React does not block the entire app while rendering.

When Should You Use Concurrent Mode?

- For better user experience → Prevents UI freezing in large applications.
- For animations & transitions → Keeps animations smooth even during data loading.
- For prioritizing updates → Ensures that urgent updates (like user input) are processed first.

Key Takeaways

- Concurrent Mode allows React to pause rendering and keep the UI responsive.
- It helps prioritize important updates like user interactions over background tasks.
- Works well with features like Suspense and React Fiber to improve performance.

#### What is React Server Components (RSC), and how do they differ from Client Components?

React Server Components (RSC) are components that execute entirely on the server, fetch data, and return serialized UI elements to the client. They improve performance by reducing JavaScript sent to the browser.

- Unlike Client Components, which run in the browser, Server Components execute on the server and send only the rendered HTML/JSON to the client.
- They do not include client-side interactivity (e.g., useState, useEffect).
- They are marked with "use server" at the top of the file.

How React Server Components Work

Server Component Example (.tsx file)

```jsx
"use server";

import db from "@/lib/db"; // Fetching from DB directly on the server

export default async function ServerComponent() {
  const data = await db.getUsers(); // No need for useEffect!
  return (
    <div>
      <h1>Users</h1>
      <ul>
        {data.map((user) => (
          <li key={user.id}>{user.name}</li>
        ))}
      </ul>
    </div>
  );
}
```

Why is this more efficient?

- No need to fetch data on the client (faster initial load).
- No client-side JavaScript needed—only HTML is sent.

How to Use React Server Components Inside Client Components

Since Server Components cannot handle interactivity, they are usually wrapped inside Client Components using React Suspense.

Client Component Example

```jsx
"use client"; // Runs on browser

import { Suspense } from "react";
import ServerComponent from "./ServerComponent";

export default function ClientComponent() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Loading...</p>}>
        <ServerComponent /> {/* Server Component inside Client Component */}
      </Suspense>
    </div>
  );
}
```

How this works:

- ServerComponent renders data on the server, and the ClientComponent handles interactivity.
- Suspense prevents UI blocking while the server component loads.

React Server Components vs. Client Components

| Feature | Server Components ("use server") | Client Components ("use client") |
| --- | --- | --- |
| Where It Runs | Server | Browser |
| Can Use useState & useEffect? | No | Yes |
| Handles Data Fetching? | Yes (fetches on server) | No (needs API calls) |
| JavaScript Sent to Client | Minimal (only HTML) | ⚠️ Full Component + JS |
| Performance | Fast (Less JS, better SEO) | Slower for large apps |

---

When to Use Server Components?

- For static or dynamic content that does not require interactivity (e.g., product listings, blog posts).
- To reduce JavaScript bundle size (RSC sends only HTML, no client JS).
- For SEO optimization (Server-rendered pages improve indexing).

Key Takeaways

- React Server Components execute on the server and return rendered UI without JavaScript.
- They do not support client-side hooks (useState, useEffect).
- They reduce JavaScript bundle size, improving app performance.
- Client Components handle interactivity, while Server Components handle rendering and data fetching.

---

#### What are React Hydration and Partial Hydration? How do they improve performance?

React Hydration is the process of attaching event listeners and making a server-rendered React app interactive on the client side.

- When using Server-Side Rendering (SSR), React renders the initial HTML on the server and sends it to the browser.
- The browser then runs React on top of this static HTML, “hydrating” it by attaching event handlers and making it fully interactive.

Example: Hydration in SSR (Next.js)

Server-Rendered HTML (Before Hydration)

```jsx
<div id="app">
  <button>Click me</button>
</div>
<script src="react.bundle.js"></script>
```

- At this point, the button is visible but NOT interactive.
- Hydration attaches event listeners, making it interactive.

React Hydrates the UI

```jsx
import { useState } from "react";

export default function Button() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Clicked {count} times</button>;
}
```

- React “hydrates” the existing HTML by attaching event listeners, allowing useState to update the button dynamically.

What Is Partial Hydration?

Partial Hydration allows React to hydrate only specific parts of a page instead of the entire app, improving performance.

- Traditional hydration loads and hydrates the entire app at once, which can cause slow performance for large applications.
- Partial Hydration only hydrates interactive components when needed, making the page load faster and feel more responsive.

Example: Partial Hydration in Next.js

Using dynamic() to Lazy Load Components

```jsx
"use client";

import dynamic from "next/dynamic";

const HeavyComponent = dynamic(() => import("./HeavyComponent"), { ssr: false });

export default function Page() {
  return (
    <div>
      <h1>Welcome</h1>
      <HeavyComponent /> {/* Hydrates only when needed */}
    </div>
  );
}
```

Instead of hydrating the entire page at once, React waits to hydrate HeavyComponent when it’s needed.

React Hydration vs. Partial Hydration

| Feature | React Hydration | Partial Hydration |
| How It Works | Hydrates the entire server-rendered page | Hydrates only interactive components |
| Performance | Can be slow for large apps | Faster because unnecessary hydration is avoided |
| Best Use Case | Full-page SSR apps | Large apps where only some parts need interactivity |
| Example | Next.js Server-Side Rendering (SSR) | Next.js dynamic() to defer hydration |

When to Use Partial Hydration?

- For large-scale applications where only some sections need interactivity (e.g., product pages with static descriptions but interactive reviews).
- To improve performance by avoiding unnecessary hydration.
- When optimizing SSR applications to load faster on low-end devices.

Key Takeaways

- React Hydration makes a server-rendered page interactive by attaching event listeners.
- Partial Hydration improves performance by only hydrating necessary components.
- Using Next.js dynamic() or React Suspense helps achieve Partial Hydration.

---

#### What is React Streaming?

React Streaming is a technique that allows server-rendered components to be sent to the browser in chunks as they are ready, rather than waiting for the entire page to render before displaying anything.

- Traditional server-side rendering (SSR) requires the server to fully generate the HTML before sending it to the client, causing potential delays.
- Streaming rendering allows the server to send parts of the page immediately, improving the perceived load time for users.

How Does React Streaming Work?

1. The server renders components asynchronously, sending HTML chunks as soon as they are available.
2. The browser starts displaying these chunks immediately, instead of waiting for the entire page.
3. React completes the page rendering progressively, enhancing the user experience.

Example: Streaming with Next.js 13

```jsx
// app/page.tsx (Server Component)
import { Suspense } from "react";
import HeavyComponent from "./HeavyComponent";

export default async function Page() {
  return (
    <div>
      <h1>Welcome to the Page</h1>
      <Suspense fallback={<p>Loading content...</p>}>
        <HeavyComponent />
      </Suspense>
    </div>
  );
}
```

- Suspense allows streaming: The header (<h1>Welcome...) is displayed immediately while the HeavyComponent loads asynchronously.
- The server streams HTML chunks to the browser, providing a faster first paint.

How React Streaming Improves Performance

| Feature | Traditional SSR | React Streaming |
| Rendering Process | Waits for all components to load | Sends HTML in chunks as ready |
| Initial Page Load | Can be delayed by slow components | Fast first paint with async components |
| User Experience | Blank page until fully loaded | Content loads progressively |
| Best Use Case | Static pages, small apps | Large apps, dynamic content, heavy data fetching |

When Should You Use React Streaming?

- For pages with mixed static and dynamic content.
- When data fetching might delay page load (e.g., dashboards, e-commerce sites).
- When optimizing for performance and perceived load times.

Key Takeaways

- React Streaming sends server-rendered HTML to the browser in chunks.
- Improves perceived performance by rendering visible parts of the UI immediately.
- Works well with Suspense and Server Components in Next.js 13+.

--- 

#### What are React Fragments, and why would you use them?

What Are React Fragments?

React Fragments let you group a list of children without adding extra nodes to the DOM.

- Normally, React requires components to return a single parent element.
- Fragments solve this by allowing multiple elements to be grouped without an unnecessary wrapper (e.g., <div> or <span>).

Why Use React Fragments?

1. Avoid Unnecessary DOM Elements: Prevents bloating the DOM with extra elements.
2. Better Performance: Reduces the number of DOM nodes, which is especially useful in large lists or complex layouts.
3. CSS Styling: Avoids potential styling issues that arise from unwanted parent elements.

Examples of Using React Fragments

Basic Syntax:

```jsx
const Component = () => {
  return (
    <>
      <h1>Title</h1>
      <p>Description</p>
    </>
  );
};
```

With React.Fragment (Full Syntax):

```jsx
const Component = () => {
  return (
    <React.Fragment>
      <h1>Title</h1>
      <p>Description</p>
    </React.Fragment>
  );
};
```

- Useful when adding key attributes, for example in mapping arrays.

When Should You Use React Fragments?

When rendering lists:

```jsx
const List = ({ items }) => (
  <>
    {items.map((item) => (
      <React.Fragment key={item.id}>
        <h2>{item.name}</h2>
        <p>{item.description}</p>
      </React.Fragment>
    ))}
  </>
);
```

When returning adjacent elements:

```jsx
const Header = () => (
  <>
    <h1>Welcome</h1>
    <p>This is the header section.</p>
  </>
);
```

React Fragments vs. Wrapper Elements

| Feature | React Fragments (<>...</>) | Wrapper Elements (<div>...</div>) |
| --- | --- | --- |
| Extra DOM Nodes? | No | Yes |
| Styling Impact? | None (no extra styling issues) | Can affect CSS and layout |
| Best Use Case | Grouping elements without rendering | When you need specific styling or layout |
| Performance | Better (fewer elements) | Can be less efficient in large lists |


Common Mistakes When Using Fragments

Avoid using Fragments where a semantic element is needed:

```jsx
// Bad: Using Fragment where <ul> is expected
const List = () => (
  <>
    <li>Item 1</li>
    <li>Item 2</li>
  </>
);
```

Use appropriate HTML elements instead:

```jsx
// Good: Use <ul> for a list
const List = () => (
  <ul>
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
);
```

Key Takeaways

- React Fragments allow grouping of elements without adding extra DOM nodes.
- They are useful for rendering adjacent components or lists without a wrapper element.
- Improves performance by avoiding unnecessary elements and keeping the DOM clean.

---

#### What is the difference between useLayoutEffect and useEffect in React? When should you use each?

👉 “Both useLayoutEffect and useEffect are React hooks used to run side effects in functional components, but they differ in when they are executed during the rendering lifecycle.”

1. useEffect: Non-blocking, Asynchronous Effect

```jsx
useEffect(() => {
  // Runs after the render is committed to the screen
  console.log("useEffect");
}, []);
```

When useEffect Runs:

- After the component is rendered and painted on the screen.
- Non-blocking: Does not block the browser paint.
- Suitable for async operations, like fetching data, setting timers, or subscribing to events.

Common Use Cases:

- Data fetching (fetch, axios).
- Subscriptions (WebSocket, EventListener).
- Timers and intervals (setTimeout, setInterval).

2. useLayoutEffect: Blocking, Synchronous Effect

useLayoutEffect(() => {
  // Runs before the browser paints the screen
  console.log("useLayoutEffect");
}, []);

When useLayoutEffect Runs:

- Executes synchronously after all DOM mutations but before the browser repaints the screen.
- Blocks rendering until the effect completes.
- Suitable for measuring DOM elements, synchronous layout adjustments, or animations.

Common Use Cases:

- Measuring elements (getBoundingClientRect).
- Updating layout before paint (e.g., synchronizing scroll positions).
- Third-party library integration that requires DOM manipulation.

Key Differences Between useEffect and useLayoutEffect

| Feature | useEffect | useLayoutEffect |
| --- | --- | --- |
| Execution Timing | After paint (asynchronous) | Before paint (synchronous) |
| Browser Paint | Non-blocking | Blocking |
| Use Case | Fetching data, timers | DOM measurement, layout changes |
| Performance Impact | Better for non-UI operations | Can block rendering, use cautiously |

Example: When to Use Each Hook

1. useEffect for Fetching Data

```jsx
import { useEffect, useState } from "react";

const FetchData = () => {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch("https://api.example.com/data")
      .then((res) => res.json())
      .then((data) => setData(data));
  }, []);

  return <div>Data: {data}</div>;
};
```

Why useEffect?

- Non-blocking, so the UI renders immediately.
- Suitable for async operations that don’t need to block rendering.

2. useLayoutEffect for Measuring Layout

```jsx
import { useLayoutEffect, useRef, useState } from "react";

const MeasureLayout = () => {
  const divRef = useRef(null);
  const [width, setWidth] = useState(0);

  useLayoutEffect(() => {
    setWidth(divRef.current.getBoundingClientRect().width);
  }, []);

  return (
    <div>
      <div ref={divRef} style={{ width: "50%" }}>Measure me</div>
      <p>Width: {width}px</p>
    </div>
  );
};
```

Why useLayoutEffect?

- Synchronous execution ensures the width is measured before the browser paints.
- Ideal for synchronous DOM reads and writes.

When to Use Which Hook?

| Situation | Recommended Hook |
| Fetching data from an API | useEffect |
| Adding event listeners to the window | useEffect |
| Measuring the DOM before render | useLayoutEffect |
| Integrating a third-party UI library | useLayoutEffect |
| Synchronizing animation frame updates | useLayoutEffect |

Key Takeaways

- Use useEffect for non-blocking operations like data fetching and timers.
- Use useLayoutEffect for layout-related tasks like measuring DOM elements or synchronizing layouts.
- Avoid using useLayoutEffect for async operations, as it blocks rendering and can affect performance.

---

#### What Are React.memo and useMemo in React?

Both React.memo and useMemo are used to optimize performance in React applications by avoiding unnecessary re-renders and recalculations, but they work differently and serve different purposes.

1. React.memo: Prevents Unnecessary Re-renders of Components

How It Works:

- React.memo is a higher-order component (HOC) that wraps a functional component.
- It compares the props of the component before re-rendering.
- If the props haven’t changed, it skips the render, improving performance.

When to Use:

- When a component always renders the same output for the same props.
- When passing props to deeply nested components that rarely change.

Example of React.memo:

```jsx
import React, { useState } from "react";

const ChildComponent = React.memo(({ value }) => {
  console.log("ChildComponent rendered");
  return <p>Value: {value}</p>;
});

const ParentComponent = () => {
  const [count, setCount] = useState(0);
  const [value, setValue] = useState("Hello");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <ChildComponent value={value} />
    </div>
  );
};
```

- Without React.memo, ChildComponent would re-render every time ParentComponent re-renders (e.g., when the count changes).
- With React.memo, ChildComponent only re-renders if its value prop changes, avoiding unnecessary renders.

2. useMemo: Memoizes Expensive Calculations

How It Works:

- useMemo is a React hook that memoizes the result of a function.
- It only recomputes the memoized value when its dependencies change.
- Useful for avoiding expensive calculations on every render.

When to Use:

- When you have complex calculations that only need to run when dependencies change.
- When you need to optimize performance for derived data, like filtered lists or computed values.

Example of useMemo:

```jsx
import React, { useMemo, useState } from "react";

const ExpensiveComponent = ({ count }) => {
  const computeExpensiveValue = (num) => {
    console.log("Computing...");
    return num * 2;
  };

  const expensiveValue = useMemo(() => computeExpensiveValue(count), [count]);

  return <p>Expensive Value: {expensiveValue}</p>;
};

const App = () => {
  const [count, setCount] = useState(0);
  const [text, setText] = useState("");

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment Count</button>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <ExpensiveComponent count={count} />
    </div>
  );
};
```

- Without useMemo, the computeExpensiveValue function would run on every render, even if count did not change.
- With useMemo, the calculation only runs when count changes, improving performance.

Key Differences Between React.memo and useMemo

| Feature | React.memo | useMemo |
| --- | --- | --- |
| Type | Higher-order component (HOC) | React Hook |
| Purpose | Prevents re-rendering of components | Memoizes expensive computations |
| Use Case | When component output is static for props | When function output is static for dependencies |
| Performance Gain | Avoids unnecessary re-renders | Avoids unnecessary recalculations |
| Common Usage | For components with static props | For expensive operations, derived data |

When to Use React.memo vs. useMemo?

| Scenario | Recommended Optimization Method |
| --- | --- |
| Avoid re-rendering of child components | React.memo |
| Optimize expensive calculations | useMemo |
| Prevent unnecessary array/object creation | useMemo |
| Maintain performance in list rendering | Both (React.memo with useMemo) |

Real-World Example: Combining React.memo and useMemo

```jsx
const List = React.memo(({ items }) => {
  console.log("List rendered");
  return (
    <ul>
      {items.map((item) => (
        <li key={item}>{item}</li>
      ))}
    </ul>
  );
});

const App = () => {
  const [count, setCount] = useState(0);

  const items = useMemo(() => ["Apple", "Banana", "Cherry"], []);

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <List items={items} />
    </div>
  );
};
```

- useMemo ensures the items array is not recreated on every render.
- React.memo prevents List from re-rendering if items have not changed.

Key Takeaways

- React.memo is ideal for preventing unnecessary re-renders of components.
- useMemo is perfect for memoizing expensive function results to avoid recalculations.
- Combining both can significantly optimize performance in large or complex applications.

---

#### What is Code Splitting in React, and how do you implement it using React.lazy and Suspense?

Code Splitting is a performance optimization technique in React that breaks down large bundles into smaller chunks, loading only the necessary components when needed.

- It helps reduce initial load times by lazy-loading components instead of loading the entire application upfront.
- Commonly used with React.lazy, React Suspense, and dynamic imports in Next.js.

Why Use Code Splitting?

- Faster Initial Load: Only loads critical components first.
- Improved Performance: Avoids loading unnecessary code.
- Better User Experience: Reduces page load times, especially in large applications.

How to Implement Code Splitting in React?

1. Using React.lazy and Suspense

```jsx
import React, { Suspense } from "react";

const LazyComponent = React.lazy(() => import("./HeavyComponent"));

const App = () => {
  return (
    <div>
      <h1>Main App</h1>
      <Suspense fallback={<p>Loading...</p>}>
        <LazyComponent />
      </Suspense>
    </div>
  );
};

export default App;
```

- React.lazy dynamically imports the HeavyComponent only when it is needed.
- Suspense displays a fallback UI (e.g., loading spinner) while the lazy-loaded component loads asynchronously.

2. Code Splitting in Next.js Using dynamic()

```jsx
import dynamic from "next/dynamic";

const LazyComponent = dynamic(() => import("./HeavyComponent"), {
  loading: () => <p>Loading...</p>,
  ssr: false, // Optional: Disable server-side rendering for this component
});

const App = () => (
  <div>
    <h1>Main App</h1>
    <LazyComponent />
  </div>
);

export default App;
```

- dynamic() in Next.js works similarly to React.lazy but with more advanced options.
- It supports server-side rendering (SSR) or client-side only loading.
- Useful for conditionally loading heavy components like charts, editors, or media players.

When Should You Use Code Splitting?

- For Large Components: Like dashboards, rich text editors, or complex forms.
- When Loading Third-Party Libraries: Avoid loading heavy libraries like Moment.js, Lodash, or Chart.js upfront.
- For Routes and Pages: Dynamically load pages in React Router or Next.js to improve navigation speed.

Code Splitting vs. Traditional Import

| Feature | Traditional Import | Code Splitting (React.lazy / dynamic()) |
| --- | --- | --- |
| When Code Loads | During initial bundle load | On demand (when component is needed) |
| Impact on Initial Load | Slower (loads all code) | Faster (loads only critical components) |
| Loading State Handling | Not available | Supports fallback (Suspense, loading) |
| Best Use Case | For small apps or critical code | For heavy components or non-critical paths |

Real-World Example: Code Splitting a Chart Component

```jsx
import dynamic from "next/dynamic";

const Chart = dynamic(() => import("react-chartjs-2"), {
  ssr: false,
  loading: () => <p>Loading chart...</p>,
});

const Dashboard = () => (
  <div>
    <h1>Analytics Dashboard</h1>
    <Chart />
  </div>
);

export default Dashboard;
```

- Only loads the charting library when the dashboard is accessed, improving initial load time.

Best Practices for Code Splitting

- Split by Route: Use lazy loading for page components to speed up navigation.
- Split by Component: Use React.lazy or dynamic() for large individual components.
- Avoid Splitting Small Components: Unnecessary for lightweight components, might increase overhead.
- Use Suspense and fallback UI: Always provide a smooth loading experience to avoid layout shifts.

Key Takeaways

- Code Splitting improves performance by breaking large bundles into smaller chunks.
- Use React.lazy and Suspense in React or dynamic() in Next.js for on-demand loading.
- Best for large components, third-party libraries, and route-based loading.

---

### What is the difference between Client-Side Rendering (CSR), Server-Side Rendering (SSR), and Static Site Generation (SSG) in React?

What Are CSR, SSR, and SSG in React?

Client-Side Rendering (CSR), Server-Side Rendering (SSR), and Static Site Generation (SSG) are rendering strategies in React applications that impact performance, SEO, and user experience.

1. Client-Side Rendering (CSR)

In CSR, the entire rendering process happens in the browser. The server sends a minimal HTML file with a JavaScript bundle, which React uses to generate the UI on the client side.

How It Works:

- The server sends an empty HTML shell with a <div id="root"></div>.
- React hydrates the page by executing JavaScript in the browser.
- Initial load might be slow, but subsequent interactions are fast.

When to Use CSR?

- Web applications where SEO is not important (e.g., dashboards, internal tools).
- When fast navigation and dynamic interactions are needed.

Example of CSR in React:

```jsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
```

2. Server-Side Rendering (SSR)

In SSR, the server renders the initial HTML content and sends it to the browser. The client then hydrates the page to make it interactive.

How It Works:

- The server generates HTML for the requested page.
- The browser receives fully rendered HTML, improving perceived performance.
- React hydrates the page to add interactivity.

When to Use SSR?

- SEO-focused websites where content needs to be indexed by search engines (e.g., blogs, e-commerce sites).
- When the app requires dynamic content that changes frequently.

Example of SSR in Next.js:

```jsx
// pages/index.js
export async function getServerSideProps() {
  const data = await fetch("https://api.example.com/data").then((res) =>
    res.json()
  );
  return { props: { data } };
}

export default function Page({ data }) {
  return <div>{data.message}</div>;
}
```

3. Static Site Generation (SSG)

In SSG, the server pre-renders HTML at build time, delivering static files to the client. These pages load quickly and are ideal for static content.

How It Works:

- HTML pages are generated during the build process, not at runtime.
- The server serves pre-rendered HTML files for each request.
- The site is fast and highly cacheable, but content is static unless rebuilt.

When to Use SSG?

- Content-driven websites where content changes infrequently (e.g., marketing sites, blogs, documentation).
- When the highest performance and SEO are required.

Example of SSG in Next.js:

```jsx
// pages/blog/[id].js
export async function getStaticProps({ params }) {
  const data = await fetch(`https://api.example.com/posts/${params.id}`).then(
    (res) => res.json()
  );
  return { props: { data } };
}

export async function getStaticPaths() {
  return {
    paths: [{ params: { id: "1" } }, { params: { id: "2" } }],
    fallback: false,
  };
}

export default function BlogPost({ data }) {
  return <div>{data.title}</div>;
}
```

Comparison of CSR, SSR, and SSG

| Feature | Client-Side Rendering (CSR) | Server-Side Rendering (SSR) | Static Site Generation (SSG)|
| --- | --- | --- |
| Rendering Location | Browser | Server | Build time (Server)|
| Performance | Slower initial load, faster SPA | Fast initial load, dynamic | Very fast load, static content|
| SEO | Difficult (content loads with JS) | Good (content pre-rendered) | Excellent (content pre-built)|
| Use Case | SPAs, internal tools, dashboards | E-commerce, blogs, dynamic sites | Static sites, marketing pages|
| Data Freshness | Dynamic, real-time | Dynamic, server updates per request | Static unless rebuilt|
| Examples | CRA (create-react-app) | Next.js (getServerSideProps) | Next.js (getStaticProps)|

When to Choose Each Rendering Method?

| Scenario | Recommended Rendering Method |
| --- | --- |
| Fast, dynamic web app | Client-Side Rendering (CSR) |
| SEO and dynamic data | Server-Side Rendering (SSR) |
| Static site with infrequent updates | Static Site Generation (SSG) |
| E-commerce with fast product updates | SSR with revalidation |
| Blog with pre-generated pages | SSG with incremental builds |

Key Takeaways

- CSR is best for apps where SEO is not a priority.
- SSR is ideal for dynamic content and SEO-focused sites.
- SSG provides the best performance and SEO for static content.
- Next.js is a powerful framework that supports all three rendering methods, allowing flexible combinations like SSG + ISR (Incremental Static Regeneration).

---

#### What is Incremental Static Regeneration (ISR) in Next.js, and how does it differ from SSG?
