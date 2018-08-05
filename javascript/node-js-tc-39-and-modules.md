# [`Node.js`, `TC-39`, and Modules](https://hackernoon.com/node-js-tc-39-and-modules-a1118aecf95e)

## What happens when `require` is called

* `Node.js` has had a module system that is derived from `CommonJS`
* Symbols exported by one JavaScript file (i.e. functions, variables) are made available for use in another JavaScript file via the `require` function
* Here's what happens when `require("foo")` is called
  * Resolve `"foo"` into an absolute file path that points to some artifact that `Node.js` understands
    * This is done by walking the local file system for any native module, JavaScript file, or JSON document that happens to match
  * Loading `"foo"` is determined by what `"foo"` is
    * If `"foo"` is a native module, then "loading" involves dynamically linking the referenced shared library into the current Node.js process
    * If `"foo"` is a JSON file or a JavaScript file, the contents of the file are read into memory after the file is verified to exist
    * `Loading` != `Evaluating` as all "loading" does is pull the string contents of the file into memory, while "evaluating" passes the string contents off to the JavaScript VM for parsing and evaluation
  * If loaded artifact is a JavaScript file, then `Node.js` currently assumes file is a `CommonJS` module
  * **Before passing the loaded JavaScript string off to the JavaScript VM for evaluation, the string is wrapped inside a function**

```javascript
// foo.js
const m = 1;
module.exports.m = m;

// Node.js evaluates as function like
function (exports, require, module, __filename, __dirname) {
  const m = 1;
  module.exports.m = m;
}
```

* `Node.js` uses JavaScript runtime to evaluate this function
* The various "globals" like `exports`, `module`, `__filename`, and `__dirname` are not globals in the traditional JavaScript sense but are function parameters, whose values are provided to the wrapped function by Node.js when the function is called
* `exports` is a regular JavaScript object
* Wrapper function attaches functions and properties to export object
* When wrapper function returns, `exports` object is cached and then returned
* **No way of determining in advance what symbols are going to be exported by the `CommonJS` module until after the wrapper function is evaluated**
  * Critical difference between `CommonJS` and `ES` module because exports of `ESM` are defined when code is being parsed and before it is actually evaluated

## What happens when `ESM` is evaluated?

```javascript
export const m = 1
```

* When code is parsed, but before it's evaluated, a `ModuleRecord` is created
* `ModuleRecord` contains a static listing of the symbols exported by module (parser looks for `export` keyword)
* These symbols are essentially pointers to things that do not exist yet
* Only after this `ModuleRecord` is constructed is the module code actually evaluated

```javascript
// Usage of ESM module
// I'm going to use the "m" symbol exported by the module "foo"

import { m } from "foo";
```

* According to `ESM` spec, link between importing script and `"foo"` must be validated before any code is evaluated
* What happens when `"foo"` is not an `ESM` but a a `CommonJS` module with dynamically defined set of exports?

## Importing a `CommonJS` module

* When `"foo"` is not an `ESM`, the process would put `"foo"` and the importing script into an intermediate pending state, deferring validation of the imported symbols until the dynamic module's code can be evaluated
* Once evaluated, the `ModuleRecord` for the `CommonJS` module can be completed and the imported links validated

### Example 1

* `MyApp` -> `A` (`ESM`) -> `B` (`CommonJS`)

```javascript
// myapp.js
const foo = require('A').default
foo();

// A.js
import { log } from "B";
export default function() {
  log('hello world');
}

// B.js
module.exports.log = function(msg) {
  console.log(msg);
}
```

* `node myapp.js`
  * `require('A')` will load `A` using `ESM` specification (instead of "wrapper function")
  * When `A` is parsed, and `ModuleRecord` is created, `B` would be identified as a non-`ESM`
    * Validation step that verifies that `log` is exported by `B` would be pending
  * `ESM` loader begins evaluation phase
  * Evaluates `B` using existing `CommonJS` wrapper. The result would be passed back to `ESM` laoder to complete construction of `A`'s `ModuleRecord`
  * Evaluates `A`
* If `A` and `B` were switched (i.e. `A` is `CommonJS` and `B` is `ESM`)?
  * Nothing special happens because `ESM`s can be `require`d

### Example 2 (Dependency Cycle)

```javascript
// A.js
const b = require('B');
exports.b = b.foo();
exports.a = 1;

// B.js
import { a } from "A";
export const foo () => a;
```

* Basically impossible to parse this dependency cycle because when `ESM` `B` is linked and evaluated, the symbol `"a"` has not yet been defined and exported by the `CommonJS` module `"A"`
  * Would probably have to be treated as a reference error

### Example 3 (Another Dependency Cycle)

```javascript
// B.js
import A from "A";
export foo() => A.a;
```

* Able to parse circular dependency because when `CommonJS` module is imported using `import` statement, the `module.exports` object becomes the `default` export
  * `ESM` code is linking to `default` export of the `CommonJS` module rather than to the `a` symbol
  * named imports from a `CommonJS` module would work only if there is not a dependency cycle between the `ESM` and `CommonJS` module

## `CommonJS` Module Mutation

* Any mutation to `CommonJS` exports after initial evaluation will not be available as a named import
* `A` (`ESM`) depends on `B` (`CommonJS`)

```javascript
// B.js

module.exports.foo = function(name, key) {
  module.exports[name] = key;
}
```

* When `B` is imported by `A`, the only exported symbols as named imports are the `default` symbol and `"foo"`
* None of the symbols added to `module.exports` when calling `foo` function would be available as named imports
* Would be available via `default` export

```javascript
// A.js
import { foo } from "B";
import B from "B";

foo("abc", 123);

// this should be fine
if (B.abc === 123) { /** ... */ }
```

## Loading for `ESM` and `CommonJS`

* Possible to load `ESM` using `require`
* Possible to load `CommonJS` module using `import`
* Impossible to use `import` inside `CommonJS` module
* Impossible to use `require` inside `ESM`

## `ESM` Detection Proposals

* `Node.js` implementation of `require` has relied on file extensions to differentiate how to load different types of things
* `*.node` files are loaded as native modules, `*.json` files are passed through `JSON.parse`, and `*.js` files are handled as `CommonJS` modules
* Various proposals for detecting `ESM`
  * "Unambigious grammar" - code is either an `ESM` or not, when parsed
  * Adding metadata to `package.json`
  * New file extension (`*.mjs`)
