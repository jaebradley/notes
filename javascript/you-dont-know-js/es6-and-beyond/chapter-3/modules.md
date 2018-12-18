# [Modules](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#modules)

* ES6 modules are file-based i.e. one module per file
  * Loading ES6 modules means loading one module at a time
  * Works well with HTTP2 which works on a persistent socket connection and can efficiently load files in parallel
* Module API is static meaning that exports cannot be changed
* Properties and methods on a module's public API are actually bound to identifiers in inner module definitions
  * When these exported values change in the module, the imported value will also change
* ES6 modules are singletons meaning that if you import a module you get a reference to the one shared instance
* Importing a browser means a blocking network request (on the browser) or a blocking file system operation (on the server)
  * Because ES6 modules have static definitions, import requirements can be statistically scanned and loads will happen preemptively
* ES6 modules have one default export - the name of this export is literally `default`

```javascript
var awesome = 42;

export { awesome };

awesome = 100;

// import { awesome } from 'some-module';
// will evaluate to 100
```

* In following example, first default export points to a function expression, while the second default export points to the variable reference
  * This variable reference could change, whereas the function expression would not change

```javascript
function foo() {
  // something
}

// export binding to function expression
// if foo changes in future, it will not be reflected in export
export default foo;

// export binding to variable foo
// If foo changes in future, it will be reflected in export
export { foo as default};
```

* All imported bindings are immutable / read-only

```javascript
import foo, * as hello from 'world';

// All of these will TypeError

foo = 42;
hello.default = 42;
```

* `import`s are hoisted

```javascript
// This will run because static resolution of import statement identified foo during compilation
// import also gets hoisted to top-level of module
foo();

import { foo } from 'bar';
```

* `import 'foo';`
  * Does not import any module bindings
  * Loads (if not already loaded)
  * Compiles (if not already compiled)
  * Evaluates (if not already run)
  * Usually used for side-effect purposes where module modifies `window` / global object

## Circular Dependencies

```javascript
// foo.js
import bar from 'bar';

export default function foo(x) {
  if (x > 10) {
    return bar(x);
  }
  return x * 2;
}

// bar.js
import foo from 'foo';

export default function bar(x) {
  if (x > 5) {
    return x / 2;
  }

  return x + 2;
}
```

* If `foo` is evaluated first, roughly what happens is that it's exports are analyzed and the exported bindings are registered
  * Then the imports, in this case, `bar` are processed
* `bar` is evaluated
  * Again, the exports bindings are analyzed
    * Then the imports are analyzed
    * It knows the API of the `foo` module so the `foo` import is verified
* Now that the `bar` module API is identified, the `bar` import in the `foo` module can be validated
* The end result is if they had been defined and called in the same scope
