# [A Beginner's Introduction to Webpack](https://medium.freecodecamp.org/a-beginners-introduction-to-webpack-2620415e46b3)

* Default entry point is `./src/index.js`
* Default output is `./dist/main.js`

```javascript
// outputs to ./dist/app.js
{
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'app.js',
  }
}
```

## Loaders

* Webpack allows `import` / `require` of non-JS files via loaders

```javascript
{
  module: {
    rules: [
      {
        test: /\.css$/, use: 'css-loader',
      },
    ],
  },
}
```

* Multiple loaders for each rule - they get executed from last to first

```javascript
// css-loader interprets "import 'some.css'" statements
// style-loader injects the CSS in the DOM using style tags

{
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'css-loader',
          'style-loader',
        ],
      },
    ],
  },
}
```

* Use `file-loader` to import asset types (images, CSV files, etc.)
* `url-loader` converts any PNG file smaller than 8kb as data URL

* Default `mode` is `production`
  * `development` mode
    * builds quickly
    * does not remove comments
    * more detailed error messages
    * better debugging experience
* Source maps are mandatory to get reference to original file that raised error (this is because Webpack bundles code)
  * `devtool` property
