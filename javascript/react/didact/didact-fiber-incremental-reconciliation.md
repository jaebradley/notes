# [Didact Fiber: Incremenetal Reconciliation](https://engineering.hexacta.com/didact-fiber-incremental-reconciliation-b2fe028dcaec)
    "lodash": "^4.17.15",

* When a browser's main thread is busy, critical tasks that take only a brief period of time might be delayed for an unacceptable amount of time
* This affects reconciliation because once it begins, it's not going to stop.
  * If reconciliation takes an abnormal amount of time there might be performance degradation for animation
  * Rewrite reconciliation to use a different data structure that makes it easier to pause work and replace recursion with loops instead

## Scheduling

* Each time `requestIdleCallback` is called a callback function is returned that will execute the next piece of work to complete
  * A `deadline` parameter indicates how much time is available to code
* `performUnitOfWork` executes the actual reconciliation (i.e. it takes a piece of work, runs this work, and returns all information to resume work in the future)

## Fiber object

* A `Fiber` object is just a POJO
* It contains `parent`, `child`, `sibling` properties to build tree of `Fiber`s that describes the component tree
* `stateNode` property contains reference to component instance (which is DOM element or instance of user-defined class component)
* Host components
  * Their `tag` property is `HOST_COMPONENT`
  * Their `type` property is name of DOM element that they represent
  * `props` will be attributes and event listeners for the element
* Class components
  * Their `tag` property is `CLASS_COMPONENT`
  * Their `type` property is a reference to the user-defined class extending `Component`
* `alternate` property
  * This represents the corresponding `Fiber` from previous component tree
  * For new components, this property may be undefined
* `effectTag` property keeps track of the type of DOM change necessary (`PLACEMENT`, `UPDATE`, `DELETION`)

## Using the `updateQueue`

* Every call to `render` or `scheduleUpdate` pushes a new update to the `updateQueue`
* Each of these pushes triggers a deferred call to perform some work
* When performing work, if work takes longer than deadline, work loop is exited and `nextUnitOfWork` is updated with a unit of work to be resumed for next time
* If there are updates to process or `nextUnitOfWork` exists, the work loop will be called again
* `performUnitOfWork` builds the WIP tree for the update it's working on 
  * It builds this tree one `Fiber` at a time
* When `performUnitOfWork` finishes all work for the current update, returns `null`, and defines all pending commits to the `pendingCommit` variable
* These pending commits are committed to the DOM outside the work loop so that UI mutations are executed synchronously and "in one go" to avoid any partial states

## Performing work

* `performUnitOfWork` walks the WIP tree
* `beginWork` creates new children for a fiber and then returns first child to become next unit of work
* If no children exist, call `completeWork` and return sibling as next unit of work
* If no sibling exists, traverse parents, calling `completeWork` at each step until there is a sibling that will become next unit of work or until root is met
* `beginWork` creates a `stateNode` and gets component children to pass them to reconciliation
* Updating host components creates a DOM node if needed and then it reconciles child elements from it's `props`
* Updating class components involves creating a new instance by calling component constructor if necessary and updating `props` and `state` to call `render` function if necessary
  * Also validates if necessary to call render (simple version of `shouldComponentUpdate`)

## Performing reconciliation

* Matches first old fiber child (`wipFiber.alternate.child`) with the first child element and the second old fiber (`wipFiber.alternate.child.sibling`) to the second child element, etc.
* If types match, can keep old `stateNode`, create new fiber based on old fiber, create `UPDATE` `effectTag`, append new fiber to WIP tree
* Different type, create new fiber that doesn't have an `alternate` property and won't have a `stateNode` (which will be created in `beginWork`, and will have a `PLACEMENT` `effectTag`.
* If more old children than new children, old fiber is tagged with `DELETION`, should not be part of WIP tree, and need to add to `effects` list to not lose track of it

## Finishing work

* `effects` are combined so that root `effects` list has all fibers that have an `effectTag`
* `commitAllWork` iterates through all `effects`
  * `PLACEMENT` appends fiber's `stateNode`
  * `UPDATE` passes `stateNode` old props and new props
  * `DELETION` if host component, remove it, if class component, find all host components in sub-tree and remove them

