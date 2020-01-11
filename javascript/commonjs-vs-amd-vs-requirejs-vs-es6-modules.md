# [CommonJS vs. AMD vs. RequireJS vs. ES6 Modules](https://medium.com/computed-comparisons/commonjs-vs-amd-vs-requirejs-vs-es6-modules-2e814b114a0b)

* Revealing module pattern uses IIFE to expose a public API that encapsulates private information / state using closure
  * Downside is that asychronously loading modules is not possible and cannot import modules programmatically (i.e. using a string to identify / import a module)

```javascript
const example = (function() {
  const name = 'Jaebaebae';
  function sayMyName() {
    console.log(name);
  };
  return {
    sayMyName,
  };
})();

example.sayMyName(); // Jaebaebae
```

* CommonJS uses `require` function and `exports` object to interact with module system
  * `require` function takes a string to identify and import a module
  * `exports` object contains any functions that will be exported with the object
  * Modules were initially designed for server development as the files are loaded synchronously
* NodeJS borrows heavily from CommonJS
  * Instead of an `exports` object, NodeJS modules have a `module.exports` object
  * Only supports synchronous module loading
  * Circular dependencies are supported
  * Only one file per module
  * Browsers cannot use modules without transpilation (like via Webpack)
* AMD (Asychronous Module Definition) supports asychronous module loading as it's more suitable for the browser
  * There is a `define` function that takes two arguments
    * First argument is an array of dependent modules
    * Second argument is a callback function that will execute after modules are loaded
    * Modules are loaded in a non-blocking manner in background
  * Supports circular dependencies
  * RequireJS implements AMD API
* ES6 modules uses `import` and `export` statements
  * `import` statement is not dynamic and can't just be used anywhere in a file (which is different than `require` and `define`)
  * `export` statement identifies publicly available elements
  * The staticness associated with `import` and `export` mean that static analyzers can be built to identify tree of dependencies when bundling the file without running any additional code
  * Isn't fully supported in browsers and requires transpiler (Babel) to work in unsupported browsers
