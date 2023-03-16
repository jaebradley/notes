# [React Fiber Architecture](https://github.com/acdlite/react-fiber-architecture)

* Reconciliation and rendering are separate phases
* Reconciler figures out which parts of the app need to be updated and the renderer actually goes applying the updates
* This means that both React DOM and React Native can use the core reconciler, but they'll use different renderers
* React Fiber reimplements the reconciler

## Fiber

* The driving force behind React Fiber is that in the future there should be better logic around scheduling work (i.e. re-renders )
  * renders based on UI-interactions should be prioritized over off-screen renders, for example
* In order to accomplish it's goals, Fiber needs to
  * pause work
  * come back to previously paused work
  * assign priority to work
  * reuse work
  * abort work if it's no longer needed
* What does "work" mean? That's what a "fiber" is - a unit of work
* A React app is "simply" a function invocation that calls other functions that calls other functions, etc.
* This results in a call stack where each new function call results in a new stack frame - this stack frame represents the work done by that function
* New browsers have ways of prioritizing the execution of stack frames (`requestIdleCallback` and `requestAnimationFrame`)
* React Fiber implements a "virtual call stack" for React components where each "fiber" is a stack frame
* By reimplementing the call stack, you can keep **all stack frames in memory and execute them whenever you want**

### Structure

* A fiber corresponds to a stack frame and an instance of a component
* A fiber is a JavaScript object with a `type`, `key`, `child`, `sibling`, `return`, `pendingProps`, `memoizedProps`, `pendingWorkPriority`
  * `type` and `key` are copied over from a React `element` - the former identifies the type of component and the latter is used for reconciliation
  * `child` refers to the `return` value of the `render` method
  * `sibling` exists when multiple children are `render`ed
  * `return` is the fiber that the program should return to after processing the current fiber (return address of a stack frame)
  * If `props` are the arguments of a function, `pendingProps` are the arguments set at the start of execution and `memoizedProps` are the arguments set at the end of execution
    * If these values are the same, the previous fiber's work can be reused
  * `pendingWorkPriority` is a number that indicates the priority level of a fiber - this value is used by the scheduler to find the next piece of work
