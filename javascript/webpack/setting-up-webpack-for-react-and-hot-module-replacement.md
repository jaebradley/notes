# [Setting Up Webpack for React and Hot Module Replacement](https://robots.thoughtbot.com/setting-up-webpack-for-react-and-hot-module-replacement)

* Webpack is a build tool that takes application code and generates static assets
  * It also can strip out unused code, support hot module replacement, and provides a dev server for hosting static assets locally

```js
module.exports = {
  // Application lives in app directory
  context: __dirname + "/app",
  // Main entry point is app.js file
  entry: "./app.js",

  output: {
    // Generated static asset should be a file named app.js
    filename: "app.js",
    // Located in the dist directory
    path: __dirname + "/dist",
  },
}
```

## Loaders

* Webpack uses loaders to process different application code (like `ES6` or `JSX`)
  * Loaders don't necessarily need to output vanilla JavaScript, and can be chained together

```js
{
  module: {
    loaders: [
      // Apply babel-loader to all files with a .js* extension
      // That are not in node_modules
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loaders: ["babel-loader"],
      }
    ],
  },
}
```

## Add an index HTML page

* In the index HTML need to point to the generated static files generated by Webpack
  * Include `script` tag where `src` points to generated `app.js` file

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Webpack + React</title>
  </head>
  <body></body>
  <script src="app.js"></script>
</html>
```

* Copy over HTML using another loader (since previous loader only handled JavaScript)
  * Example uses `file-loader` (like this) - (`file-loader` copies files)

```js
{
  entry: {
    javascript: "./app.js",
    // Add index file as html entry point
    html: "./index.html",
  },
  module: {
    loaders: [
      // babel-loader
      {
        test: /\.html$/,
        loader: "file?name=[name].[ext]",
      },
    ]
  }
}
```

## Hot Module Replacement

* The way `react-hot-loader` works is that modifying and saving a component file should replace the module on the page without reloading and losing component state
* Have to add `react-hot-loader` as a JavaScript loader (before `babel-loader`)
  * Application code will be processed by `react-hot-loader` and then the result of that will be passed to `babel-loader`
* `app/app.js` will *not* be reloaded and will cause a full page reload

```js
{
  // Some other shit
  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loaders: ["react-hot", "babel-loader"],
      }
    ]
  }
}
```
