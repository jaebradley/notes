# [The 100 Correct Way To Split Your Chunks With Webpack](https://hackernoon.com/the-100-correct-way-to-split-your-chunks-with-webpack-f8a9df5b7758)

* When app is bundled into a single file, changing one line of code will mean that the user must download the entire file again
  * If bundle was split across two files, only one of the two files would need to be downloaded again
  * The other file would be served from cache
* Since bundle splitting is about caching, it makes no impact on first time visitors

## Dependency packages

* Don't forget about setting a `filename` format (`[name].[contenthash].js`)
* `optimization.splitChunks.chunks = 'all'` - put everything in `node_modules` in a file called `vendors~main`
  * `vendors` file still suffers from original single bundled file in that changing one dependency will change entire file (and thus cause the entire file to be downloaded)
  * Author suggests creating separate file for each npm package

```javascript
splitChunks: {
  chunks: 'all',
  maxInitialRequests: Infinity,
  minSize: 0,
  cacheGroups: {
    vendor: {
      test: /[\\/]node_modules[\\/]/,
      name(module) {
        // get the name. E.g. node_modules/packageName/not/this/part.js
        // or node_modules/packageName
        const packageName = module.context.match(/[\\/]node_modules[\\/](.*?)([\\/]|$)/)[1];

        // npm package names are URL-safe, but some servers don't like @ symbols
        return `npm.${packageName.replace('@', '')}`;
      },
    },
  },
}
```

* `cacheGroups` - rules for grouping chunks into output files
  * Here `vendor` is used to group `node_modules`
  * By defining `name` as a function and having the name map to the name of the package, there's now one file per package
* `webpack` will automatically create files for things shared between two points in the `entry` definitions

## Downsides of Small Chunks

* Yes, there is webpack boilerplate and overhead
* Yes, lose out on some compression by having small files
* The added overall overhead (2%) seemed to be balanced out by savings on future page loads
* `HTTP/1.1` could see some problems but not the case with `HTTP/2` unless several hundred concurrent file loading

## Code splitting

* 20-20 rule - 20%+ of users go to and bigger than 20% of site's javascript, then code split
* If completely unique code is 7KB and rest of site is 300KB maybe not worth code splitting
  * No difference between 300KB load time and 307KB load time
* Could code-split polyfills

```javascript
if (
  `fetch` in window
) {
  render();
} else {
  import('./polyfills').then(render);
}
```

* `import` uses `Promise`s so will need `dynamic-import-plugin`
* Will also need to polyfill `Promise`s separately from other polyfills
