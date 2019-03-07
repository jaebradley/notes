# [React Component Lifecycle](http://busypeoples.github.io/post/react-component-lifecycle/)

## Render

* First two methods called are `getDefaultProps` and `getInitialState`
  * Called only once when initially rendering component
  * Accessible within component via `this.props` and `this.state`, respectively
* `componentWillMount`
  * Called before `render` method
  * Setting state here will not trigger re-render
* `render`
  * Pure function that returns markup
  * `props` nor `state` should be modified inside this function
* `componentDidMount`
  * Called after `render` method
  * DOM can be accessed in this method, so DOM manipulation or data fetching operations should occur in this lifecycle method
  * DOM interactions should happen in this phase and not in the `render` method

## State Changes

* `shouldComponentUpdate`
  * Called before `render`
  * Returns a `boolean` that determines if re-rendering is necessary
  * Not called on initial render (obviously)
  * Can use current props / state to determine if re-rendering is necessary
* `componentWillUpdate`
  * Called after `shouldComponentUpdate` returns `true`
  * Takes `nextProps` and `nextState` - use both these objects to prepare for update
  * Should not trigger an update (i.e call `setState`)
* `componentDidUpdate`
  * Called after `render` method
  * Takes `prevProps` and `prevState` - use both these objects to manipulate DOM

## Prop Changes

* `componentWillReceiveProps`
  * First method called when the props have changed and not an initial render
  * Takes a `nextProps` object that enables state updates, without triggering re-render

## Unmounting

* `componentWillUnmount`
  * Called before component leaves DOM
  * Allows the clean up of timers, for example

