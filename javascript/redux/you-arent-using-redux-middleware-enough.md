# [You Aren't Using Redux Middleware Enough](https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6)

* Middleware is function with signature

```javascript
storeInstance 
  => functionToCallWithAnActionThatSendsToNextMiddlewareOrReducerIfLastMiddleware 
    => actionThatDispatchWasCalledWith 
      => valueToUseAsreturnValueOfDispatchCall
```

* The most basic middleware which only forwards action is

```javascript
const middleware = store => next => action => next(action)
```

* A more complicated middleware is

```javascript
// Any dispatches with action type 'SOME_TYPE' will return 'SOME CUSTOM TEXT' as result of dispatch
// It will also NOT dispatch actions since next wasn't called
// Also note that a value was returned in both cases
// If middleware afterward returns value but current middleware doesn't return value then dispatch won't see returned value
const middleware => store => next => action => {
  if (action.type === 'SOME_TYPE') {
    return 'SOME CUSTOM TEXT';
  }
  
  return next(action)
}
```

* Can also wrap middleware in higher order function to "instantiate" it with variables / conditions

```javascript
const middleware = initialValue => store => next => action => someProcessingFunction;
const middlewares = applyMiddleware(middleware('Some Initial Value'));
```

* If you call `getState` _before_ calling `next(action)` you get state before action has been passed to reducer
* If you call `getState` _after_ calling `next(action)` you get state after action has been applied to reducer

