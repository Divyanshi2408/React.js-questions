# React Interview Questions

## 1. How many instances of VDOM always exist in React?
In React, **two instances of the Virtual DOM (VDOM)** are created for each component:
- **Previous VDOM:** Represents the VDOM state before an update.
- **Current VDOM:** Represents the VDOM state after the update.

React compares these two instances during the **reconciliation process** to determine the necessary changes to update the Real DOM efficiently.

## 2. At what step in the React component lifecycle does a VDOM get created?
The Virtual DOM (VDOM) is created during the **render** phase of the React component lifecycle:
- For **class components**, the VDOM is created during the `render()` method.
- For **functional components**, the VDOM is created when the function is called.

This VDOM is used to determine how the Real DOM should be updated during the **commit phase**.

## 3. How to pass state between two components, not related to each other, without using any state library and local storage?
To pass state between two unrelated components without using a state management library or local storage, you can use a **common parent component** or a **global event system**.

### Example using a custom event system:
```javascript
// EventManager.js
const EventEmitter = {
  events: {},
  subscribe(eventName, callback) {
    if (!this.events[eventName]) {
      this.events[eventName] = [];
    }
    this.events[eventName].push(callback);
  },
  emit(eventName, data) {
    if (this.events[eventName]) {
      this.events[eventName].forEach(callback => callback(data));
    }
  }
};
export default EventEmitter;

// ComponentA.js
import EventEmitter from './EventManager';

function ComponentA() {
  const handleClick = () => {
    EventEmitter.emit('customEvent', { data: 'Some data' });
  };
  return <button onClick={handleClick}>Send Data</button>;
}

// ComponentB.js
import { useEffect } from 'react';
import EventEmitter from './EventManager';

function ComponentB() {
  useEffect(() => {
    EventEmitter.subscribe('customEvent', (data) => {
      console.log('Received data:', data);
    });
  }, []);

  return <div>Listening for Data</div>;
}
```
## 4. Which is created and rendered first, Real DOM or VDOM? And why?

**VDOM (Virtual DOM)** is created and rendered first before the Real DOM. Here's why:

- **Efficiency**: The VDOM is a lightweight representation of the Real DOM, allowing React to efficiently calculate the differences (diffing algorithm) and minimize changes to the Real DOM.
  
- **Performance**: Updating the Real DOM is relatively slow due to direct manipulation of the browser's rendering engine. By using the VDOM, React reduces performance costs by batching updates and only making necessary changes to the Real DOM.

This process ensures efficient and minimal updates to the Real DOM.
