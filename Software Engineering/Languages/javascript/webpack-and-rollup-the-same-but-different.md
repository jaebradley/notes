# [Webpack and Rollup: the same but different](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c)

* Webpack had two game-changing features
  * Code-splitting, which broke app into smaller chunks that could be loaded on-demand leading to a better user experience since users didn't have to wait for entire application to download
  * Static assets like images and CSS can be imported into application
* Rollup was created to build JavaScript libraries as efficient as possible, in particular, capitalizing on the design of ES2015 modules
* Webpack works by wrapping each module in a function, putting them in a bundle with a browser-compatible implementation of `require` and evaluating them synchronously
  * Great for on-demand loading, but it's a bit wasteful and gets worse when there are a lot of modules
* Use webpack for apps and rollup for libraries
* Before ES2015 modules, users and library authors had to agree on what module system they would use
  * If you needed CommonJS and they used AMD, some type of conversion would need to happen
  * The Universal Module Definition provided a common interface, but it wasn't really enforced anywhere
  * Though `import` and `export` are built into ES2015, most browsers still don't support `import` and `export` (and at time of writing, neither did Node) so still need to ship UMD
  * By taking advantage of `module` property in `package.json` file that points to ES2015 module distribution, it's possible to serve UMD and ES2015
    * Both webpack and rollup can use `pkg.module` to generate most efficient code possible via tree-shaking
