# React Interview Questions

## 1. How many instances of VDOM always exist in React?

In React, **two instances of the Virtual DOM (VDOM)** are created for each component:
- **Previous VDOM:** This represents the state of the UI before any updates are made.
- **New VDOM (Current):** This represents the UI state after a change occurs, such as a state or prop update.

React uses these two instances during the reconciliation process to perform an efficient diffing operation. It compares the Previous VDOM with the New VDOM to identify changes and then updates the real DOM accordingly, minimizing direct manipulations.

## 2. At what step in the React component lifecycle does a VDOM get created?

The creation of the Virtual DOM (VDOM) occurs during the mounting and updating phases of a React component's lifecycle
1. **Mounting Phase:**
-  During the mounting phase, when a component is rendered for the first time, the render() method is called (for class components) or the function itself is invoked (for functional components).
-  This generates a React element, which is essentially a representation of the Virtual DOM.
2. **Updating Phase**
- When the component's props or state change, React invokes the render() method again for class components or re-invokes the functional component.
- This creates a new Virtual DOM representation.
- React then compares this new VDOM with the previous one using the reconciliation process to update the real DOM efficiently.

Key Points:
- This VDOM is used to determine how the Real DOM should be updated during the **commit phase**.
- The VDOM is indeed created during the render phase of the lifecycle.
- Both for class and functional components, the creation of the VDOM happens whenever the component is rendered, whether for the first time (mounting) or due to updates (state or prop changes).

## 3. How to pass state between two components, not related to each other, without using any state library and local storage?

We can use React's Context API or lift the state up via a common parent component. Here's how you can achieve it:

## 1. Using React's Context API

The Context API allows you to share state across the component tree without prop drilling.

### Steps:

#### Create a Context:
```jsx
import React, { createContext, useState } from 'react';

export const MyContext = createContext();
```

#### Provide the Context in a Common Ancestor:
```jsx
const MyProvider = ({ children }) => {
    const [sharedState, setSharedState] = useState('Initial State');
    
    return (
        <MyContext.Provider value={{ sharedState, setSharedState }}>
            {children}
        </MyContext.Provider>
    );
};

export default MyProvider;
```

#### Consume the Context in Unrelated Components:

**In Component A:**
```jsx
import React, { useContext } from 'react';
import { MyContext } from './MyProvider';

const ComponentA = () => {
    const { sharedState, setSharedState } = useContext(MyContext);

    return (
        <div>
            <p>Component A: {sharedState}</p>
            <button onClick={() => setSharedState('Updated from A')}>Update State</button>
        </div>
    );
};

export default ComponentA;
```

**In Component B:**
```jsx
import React, { useContext } from 'react';
import { MyContext } from './MyProvider';

const ComponentB = () => {
    const { sharedState } = useContext(MyContext);

    return <p>Component B: {sharedState}</p>;
};

export default ComponentB;
```

#### Wrap Your Components with the Provider:
```jsx
import React from 'react';
import MyProvider from './MyProvider';
import ComponentA from './ComponentA';
import ComponentB from './ComponentB';

const App = () => (
    <MyProvider>
        <ComponentA />
        <ComponentB />
    </MyProvider>
);

export default App;
```

## 2. Lifting State Up

If both components have a common parent, you can lift the state to the nearest common ancestor and pass it down via props.

### Steps:

#### Lift State to Common Parent:
```jsx
const ParentComponent = () => {
    const [sharedState, setSharedState] = useState('Initial State');

    return (
        <div>
            <ComponentA sharedState={sharedState} setSharedState={setSharedState} />
            <ComponentB sharedState={sharedState} />
        </div>
    );
};

export default ParentComponent;
```

#### Pass State and Setters as Props:

**In Component A:**
```jsx
const ComponentA = ({ sharedState, setSharedState }) => {
    return (
        <div>
            <p>Component A: {sharedState}</p>
            <button onClick={() => setSharedState('Updated from A')}>Update State</button>
        </div>
    );
};

export default ComponentA;
```

**In Component B:**
```jsx
const ComponentB = ({ sharedState }) => {
    return <p>Component B: {sharedState}</p>;
};

export default ComponentB;
```

## 4. Which is created and rendered first, Real DOM or VDOM? And why?

**VDOM (Virtual DOM)** is created and rendered first before the Real DOM. Here's why:

- **Efficiency**: The VDOM is a lightweight representation of the Real DOM, allowing React to efficiently calculate the differences (diffing algorithm) and minimize changes to the Real DOM.
  
