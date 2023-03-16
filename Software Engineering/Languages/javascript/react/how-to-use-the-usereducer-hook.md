# [How To Use The useReducer Hook](https://daveceddia.com/usereducer-hook-examples/)

* A "reducer" is a fancy word for a function that takes 2 values and returns a single value
  * Like taking an array of values and using JavaScript's functional `Array#reduce` function to combined those values
* `useReducer` callback takes a state argument as first arg and an action argument as second arg and returns new state
  * Second argument to `useReducer` is the initial state
* `useReducer` returns a two element array
  * First element is the current state and second element is a `dispatch` function to dispatch an action

```jsx
// Example of a simple useReducer that just sums a value
// The usage later would simply be something like dispatch(1), which would increase the sum by 1

const [sum, dispatch] = useReducer((state, action) => {
  return state + action;
}, 0);
```

* More complex examples could pass an action with a `type` property and then the callback would `switch` on this `type` property to execute other state changes

```jsx
// Example for adding to a todo list

const [items, dispatch] = useReducer((state, action) => {
    switch (action.type) {
      case 'add':
        return [
          ...state,
          {
            id: state.length,
            name: action.name
          }
        ];
      default:
        return state;
    }
  }, []);

// Somewhere in the component, `dispatch({ type: 'add', name: input.name })` would be executed
```