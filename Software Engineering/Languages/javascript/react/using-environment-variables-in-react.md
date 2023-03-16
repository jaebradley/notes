# [Using Environment Variables in React](https://medium.com/@trekinbami/using-environment-variables-in-react-6b0a99d83cf5)

* Environment variables are available via `process.env` object
  * Provided via Node
  * Since Node doesn't exist in browser need to use webpack to specify these environment variables
  * These env variables can be added in `npm` scripts
    * `webpack --env.SOME_KEY=SOME_VALUE`
  * These environment variables serve as placeholders that will be replaced when code is compiled
* Need to let webpack know about these variables using `DefinePlugin` webpack plugin
  * `new webpack.DefinePlugin({ someKey: 'someValue' })`
* Combine with `dotenv`
* Common to have `.env` and `.env.development` files
  * If so, can parse the right file based on what the defined environment is
