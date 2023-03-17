# [Understanding Redux Middleware and Writing Custom Ones](https://designingforscale.com/understanding-redux-middleware-and-writing-custom-ones/)

* Redux middleware is similar to middleware in Express - it intercepts dispatched actions before they make it to the reducer
* When a `dispatch` is called on an action, the action could go through many middlewares before making it to the reducer
* Multiple middlwares are executed in the order they're defined
* Middlewares can choose to stop forwarding the action (by not calling `next`)
* Middleware functions have the following signature `store => next => action => result`
  * `store` is the Redux store instance that is passed to middlewares
  * `next` is function that takes action to pass to next middleware or to reducer
  * `action` is action that was originally dispatched and will probably be operated on in some way
  * `result` is the value used as the result of of the original `dispatch` function invocation
