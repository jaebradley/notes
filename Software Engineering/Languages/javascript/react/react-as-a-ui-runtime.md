# [React as a UI Runtime](https://overreacted.io/react-as-a-ui-runtime/)

## React Elements

* Tree of UI elements is called "host tree" as it is part of host environment outside of React
  * Host tree has own API that React is a layer on top of
  * Host tree consists of nodes or "host instances"
    * In DOM environment, host instances are DOM nodes
* React elements are JavaScript objects that describe a host instance

```javascript
// JSX is syntactic sugar over React elements
// <button className="blue" />

{
  type: 'button',
  props: { className: 'blue' },
}
```

* React elements can form a tree

```javascript
// JSX
// <dialog>
//  <button className="blue" />
//  <button className="red" />
// </dialog>

{
  type: 'dialog',
  props: {
    children: [{
      type: 'button',
      props: { className: 'blue' },
    },
    {
      type: 'button',
      props: { className: 'red' },
    },
  }],
}
``

* React elements are immutable
* Can't change children or a property of a React element
  * If need to render something different, need to describe a new React element tree created from scratch

## React Renderer

* Tells React to render a particular React element tree inside a container host instance
* `ReactDOM.render(reactElement, domElement)` means "React, make domElement host tree match reactElement`
* React then asks React DOM renderer to create a host instance based on the React element defined (i.e. it's `type` and `properties`)
  * Will also apply rendering recursively to any child React elements

## Reconciliation 

* `ReactDOM.render(reactElement, domElement)` is called with same `domElement`, but `reactElement` might change (`properties` change or `type` changes)
* The process of figuring out what to do to host instance tree based on updates is called reconciliation
* Naive strategy is to blow everything away and recreate tree from scratch
  * This is slow and loses informatoin like what was in focus, scroll state, etc.
* If an element type is in the same place in the tree between renders, then React reuses existing host instance

### Conditional Rendering

```jsx
function Form({ showMessage }) {
  let message = null;
  if (showMessage) {
    message = <p>Foobar</p>;
  }

  return (
    <dialog>
      {message}
      <input />
    </dialog>
  );
}

// In object form
function Form({ showMessage }) {
  let message = null;
  if (showMessage) {
    message = {
      type: 'p',
      props: { children: 'Foobar' },
   };
  }

  return {
    type: 'dialog',
    props: {
     children: [
      message,
      { type: 'input', props: {} },
    ],
  };
}
```

* Whether `showMessage` is `true` or not, the `input` being the second child doesn't change tree position between renders
* What _does_ change type is the first child, which is `null` and then `p` (which _will_ need to be rendered / created)

### Arrays

* `key` prop tells React that it should consider an item to be the same even if it has different positions beween renders

## Rendering

* Reconciliation is non-blocking (render phase) and asynchronous
* Host tree operations should be executed in single swoop (commit phase) and synchronous
* When a parent calls `setState`, `React` will reconcile whole children subtree
  * React doesn't know if update to parent will affect children or not, hence the re-rendering
  * Fine for small / medium-sized subtrees
  * Use `React.memo` to memoize a subtree and reuse previous render results
* React components are in a "call tree" rather than a "call stack"
  * Need to keep references to "call tree"s of particular components

## Context

* `Context` is like a wormhole that lets you put something on the highest level and have every child at the bottom be able to read it

## Effects

* React defers executing effects until after browser re-paints
* Effects run after first render and every subsequent render
  * Can skip an effect based on if a variable changed (or not)
* Hooks are implemented as an linked lists
  * When `useState` is called, pointer is moved to next item
  * When a component's "call tree" frame is exited, the resulting list is saved to the reference until the next render