- **Performance**: Updating the Real DOM is relatively slow due to direct manipulation of the browser's rendering engine. By using the VDOM, React reduces performance costs by batching updates and only making necessary changes to the Real DOM.

This process ensures efficient and minimal updates to the Real DOM.
- The Virtual DOM (VDOM) is created and rendered first.
- The Real DOM is updated afterward based on the differences between the new and previous VDOMs. This approach improves performance and keeps the UI in sync with the component's state and props.

## 5. Is there any difference between browser paint and rendering?

Yes, there is a difference between browser paint and rendering in the context of how browsers process and display web content. Here's a breakdown of both concepts:

## 1. Rendering
Rendering refers to the entire process of converting HTML, CSS, and JavaScript into pixels on the screen. It includes several stages:

### Parsing
- The browser parses HTML and CSS to construct the DOM (Document Object Model) and CSSOM (CSS Object Model).

### Render Tree Construction
- The DOM and CSSOM are combined to create a render tree, which represents the structure and styling of elements to be displayed.

### Layout
- The browser calculates the position and size of each element on the page based on the render tree.

### Painting
- The browser fills in pixels based on the layout and styles.

## 2. Painting
Painting is a specific step within the rendering process. It involves:

- Filling in the pixels on the screen for each visual element, such as colors, text, images, and borders.
- Painting happens after the layout phase and can be triggered by changes to styles like color, background, or visibility that do not affect the layout.
  
**summary**
- Rendering is the broader process that includes layout, paint, and compositing.
- Painting is a specific phase where visual elements are drawn onto the screen after layout calculations are done.
In simple terms, **rendering** refers to the entire process, while **painting** is just one step within it.

## 6. What causes rerendering in React?
Rerendering in React can be caused by several factors, such as:
- **State changes**: When a component's state changes, React rerenders the component to reflect the updated state in the UI.
- **Props changes**: If a component receives new props from its parent, it will rerender to reflect the changes.
- **Context updates**: If a component consumes context data and the context value changes, the component will rerender.
- **Force update**: Using `forceUpdate()` in class components will manually trigger a rerender.

## 7. Can props changes cause rerendering?
Yes, **props changes can cause rerendering**. When a component receives new or changed props, React triggers a rerender to reflect the updated data. However, React uses an optimization technique (called **shallow comparison**) to check whether the props have actually changed before rerendering. If the props are unchanged, React skips the rerender.

## 8. When and why to use a state management library?
You should use a **state management library** (e.g., Redux, MobX, Zustand) when:
- **Complex state**: The application has a large or deeply nested state that needs to be accessed by many components across different levels of the component tree.
- **Non-local state**: When the state needs to be shared across different components that aren't directly related (without having to pass props through multiple layers).
- **Scalability**: As the application grows, managing state using React's built-in `useState` or `useContext` may become cumbersome and inefficient. A state management library can offer better scalability, maintainability, and optimization.
- **Predictable state**: Some libraries, like Redux, make the state changes more predictable and traceable by using actions, reducers, and a central store.

## 9. How to avoid using any state management library in React?
To avoid using a state management library in React, you can rely on the following techniques:
- **Lifting state up**: If two components need to share state, you can lift the state to their common ancestor and pass it down via props.
- **React Context API**: You can use React’s built-in Context API for global state management, especially if the state needs to be shared across multiple components at different levels of the tree.
- **Hooks**: Utilize React hooks like `useState`, `useReducer`, and `useContext` to manage local and global state without external libraries.
  
In simple applications, the native React features can suffice, and libraries may add unnecessary complexity.

## 10. Which React patterns do you use?
Some common **React patterns** that are often used include:
- **Component Composition**: This pattern promotes reusability by composing smaller components together to build complex UIs rather than using inheritance.
- **Higher-Order Components (HOC)**: A function that takes a component and returns a new component, often used for cross-cutting concerns like authentication or logging.
- **Render Props**: A pattern where a component provides a function as a prop that the child component can call to determine what to render, often used for sharing state or logic between components.
- **Controlled and Uncontrolled Components**: Controlled components are those where form data is handled by React (via state), and uncontrolled components handle their own state internally (using refs).
- **Custom Hooks**: A pattern that allows you to encapsulate and reuse logic between components.
- **Context API**: A pattern for passing down data without prop drilling.
- **Lazy Loading and Suspense**: A pattern for splitting up your code and loading components only when needed to improve performance.

Each of these patterns helps in making React applications more efficient, maintainable, and scalable.

