# [React `useEffect` Hooks in Action](https://medium.com/better-programming/https-medium-com-mayank-gupta-6-88-react-useeffect-hooks-in-action-2da971cfe83f)

* A side-effect is any execution that affects something outside the scope of the function being executed
  * Function modifying global variable (since global variable doesn't belong in scope of current function)
  * API calls for data
  * Subscribing to some type of observable
  * Updating a DOM element
* `useEffect` takes a function as an input, executed after the initial rendering and re-rendering of the component
  * Important because using `useState` hook to update a variable will trigger re-render which will re-invoke `useEffect` function
* `useEffect` can take advantage of variables and functions in component via closure
* If want to only call `useEffect` in certain cases, then specify second parameter which represents a list of state or props which will trigger the function if updated
  * `[foo, bar]` means that effect is not executed unless `foo` or `bar` is updated (React checks reference)
  * `[]` indicates that effect will only be executed during initial rendering
* By returning a function, `useEffect` can specify a cleanup operation
  * Cleanup function will be executed on unmount
  * This could be unsubscribing from a subscription
  * If don't unsubscribe, this could create multiple subscriptions

