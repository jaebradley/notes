# [Loaders](https://webpack.js.org/concepts/loaders/)

* Loaders are transformations applied to source code of module
* Loaders are evaluated from right to left

```javascript
module: {
  rules: [
    {
      loader: 'this will run third',
    },
    {
      loader: 'this will run second',
    },
    {
      loader: 'this will run first',
    },
  ],
}
```

* Can inline loaders using `!` so `style-loader!css-loader`, which will run loaders one at a time
* Can pass options to loaders using query parameters like `?key=value`
  * Can also be specified via an `options` object

* **Additional Resources**
  * [Module Rules](https://webpack.js.org/configuration/module/#module-rules)

## [How does `url-loader` work](https://stackoverflow.com/a/49080266/10039741)

* `url-loader` encodes files to base64 and includes them inline in `bundle.js`
  * Something like `data:;base64,aW1wb3J0IFJlYWN0IGZ...` would be added to bundle
* Having many small files would recall multiple requests to web server to fetch these files
* Instead, could add these files to bundle (which has to be downloaded anyways) to avoid roundtrip times
* Larger files (online it says anything > 8kb) get "emitted" to a directory that web server will serve statically (what `file-loader` does)

* **Additional Resources**
  * [`url-loader` docs](https://webpack.js.org/loaders/url-loader/)

## [`file-loader` example with Font Awesome]

* Example rule that outputs different files to a fonts directory in the output directory (`dist`, for example)
* Note how the `name` property is the filename + extension

```javascript
{
  test: /.(ttf|otf|eot|svg|woff(2)?)(\?[a-z0-9]+)?$/,
  use: [{
    loader: 'file-loader',
    options: {
      name: '[name].[ext]',
      outputPath: 'fonts/',    // where the fonts will go
      publicPath: '../'       // override the default path
    }
  }]
},
```

```text
dist/
  css/
      main.css
  fonts/
      fontawesome.webfont.eot
      fontawesome.webfont.svg
      fontawesome.webfont.ttf
      fontawesome.webfont.woff2
      fontawesome.webfont.woff
```
