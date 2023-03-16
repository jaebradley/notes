# [Live Editing JavaScript with Webpack (Part 3)](https://jlongster.com/Backend-Apps-with-Webpack--Part-III)

* Webpack compiler needs to communicate changes with running app via websocket connection or unix signal
  * Compiler watches for changes, recompiles modules, and then notifies app
* If a module B was updated then modules that B depends on and that depend on B need to be updated
  * For modules that B depends on, B' would require them and use the same instances
  * For modules that depends on B, webpack walks up dependency chain and re-runs each module until it hits a module that "accepts" the changes

```javascript
// all implementation-specific code

if (module.hot) {
  module.hot.accept();
}
```

* An update fails if it was never accepted
* If there is a module A that depends on B, when B' is generated, then A is re-run to generate A' which accepts the change
* Generally, only call `accept` method in root module and all updated modules bubble up to root module
* HMR always evaluates entire module again

## Monkey Hot Loader Details

* If module B updates, only swaps with B'
* How to patch closures without losing state in closure?
* MHL made choice to only support basic patching heuristics like patching top-level functions inside a module
* Need to support exported functions where dependent modules would still reference old version
* On first execution of module, override / patch existing functions by keeping a map of patched functions
  * If a patched / updated function exists for a given function in a module, use that function instead
* Subsequent updates follow different path where module is evaluated again
  * All patched functions are iterated, re-evaluated using module's scope (so it references original state) and then the patched functions are updated to use updated function

```javascript
bindings.forEach(function(binding) {
  // Get the updated function instance
  var f = eval(binding);

  // We need to reify the function in the original module so
  // it references any of the original state. Strip the name
  // and simply eval it.
  var funcCode = (
    '(' + f.toString().replace(/^function \w+\(/, 'function (') + ')'
  );
  patchedBindings[binding] = module.hot.data.moduleEval(funcCode);
});
```
