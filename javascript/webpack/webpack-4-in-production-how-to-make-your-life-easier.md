# [Webpack 4 In Production: How To Make Your Life Easier](https://medium.com/@hpux/webpack-4-in-production-how-make-your-life-easier-4d03e2e5b081)

* The created a module to return all entry points
* `resolve` - configures how modules are found
  * So `import "lodash"` looks in certain places for `lodash`
* `context` - base directory, resolves entry points and loaders
* `entry` - point or points to enter application
* `output.path` - output directory as an absolute path`
* `output.filename` - name of each output bundle in the output directory
* `module.rules` - array of rules that modify module creation
  * Apply loaders to modules or modify parsing
  * For example, use `babel-loader` on all non-`node_modules` files to take advantage of `ES6` syntax
* `devtool: 'inline-source-map'` for dev environment
* `minimizer` - production setting that specifies what minimization library to use
  * `UglifyJSPlugin`, for example
* Don't forget `sourceMap: true` in production
* `splitChunks` - can extract common modules into a specific file so that it's downloaded and used once

```javascript
// all modules in node_modules that are duplicated are put into a single file
splitChunks: {
  cacheGroups: {
    default: false,
    commons: {
      test: /[\\/]node_modules[\\/]/,
      name: 'vendor_app',
      chunks: 'all',
      minChunks: 2
    }
  }
}
```
