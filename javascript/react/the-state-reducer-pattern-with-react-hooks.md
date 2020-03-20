# [The State Reducer Pattern With React Hooks](https://kentcdodds.com/blog/the-state-reducer-pattern-with-react-hooks)

* Example using toggling users
* Start with using `useState` and then creating a custom toggle hook

```jsx
function useToggle() {
  const [on, setOnState] = React.useState(false)
  const toggle = () => setOnState(o => !o)
  const setOn = () => setOnState(true)
  const setOff = () => setOnState(false)
  return {on, toggle, setOn, setOff}
}
```

* But if there needs to be a feature where the user can't click a particular button more than X times in a row
  * Can solve quickly by introducing an `if` statement
  * But can also consider inverting control of the `useToggle` hook so that it takes some method that will "hook" into every state update and potentially modify it before it happens

```jsx
useToggle({
  modifyStateChange(currentState, changes) {
    if (tooManyClicks) {
      // other changes are fine, but need to keep on state unchanged
      return {...changes, on: currentState.on};
    }

    return changes;
  }
})
```

* Doesn't allow us to scope changes by their type (so if `tooManyClicks` is true, but there are other buttons that are allowed to change the state of `on`)
* So use a reducer, that takes a `type` and then from this `type`, can correctly "scope" the `tooManyClicks` condition

```jsx
reducer(currentState, action) {
  if (tooManyClicks && action.type === 'TOGGLE') {
    // etc
  }
}
```

* Can convert state hook to a reducer hook

```jsx
function toggleReducer(state, action) {
  switch (action.type) {
    case 'TOGGLE': {
      return {on: !state.on}
    }
    // etc.
  }
}

function useToggle() {
  const [{on}, dispatch] = useReducer(toggleReducer, {on: false});

  const toggle = () => dispatch({type: 'TOGGLE'});

  // etc.

  return {on, toggle};
}
```

* Now we can allow inversion of control (i.e. allow developer to modify state by having `useToggle` pass in it's own custom reducer and then calling that reducer after identifying changes from the `toggleReducer`)
  * This allows the developer to see what the "vanila" state changes are going to be, and modify them in certain cases, without having to continually rewrite `toggleReducer`

```jsx
function useToggle({reducer}) {
  const [{on}, dispatch] = useReducer(
    // inline "reducer" definition that combines toggleReducer and custom reducer
    (state, action) => {
      const changes = toggleReducer(state, action);
      return reducer(state, {...action, changes});
    },
    {on: false},
  );

  // etc.
}
```

* Can add a default `reducer` function that'll just return the `toggleReducer` changes
