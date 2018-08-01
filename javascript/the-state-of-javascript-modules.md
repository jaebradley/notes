# [The State of JavaScript Modules](https://medium.com/webpack/the-state-of-javascript-modules-4636d1774358)

* `ES2015` introduced two different modes
  * `script` for regular scripts with a global namespace
    * If you try to `import` or `export` inside a `script`, it will raise with a `SyntaxError` because these statements don't make sense in a global context
  * `module` for modular code with explicit imports and exports
    * Implies `strict` mode which forbids certain language features, like the `with` statement
  * Important to defined mode *before* script is parsed and executed
* `<script type="module" src="main.js"></script>` to specify `module` mode in browser

```javascript
// Supported:
import {foo} from 'https://jakearchibald.com/utils/bar.js';
import {foo} from '/utils/bar.js';
import {foo} from './bar.js';
import {foo} from '../bar.js';

// Not supported:
import {foo} from 'bar.js';
import {foo} from 'utils/bar.js';
```

* Build tools like `webpack` try to parse code using `module` mode with `script` mode fallback
  * Output is a `script` and a module runtime, which simulates behavior of `CJS` and `ESM`
* `webpack` uses function wrappers to encapsulate module scope and object references
  * Once per compilation it also includes a module runtime which is responsible for bootstrapping and caching modules
  * Translates module specifiers into numeric module ids
  *  `webpack` replaces `export` statements with `Object.defineProperty` on the `exports` object
  * Replaces all references to imported valeus with property accessors
  * `"use strict"` is used at the beginning of very ESM
  * Implementation is a simulation because it *mimics* behavior of ESM and CJS, but does not *replicate* it

```javascript
// a.js
export let number = 42;
export function incr() {
    number++;
}

// test.js
import { number } from "./a";

console.log(number);
```

```javascript
// webpack output

(function(modules) {
    // This is the module runtime.
    // It's only included once per compilation.
    // Other chunks share the same runtime.
    var installedModules = {};
    // The require function
    function __webpack_require__(moduleId) {
        ...
    }
    ...
    // Load entry module and return exports
    return __webpack_require__(__webpack_require__.s = 1);
})
([ // An array that maps module ids to functions
    // a.js as module id 0
    function (module, __webpack_exports__, __webpack_require__) {
        "use strict";
        Object.defineProperty(__webpack_exports__, "a", {
            configurable: false,
            enumerable: true,
            get: () => number
        });

        let number = 42;

        function incr() {
            number++;
        }
    },
    // test.js as module id 1
    function (module, __webpack_exports__, __webpack_require__) {
        "use strict";
        var __WEBPACK_IMPORTED_MODULE_0__a__ = __webpack_require__(0);

        // Object reference as "live binding"
        console.log(__WEBPACK_IMPORTED_MODULE_0__a__["a" /* number */]);
    }
]);
```

* This `webpack` simulation fails in the case of module in form of `console.log(this)`
  * `babel` assumes ESM by default since `module` mode implies strict mode - initializes `this` with undefined
  * `webpack`'s `this` points to exports, which matches behavior of `CJS` in `Node.js`
    * This is because `script` and `module` is ambiguous - parsers cannot tell whether module is `ESM` or `CJS`
    * `webpack` defaults to `CJS` since it's still the most popular module style

```javascript
// babel
"use strict";
console.log(undefined);

// webpack
(function(module, exports) {
  console.log(this);
});
```

* `CJS` vs. `ESM`
  * `CJS`
    * dynamic, synchronous, `require`
    * exports are only known after evaluating module
    * exports can be added, replaced, removed, even after the module has initialized
  * `ESM`
    * static, synchronous, `require`
    * imports and exports are linked before evaluating the module
    * imports and exports are immutable
  * Cyclic dependencies between `ESM` and `CJS` can lead to deadlock situations

* Tradeoffs
  * `ESM` must have the `*.mjs` file extension
    * Impossible to tell what kind of JavaScript code something is just by parsing it
  * `CJS` can only import `ESM` via asynchronous `import()`
    * `Node.js` will load `ESM`s asynchronously to match browser behavior
    * synchronous `require` of an ESM will not be possible
    * every function that depends on an `ESM` needs to be asynchronous
  * `CJS` exposes single immutable default export to `ESM`
    * There are no named exports in `CJS` - just a single export called `default`

* Should I use `ESM`s in my new code?
  * If you're writing a library, yes (tree-shaking)
    * Write `ESM` and use `rollup` or `webpack` to transpile down to a single `CJS` module
    * Point the `main` field inside `package.json` to this `CJS` bundle
    * Use `module` field to point to original `ESM`s
  * If you're writing a `Node.js` app, stick to `CJS`
