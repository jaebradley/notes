# [Dynamic Import ANd Tree Shaking in JavaScript](https://tech.olx.com/webpack-from-zero-to-hero-f64924e4d06)

* Imagine a file with a bunch of named exports
* They are used in another file (`main.ts`) though only one of the methods is `import`ed
* Setting the `usedExports` property to `true` in the `optimization` field in the webpack configuration will turn on tree-shaking
* In the generated bundle file, the output from the file with a bunch of named exports generates the source for each named export as well as `unused harmoney export...` messages that indicate which `export`s have not been used
* If another one of the named exports are imported but not used, the bundle will be the same which means that webpack understands which methods are used and puts them in some kind've "used and exported" list
* Same result from `import *` and then only using that single named export
* A dynamic import is the `import` function that returns a `Promise` that can be chained
  * The result of the `then` is the module for that file that contains every export
  * When bundled, the result shows that every named export is included in the export list (`harmony export (binding)`)
* The reason that the previous output bundles showed the unused functions is that the mode was _not_ `production`
* When `production` `mode` is used only the used exports are defined
* However, with the dynamic imports, all named exports are defined whether they are used or not
  * This is because from webpacks' perspective it doesn't know if a file would be imported eventually or not (in the case where the `import('./someFile')` is wrapped in a conditional like `if (isOdd(num))`)
