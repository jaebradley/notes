# [Virtual DOM in ReactJS](https://hackernoon.com/virtual-dom-in-reactjs-43a3fdb1d130)

## What happens when the DOM changes?

![alt-text](https://cdn-images-1.medium.com/max/1600/1*4s99HTDCA0UUyOc39k5dag.png)

* Browsers have HTML and CSS parsers to create the DOM tree and style rules - these get combined to create a render tree in a process called "attachment".
* What happens for `document.getElementById('elementId').innerHTML = "New Value"`
  * Browser parses HTML
  * Finds matching element
  * Removes child of `#elementId`
  * Adds the `New Value` node in the DOM tree
  * Recalculates CSS for parent and child
  * Updates the layout (the exact coordinates for each element)
  * Traverse render tree, paint on browser
* This occurs for every single DOM change

## Virtual DOM

* In-memory representation of DOM
* AngularJS uses a "dirty checking" process that runs (it seems) on some loop
  * It checks the entire model - as the application grows this can hamper performance
* ReactJS uses "observables" to identify modified components - whenever `setState` is called within that component, ReactJS marks it as dirty and re-renders it
* When `setState` is called, React creates the entire Virtual DOM from scratch (apparently, creating trees from scratch is not that much of a performance burden)
* React maintains two Virtual DOMs, one with the updated state, and one with the previous state

### React Diffing Algorithm

* Normally, finding the minimum number of modifications between two trees has a complexity of `O(N^3)`
* React's diffing algorithm has a complexity of `O(N)`

* Re-renders all children if parent state has changed, even if child is not modified
  * Can use `shouldComponentUpdate` lifecycle method to prevent unwanted re-renders
* Breadth First Search through Virtual DOM
  * If `H` is child component of `B` and `B` and `H` change, then `H`'s change is "taken care of" by `B`'s re-render and we don't need to re-render `H`.
* Reconciliation
  * Two elements of different types will produce different trees
  * The `key` prop determines which child elements may be stable across re-renders
