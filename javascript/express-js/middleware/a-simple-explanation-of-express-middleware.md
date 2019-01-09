# [A Simple Explanation of Express Middleware](https://medium.com/@agoiabeladeyemi/a-simple-explanation-of-express-middleware-c68ea839f498)

* Middlewares can be specified globally across an express app like `app.use(someMiddleware)`
  * Specific path: `app.get('some/path', someMiddleware)`
* Middleware function can take up to three arguments - `request`, `response`, and `next`
  * `next` represents function that calls the next middleware
    * When `next` takes a param, this let's express know that an error occurred
* Error Middleware is a function that four arguments - an additional first `error` argument
  * Middleware gets access to any errors that are thrown by previous middleware (i.e. via when `next(error)` is invoked)
