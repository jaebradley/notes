# [React Hooks: What's Going To Happen To My Tests](https://kentcdodds.com/blog/react-hooks-whats-going-to-happen-to-my-tests)

* Doing things like `instance()` or `state()` won't work when refactoring components from class components with state / lifecycles to functional components with hooks
* Simple `Counter` component where the tests assert that some part of the _external_ / UI changed in some expected way
  * "assert that button text says 1 instead of 0 after firing some `click` event on the button"
* New `Counter` component that interacts with local storage
  * on mount it reads from local storage and updates component state
  * when the count increments, it updates the value from local storage
  * in the test, you'd assert that the button text increments on click and that the local storage value increments on click
* Converting to `useEffect` is not the same as `componentDidMount` and `componentDidUpdate`
  * `useEffect` is scheduled to run at a later time vs. setting the value of local storage synchronously after render
  * > Unlike `componentDidMount` or `componentDidUpdate`, effects scheduled with `useEffect` don't block the browser from updating the screen. This makes your app feel more responsive. The majority of effects don't need to happen synchronously.
  * There is a `useLayoutEffect` for measuring layout in a synchronous manner
  * So using `useEffect` is better for performance

## Render Props Component

```jsx
class Counter extends React.Component {
  state = {count: 0}
  increment = () => this.setState(({count}) => ({count: count + 1}))
  render() {
    return this.props.children({
      count: this.state.count,
      increment: this.increment,
    })
  }
}
// usage:
// <Counter>
//   {({ count, increment }) => <button onClick={increment}>{count}</button>}
// </Counter>

// __tests__/counter.js
import React from 'react'
import {render, fireEvent} from '@testing-library/react'
import Counter from '../counter.js'
function renderCounter(props) {
  let utils
  const children = jest.fn(stateAndHelpers => {
    utils = stateAndHelpers
    return null
  })
  return {
    ...render(<Counter {...props}>{children}</Counter>),
    children,
    // this will give us access to increment and count
    ...utils,
  }
}
test('counter increments the count', () => {
  const {children, increment} = renderCounter()
  expect(children).toHaveBeenCalledWith(expect.objectContaining({count: 0}))
  increment()
  expect(children).toHaveBeenCalledWith(expect.objectContaining({count: 1}))
})
```

* Trick is to get access to the returned props so that the props can be validated in the test
* Can rewrite counter logic to own custom hook that uses `useState` and exposes `count` and `increment` API
* Then can use custom `useCounter` hook in `Counter` render prop component like
  * `const Counter = ({children, ...props}) => children(useCounter(props))`
