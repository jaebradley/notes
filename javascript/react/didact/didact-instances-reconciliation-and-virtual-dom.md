# [Didact: Instances, reconciliation, and virtual DOM](https://engineering.hexacta.com/didact-instances-reconciliation-and-virtual-dom-9316d650f1d0)

* Preserve previous rendered tree of elements to compare with updated tree of elements to identify which elements need to be updated - this is the virtual DOM
  * Can't use didact elements because need to keep reference to actual DOM node
* Didact instances are plain JS objects that represent an element rendered to the DOM
  * They have three properties `element`, `dom`, and `childInstances`
  * Each DOM node should have a matching instance
* Basic `render` function (will modify in near future)
  * Walks through current didact element tree, creating instances if previous instance doesn't exist
    * Instantiation includes
      * creating a DOM node
      * adding event listeners
      * setting DOM node properties
      * iterating over children and creating instances
* Need to reuse DOM nodes / update existing instances
  * in an `updateDomProperties` function, remove old event listeners and properties, and add new ones
    * This function will be called when instantiating instances
  * Add validation in `reconcile` function to check if previously rendered element has same `type` as the one that's being evaluated / rendered
    * if instance is `null` or `undefined` create new instance and append it to the DOM
    * if instance exists and matches the previous instance type, update it's DOM properties and assign the DOM reference to the instance's element
    * if instance type does not match, create a new instance object and replace old DOM node with new instance
* Child reconciliation - naive approach uses array index of children to determine if need to reuse DOM nodes or not
  * React uses `key` property to match children
  * In a `reconcileChildren` function, each child instance is iterated through and recursively reconciled, comparing the previous child instance at that index and the next child element at that index (defined via `props`)
    * each child reconciliation will return an instance, which will be added to an array of new child instances
  * If a child is added, the reconciliation function will be called where the previous instance will be `undefined` / `null` which is handled by existing reconciliation logic
  * If a child is removed, the reconciliation function will be called where the previous instance exists and next element is `undefined` / `null`
    * Add check in reconciliation logic where if previous instance exists but next element is not defined, remove the instance from the DOM
    * new child instances need to filter out "instances" that have a `null` value
