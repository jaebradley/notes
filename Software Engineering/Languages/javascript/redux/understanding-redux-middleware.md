# [Understanding Redux Middleware](https://medium.com/@meagle/understanding-87566abcfb7a)

* `dispatch` function is responsible for sending actions to one or many reducer functions for state changes
  * Redux reducer functions take a `state` and an `action` parameter and return a new state

## Composing Functions

```javascript
const greet = x => `Hello, ${x}`;
const emote = x => `${x} :)`;
const happyGreeting = compose(greet, emote);
happyGreeting('Mark') // 'Hello, Mark :)'
```

## Currying Functions

```javascript
const curriedAdd = a => b => a + b;
const addTen = curriedAdd(10);
addTen(10); // 20
```

## Simple Logger Middleware

* Redux middleware signature is `middleware:: next -> action -> some return value`

```javascript
// returns new function with the next parameter
// used to compose the next chained middleware function
// or main dispatch function
const createLogger = ({ getState }) => (
  (next) =>
    // this function accepts action object
    // which can be read or modified before sending to next middleware function
    // at end of chain, main dispatch function is invoked with action object
    (action) => {
      const console = window.console;
      const previousState = getState();
      const returnValue = next(action); // all downstream middleware functions are invoked; last function called is store.dispatch, which calls reducer functions, and updates state
      const nextState = getState();
      const actionType = String(action.type);
      const message = `action ${actionType}`;
      console.log(previousState);
      console.log(action);
      console.log(nextState);
      return returnValue;
    }
)
```

## `applyMiddleware.js`

```javascript
export default function applyMiddleware(...middlewares) {
  // next argument is function used to create store
  return (next) =>
    (reducer, initialStore) => {
      var store = next(reducer, initialState);
      var dispatch = store.dispatch; // original dispatch that gets overridden by composed dispatches from middleware
      var chain = [];
      var middlewareAPI = {
        getState: store.getState,
        dispatch: (action) => dispatch(action),
      };
      chain = middlewares.map(middleware => middleware(middlewareAPI));
      dispatch = compose(...chain, store.dispatch);
      // original dispatch function gets extended / overridden
      return {
        ...store,
        dispatch,
      };
    }
}

// compose function looks like
// export default function compose(...functions) {
//   return functions.reduceRight((compose, f) => f(composed));
// }
// Order matters as compose results in something like
// middlewareI(middlewareJ(middlewareK(store.dispatch)))(action)
```

## Application

```javascript
import {
  createStore,
  applyMiddleware,
} from 'redux';
import loggerMiddleware from 'logger';
import rootReducer from '../reducers';
const createStoreWithMiddleware = applyMiddleware(loggerMiddleware)(createStore);
export default function configureStore(initialState) {
  return createStoreWithMiddleware(rootReducer, initialState);
}
const store = configureStore();
```