## 11. Is using the map to render children components with "i" as an index in key a safe method?
Using the index (`i`) of the array as the `key` in React's `map()` function can **sometimes** be a safe method but **should generally be avoided** in certain cases. It can lead to issues, especially when:
- The order of items changes.
- Items are added or removed from the list.

In such cases, using the index as a `key` can cause **performance issues** and **incorrect component re-renders**. It’s recommended to use a **unique identifier** (e.g., a `UUID` or a unique property in the data) instead of the index to ensure that each component has a stable identity across renders.

### When to use the index:
- When the list is static and doesn't change order or elements.
  
### Why to avoid the index:
- When items are added, removed, or reordered, using the index as the `key` may cause issues with component state and performance.

## 12. Can you create a custom hook? If yes, in which cases to use it?
Yes, you can create custom hooks in React. A custom hook is a JavaScript function that allows you to reuse stateful logic across multiple components.

### When to use custom hooks:
- **Reusability**: When you have a logic that needs to be shared across multiple components, you can abstract it into a custom hook.
- **Separation of concerns**: When you want to isolate logic from UI components, like fetching data, handling forms, or subscribing to an event.
- **Complex logic**: When you need to manage a more complex state or lifecycle logic that doesn't belong directly in a component.

### Example:
```javascript
// useFetch.js
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(response => response.json())
      .then(data => {
        setData(data);
        setLoading(false);
      })
      .catch(error => {
        setError(error);
        setLoading(false);
      });
  }, [url]);

  return { data, loading, error };
}

export default useFetch;
```

## 13. How do you ensure that components re-render less?
To reduce unnecessary re-renders in React:

- **Memoize components**: Use `React.memo` for functional components to prevent re-renders when props haven't changed.
- **Use `shouldComponentUpdate` (Class Components)**: In class components, you can implement `shouldComponentUpdate` to manually control when a component should re-render.
- **Use `useMemo` and `useCallback` hooks**:
  - `useMemo` memoizes a value so that it is recalculated only when necessary.
  - `useCallback` memoizes a function so that it’s not recreated on every render.
- **Key prop in lists**: Ensure you are using a stable and unique `key` prop for list items to help React efficiently update the DOM.
- **Avoid passing new functions/objects/arrays in props**: Passing new functions or objects on each render causes unnecessary re-renders. Use `useCallback` and `useMemo` to memoize them.

## 14. What you don't like in React?
Some common criticisms of React include:

- **Complexity**: While React offers powerful features, managing the state, hooks, and component structure can become complex as the application grows.
- **Boilerplate**: React requires a lot of boilerplate, especially when using state management libraries or complex setups like Redux or Context API.
- **Learning curve**: React's learning curve can be steep for beginners, especially when dealing with hooks, advanced patterns, or third-party tools.
- **Performance issues with large applications**: While React is generally performant, large applications can run into performance bottlenecks if not optimized correctly (e.g., unnecessary re-renders or inefficient list rendering).

## 15. When and why do you go for Redux or other third-party tools? Why it couldn't be solved with `useReducer`, `Context API`?
You would go for **Redux** or other third-party tools when:

- **Complex state management**: When your application’s state is large, needs to be shared across many components, or has complex interactions.
- **Performance optimization**: Redux (and other libraries) offer optimizations like memoization and selective re-rendering, which may not be as efficient with `useReducer` or `Context API` in large apps.
- **Predictable state**: Redux allows for better state management with a central store, and actions to update state, which makes state changes more predictable and easier to debug.
- **Developer tools**: Redux provides powerful developer tools, like time-travel debugging, which help in tracking state changes and troubleshooting.

### Why `useReducer` and `Context API` may not work:
- **Performance**: In large applications, passing state through `Context API` can lead to unnecessary re-renders of all consumers, especially if the state changes frequently. `useReducer` and `Context API` do not have the same optimizations as Redux.
- **Scalability**: As your app grows, `useReducer` and `Context API` may become difficult to maintain because they are not as modular or feature-rich as Redux, which is designed for large applications.
- **Debugging**: Redux provides a better debugging experience due to its predictable flow and middleware support, making it easier to track changes in the state.

In small or medium-sized applications, `useReducer` and `Context API` might suffice, but for large-scale apps, Redux offers more robust solutions.

