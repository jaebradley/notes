# [Design Principles](https://reactjs.org/docs/design-principles.html)

## Scheduling

* API user does not call components directly - instead user creates components that React renders sometime in future (i.e "schedules")
* This means that React can delay the render of certain components in the future for performance reasons (limiting frame drop, for example)
  * Components that are offscreen
  * Batch updating components where data is being updated faster than frame rate
  * Prioritizing rendering of components that user is interacting with vs. "background" UI updating
* React is not taking advantage of this right now, but could in future
* Currently, on each tick, React is walking updated UI tree and calling render functions
* Pull-based theme vs. push-based - React wants full control of updating tree when it's ready vs. allowing "push"-based updates (when things are first / actually updated)
  * One of React's key principles is that the amount of user-logic should be minimized - this generally means that React should have full control over things like scheduling updates
* This is why `setState` is async - should be viewed as "scheduling some update in the future"
