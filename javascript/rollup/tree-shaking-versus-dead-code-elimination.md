# [Tree-shaking Versus Dead Code Elimination](https://medium.com/@Rich_Harris/tree-shaking-versus-dead-code-elimination-d3765df85c80)

* Dead code elimination: like baking a cake by smashing whole eggs into it and then trying to remove all the egg shells after the fact (finished product, removing bits you don't want)
* Tree-shaking: given that I want to bake a cake, what should I include (live code inclusion)
* Rollup works on top-level AST nodes (i.e function declarations, the type of thing that it'd want to figure out whether to include or not), rather than a more granular level, so it still might include more code than is needed
* Rollup (as of 12/2015) was not smart enough to remove unused functions from used objects
* Rollup tries to create the maximally efficient bundle so it also *doesn't*
  * Wrap modules in functions
  * Put a module loader at top of function
  * Generate code from intermediate AST