## 16. Have you ever used vanilla JS with a React app to offer a robust experience?
Yes, there are situations where **vanilla JavaScript** can be used alongside a React app to offer a more **robust** experience, especially when:
- **Integrating third-party libraries**: Sometimes, third-party libraries that are not built for React need to be used, such as jQuery plugins or other custom scripts.
- **Performance optimizations**: For certain performance-critical tasks, vanilla JS can be used to directly manipulate the DOM or handle specific tasks, while React manages the app's state and UI rendering.
- **Legacy code integration**: When working with existing legacy JavaScript code, it may be necessary to use vanilla JS in specific parts of the application.

However, in general, it's recommended to stick to React's declarative approach as much as possible for maintainability and consistency.

## 17. How `useState` works internally?
Internally, React's `useState` hook works as follows:

1. **State Initialization**: When the component is rendered for the first time, the state is initialized based on the argument passed to `useState()`. This value is stored internally in React’s fiber tree (the internal data structure React uses to manage components).
  
2. **State Setter Function**: React provides a setter function (the second element returned by `useState`) to update the state. When this setter function is called, React marks the component as "dirty" and schedules a re-render to update the UI with the new state value.

3. **State Persistence Across Renders**: React ensures that the state persists across re-renders by associating the state with the component's lifecycle. It does this by storing the state in memory and maintaining the correct value even as the component re-renders.

4. **Batching Updates**: React batches state updates to optimize performance. If multiple state updates happen within the same event cycle, React will only trigger one re-render.

### Example:
```javascript
const [count, setCount] = useState(0); // count is state, setCount is the setter function
```
## 18. Why does a false condition with `&&` during rendering print 0 on the screen?
In React, when you use the `&&` (logical AND) operator, the right-hand side of the expression is evaluated only if the left-hand side is true.

However, **if the left-hand side is false**, React will return the right-hand side expression as the result. In JavaScript, `0` is a **falsy** value, but React will render it since `0` is still a valid value to display (not `null` or `undefined`).

### Example:
```javascript
const isLoggedIn = false;
return <div>{isLoggedIn && 0}</div>; // Output: 0
```
## 19. Can you explain the rules of React and write a component following those?

React follows the following key rules:

1. **Only call hooks at the top level**: Hooks should be called in the top-level scope of a component and not inside loops, conditions, or nested functions.
2. **Only call hooks from React functions**: Hooks should only be called from functional components or custom hooks, not regular JavaScript functions or class components.
3. **Use `useEffect` for side effects**: When you need to perform side effects like fetching data, updating the DOM, or setting up event listeners, you should use the `useEffect` hook.
4. **State should be local**: Keep state as local as possible and only pass down props or lift state up when needed.

### Example of a functional component following these rules:

```javascript
import React, { useState, useEffect } from 'react';

const Timer = () => {
  const [seconds, setSeconds] = useState(0);

  // This effect will run on mount and update the timer every second
  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    return () => clearInterval(interval); // Cleanup on unmount
  }, []);

  return (
    <div>
      <h1>{seconds} seconds</h1>
    </div>
  );
};

export default Timer;
```
## 20. How do you structure your React.js project?

A common way to structure a React project is:

```
src/
│
├── components/          # Reusable components
│   ├── Header.js        # Specific component
│   ├── Button.js        # Specific component
│
├── pages/               # Page-level components
│   ├── Home.js          # Home page component
│   ├── About.js         # About page component
│
├── hooks/               # Custom hooks
│   ├── useFetch.js      # Custom hook for fetching data
│
├── assets/              # Static files (images, styles, fonts, etc.)
│
├── utils/               # Utility functions
│   ├── api.js           # Helper functions for API calls
│
├── App.js               # Root component
├── index.js             # Entry point of the app
├── styles/              # Global styles (CSS/SASS)
│   ├── main.css         # Main stylesheet
```

### Key Guidelines:
- **Components**: Keep your components small and reusable.
- **Hooks**: Place custom hooks in a dedicated folder for better organization.
- **Pages**: Use this folder to store components that represent entire pages in your app.
- **Assets**: Store static files such as images and fonts here.
- **Utils**: This folder contains utility functions or helper files.

This structure helps to keep the codebase organized and maintainable, especially as the project grows.

### 21. What design system do you follow?
I typically follow design systems such as **Material UI** or a custom design system based on the needs of the project. Material UI provides a set of pre-designed, responsive components that align well with modern design principles. Custom design systems are also great for maintaining consistency in design, especially for larger applications. These systems ensure uniformity, scalability, and ease of maintenance throughout the application.

