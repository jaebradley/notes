# [Lessons Learned From a Year of Fighting with Webpack and Babel](https://levelup.gitconnected.com/lessons-learned-from-a-year-of-fighting-with-webpack-and-babel-ce3b4b634c46)

* Trying to complie large libraries can increase Webpack compilation tremendously
  * Use CDN version of package (webpack libraries to use CDN version of package in production build)
* Importing large packages can add bloat to build - import only what is needed / use webpack plugin to only import necessary logic (webpack plugin that removes bloat from moment, for example)
* Add `es-check` to continuous integration pipeline to ensure that bundle only serves ES5
  * Transpile libraries to ES5 (if they aren't compatible) by adding library to babel portion of webpack config
* Use Browserlist to specify what browsers your application should be transpiled for
* Use `babel.config.js` over `.babelrc` as `.babelrc`s can be overridden by a `.babelrc` belonging to `node_module` that you are transpiling
* Make `webpack-dev-server` output friendlier
  * add `noInfo: true` and `stats: minimal` properties to `devServer` config
  * Add tools like `WebpackBar` to get friendlier output
