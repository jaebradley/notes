# [How To Use React Context Effectively](https://kentcdodds.com/blog/how-to-use-react-context-effectively)

* Example is a simple counter that uses context to hold it's state
* Need to provide a `Provider` component that children will use to "consume" state (and `dispatch`, but more on that later)

```jsx
<CountProvider>
  <CountDisplay />
  <Counter />
</CountProvider>
```

* Store state using `useReducer` and expose two contexts - one for the actual count state and the other for dispatching count-related actions (like `increment` or `decrement`)

```jsx
const [state, dispatch] = useReducer(countReducer, { count: 0 });
return (
  <CountStateContext.Provider value={state}>
    <CountDispatchContext.Provider value={dispatch}>
      {children}
    </CountDispatchContext.Provider>
  </CountStateContext.Provider>
);
```

## Custom Consumer Hook

* Instead of `const someContext = useContext(TheContext)` can use a custom consumer hook to add error-handling and to avoid exposing the context directly via export

```javascript
function useCountState() {
  const context = useContext(CountStateContext);
  if (context === undefined) {
    throw new Error('some descriptive error')
  }
  return context;
}

// do same for useCountDispatch()
```

## Async Actions

* Add a helper function in whatever context module you're creating that accepts a `dispatch` argument

```javascript
// user-context.js

async function updateUser(dispatch, user, updates) {
  dispatch({ type: 'starting' });
  try {
    const updatedUser = await userClient.updateUser(user, updates);
    dispatch({ type: 'successfully updated', updatedUser });
  } catch (error) {
    dispatch({ type: 'failed to update', error });
  }
}

export {UserProvider, updateUser}
```

* Example of how the hooks are consumed
* `CountContext` is _not_ exported so that there's only one way to provide context value and only one way to consume it

```jsx
function CountDisplay() {
  const {count} = useCountState()
  return <div>{`The current count is ${count}`}</div>
}

function Counter() {
  const dispatch = useCountDispatch()
  return (
    <button onClick={() => dispatch({type: 'increment'})}>
      Increment count
    </button>
  )
}
```