### 22. What libraries do you use for unit and E2E testing and why choose those?
For unit testing, I prefer using **Jest** in combination with **React Testing Library**. Jest is a widely-used, fast, and reliable testing framework that provides built-in assertions and test runners. React Testing Library allows for testing React components in a way that focuses on user interactions, ensuring that the tests reflect how the components will behave in production.

For **end-to-end (E2E)** testing, I use **Cypress**. Cypress is a robust, fast, and easy-to-use E2E testing framework that allows for testing the entire application in a real browser environment. It provides excellent debugging features and is well-suited for testing user flows and integrations.

### 23. When was the last time you worked on Class-based components and if migrated how did you do this?
The last time I worked on class-based components was when I was building legacy React applications. In recent projects, I have migrated to functional components with hooks. The migration process typically involves:
- Replacing state management with the `useState` hook.
- Replacing lifecycle methods (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`) with the `useEffect` hook.
- Updating event handlers to use functions inside the component, avoiding the use of `this`.

The migration ensures that the components are cleaner, more maintainable, and can take advantage of React’s newer features like hooks.

### 24. What you don't like in React and when would prefer to use another framework?
Some of the challenges in React include:
- The steep learning curve when transitioning from class-based to functional components with hooks.
- Boilerplate code in larger applications, especially when managing complex state with libraries like Redux.
- React’s reliance on JavaScript for rendering, which might not suit developers with a strong design background.

I would prefer using another framework like **Vue.js** or **Svelte** when:
- I need a simpler setup and less boilerplate for smaller applications.
- I want to use a framework with a more intuitive templating system and built-in reactivity (Vue.js).
- I’m working on performance-sensitive applications where minimal bundle size and faster rendering are priorities (Svelte).

### 25. Do you use TailwindCSS? If yes, how is it better than CSS-in-JS, SASS, or normal CSS?
Yes, I use **TailwindCSS** for styling in React projects. TailwindCSS offers utility-first classes, which allow for rapid prototyping and styling directly in the markup. Compared to **CSS-in-JS**, **SASS**, or **normal CSS**, it offers the following benefits:
- **Faster development:** By using utility classes, developers can avoid switching between JavaScript and CSS files, leading to faster styling.
- **No context switching:** Unlike CSS-in-JS, Tailwind allows for all styles to be contained within the markup itself, making the code more concise and easier to understand.
- **Better maintainability:** With Tailwind, you can enforce consistent styles across the application using predefined utility classes.
- **Customizable and responsive:** Tailwind provides a highly customizable configuration and includes built-in responsive design utilities.

### 26. Why does calling a function such as `func()` in events inside put the app on the crash loop but not with `func` or `() => func()`?

## 1. Calling a Function Directly (func()):
When you write `onClick={func()}`, the function `func` is executed immediately during the render phase, not when the event occurs. This means `func` runs every time the component renders, which can lead to:

- **Side Effects**: If `func` updates state or props, it can trigger additional re-renders, potentially causing an infinite loop.
- **Crash Loop**: If `func` causes a state change that triggers a re-render, which in turn calls `func` again, you might enter a crash loop due to continuous re-renders.

**Example:**
```jsx
function handleClick() {
    setCount(count + 1); // Updates state and triggers a re-render
}

// Causes the function to run immediately, leading to potential issues
<button onClick={handleClick()} />
```

## 2. Passing the Function Reference (func):
Writing `onClick={func}` passes the function reference, meaning the function will only execute when the event (e.g., a click) actually occurs. This is the intended behavior for event handlers, as the function is called in response to the user's action, not during rendering.

**Example:**
```jsx
function handleClick() {
    setCount(count + 1); // Updates state when the button is clicked
}

// Passes the reference to the function, which is called when the button is clicked
<button onClick={handleClick} />
```

## 3. Using an Arrow Function (() => func()):
Using an arrow function `onClick={() => func()}` creates a new function that calls `func` when the event occurs. This is useful when you need to pass arguments to `func` or when `func` should be called with specific context or conditions. The arrow function itself is not called during rendering, but only when the event triggers.

**Example:**
```jsx
function handleClick() {
    setCount(count + 1); // Updates state when the button is clicked
}

// Arrow function calls handleClick when the button is clicked
<button onClick={() => handleClick()} />
```

## Summary:
- **func()**: Executes the function immediately during rendering, causing potential re-renders and crash loops.
- **func**: Passes the function reference, executing only when the event occurs.
- **() => func()**: Creates a new function that calls `func` during the event, allowing for flexibility like passing arguments.

To avoid crash loops, always pass a function reference or use an arrow function for event handlers, not the result of a function call.


