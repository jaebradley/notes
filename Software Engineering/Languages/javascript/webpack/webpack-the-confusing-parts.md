# [Webpack - The Confusing Parts](https://medium.com/@rajaraodv/webpack-the-confusing-parts-58712f8fcad9)

* Everything is a module - so CSS files, images, HTML files can all be "modules" and can be reused
* Webpack has various features to split code up and generate multiple bundle files so you load only what you need when you need it
* Webpack, the module bundler, provides two interfaces, the Webpack CLI tool and `webpack-dev-server` which needs to be installed separately
* `webpack-dev-server` is just an `express` `NodeJS` server that internally calls `Webpack`
  * It has capabilities like reloading the browser and replacing a changed module

## Entry

* The `entry` definition tells Webpack the starting point(s) for the application(s)
  * To append multiple files that are _not_ dependent on each other, can use an `Array` definition
    * `entry: ['source.js', 'googleAnalytics.js']` will append the `googleAnalytics.js` file to the end of `bundle.js`
  * When the entry definition is an object, this supports a multi-page application (i.e. `index.html` and `profile.html`)

```javascript
{
  entry: {
    "indexEntry": "index.js",
    "profileEntry": "profile.js"
  },
  output: {
    path: '/dist',
    filename: '[name].js'
  }
}
```

* `profile.html` has a `script` tag that references `dist/profileEntry.js`
* `index.html` has a `script` tag that references `dist/indexEntry.js`

## `path` vs. `publicPath`

* The `output` property tells webpack where / how to store the resulting bundle's files
* `path` tells webpack where to store the result somewhere in the filesystem
* `publicPath` can be used by webpack plugins to specify the URLs inside CSS / HTML files when generating production builds
* Define a certain configuration in production (like the CDN URL scheme for images in production)

## Loaders

* Loaders are external dependencies that help load / import files of various types into browser-acceptable formats
  * `babel-loader` converts `ES6` JavaScript into browser-acceptable `ES5` JavaScript (for example)
  * loaders can be chained - the chaining works from right to left
    * `style!css` would execute the `css-loader` first and then the `style-loader`
    * Webpack searches for CSS file dependencies inside modules (like `require(someFile.css)`)
    * `css-loader` loads the CSS and handle things like `import`ing other CSS and outputs JSON
    * Webpack takes the generated JSON and passes the result to `style-loader`
    * `style-loader` takes the JSON and generates valid `style` tags and insert the generated `style` tag into the `index.html` file

## Plugins

* Plugins are external dependencies that work on the resulting bundle
* Loaders work at the individual file level during or before the bundle is generated whereas plugins work at bundle or chunk level and work at the end of the bundle generation process

## Resolve

* The webpack configuration object has a `resolve` object with an `extensions` property that allows resolving imports without having to specify an explicit file extension
* `extensions: ['.js']` would allow `require('./someJSFile')` to be successfully `require`d without having to specify an explicit file extension
