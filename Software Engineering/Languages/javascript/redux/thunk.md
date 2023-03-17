# [`redux-thunk`](https://github.com/gaearon/redux-thunk)

* A `thunk` is a function that wraps an expression - this delays execution of that expression

```javascript
// can call thunk at any time afterwards for evaluation
const thunk = () => 1 + 2;
// notThunk is evaluated immediately
const notThunk = 1 + 2;
```

* `redux-thunk` was built to dispatch action creators that return a function instead of an action object
  * Can be used to to dispatch only if certain conditions are met or for `async` actions (like fetching from an API or after a timeout)

```javascript
// So this will return a function that takes a "dispatch" argument
// This function returns a Promise
// This Promise is the result of
// 1. Fetching from some API
// 2. Taking the response and dispatching some action based on the response
// 3. Or if the response is an error, dispatching an action to handle that error
// The resolved value of the Promise is whatever those dispatches return
const fetchThunkExample = (thing) => (
  (dispatch) => fetch(`https://thing.api.com/api/v1/some=${thing}`).then(do  => dispatch(something(do), error => dispatch(handleError(error))));
);
```

## Source Code

Here's [the `redux-thunk` source](https://github.com/gaearon/redux-thunk/blob/master/src/index.js#L1)

```javascript
function createThunkMiddleware(extraArgument) {
  return ({ dispatch, getState }) => next => action => {
    if (typeof action === 'function') {
      return action(dispatch, getState, extraArgument);
    }

    return next(action);
  };
}

const thunk = createThunkMiddleware();
thunk.withExtraArgument = createThunkMiddleware;

export default thunk;
```

Basically, it looks like `createThunkMiddleware` takes a variable
  * returns `function` that takes an object that has `dispatch` and `getState`
    * which returns a `function` that takes a parameter called `next`
      * which returns a `function` that takes a parameter called `action`
        * which executes the `action` parameter with `dispatch`, `getState`, and the passed in `extraArgument` and returns the result
        * or it returns the execution of `next` on the `action` parameter

Here's a similar method

```javascript
const logger = store => next => action => {
  console.log('store: ', store);
  console.log('next: ', next);
  console.log('action: ', action);
}
logger('fuck')('you')('jae');

// outputs
// store:
// fuck
// next:
// you
// action:
// jae
```

So `thunk` is probably going to be used like `thunk(store)(store.dispatch.next)(someAction)` where `store` has `dispatch` and `getState` methods
