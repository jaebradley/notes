# [Didact: Components and State](https://engineering.hexacta.com/didact-components-and-state-53ab4c900e37)

* Create `Component` base class that components extend
  * Has `constructor` with `props` and `setState` method that receives `partialState` argument to update component state
* Also need a `createPublicInstance` method that instantiates the component class defined and sets an object property that references the virtual DOM instance associated with the component instnace
  * This reference is used to only update the sub-tree when the public instance state changes

```javascript
setState(partialState) {
  this.state = Object.assign({}, this.state, partialState);
  // internal instance references virtual DOM instance
  updateInstance(this.__internalInstance);
}

updateInstance(internalInstance) {
  const parentdom = internalInstance.dom.parentNode;
  const element = insternalInstance.element;
  // Only updating instance sub-tree
  reconcile(parentDom, internalInstance, element);
}
```

* Component internal instances and dom element instances are different
  * Component internal instances can only have one child (from `render`) while dom element instances can have multiple children
  * Component internal instances need to have a reference to the public instance so `render` function can be called during reconciliation

## Reconciliation
 
* Component internal instance will use updated element objects `props` and call `render` on public instance reference to generate new child element object
* Then will use old / existing child instance reference and reconcile that using the newly created child element object
* Reconciliation function will return a new child instance object
* This instance object will then have it's `dom`, `childInstance`, and `element` properties set
