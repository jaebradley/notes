# [How To Fix Nasty Circular Dependency Issues Once And For All In JavaScript & Typescript](https://medium.com/visual-development/how-to-fix-nasty-circular-dependency-issues-once-and-for-all-in-javascript-typescript-a04c987cf0de)

* Example is three files / classes - `AbstractNode`, `Leaf` and `Node`
  * `AbstractNode` uses `Leaf`, and both `Node` and `Leaf` extend `AbstractNode`
  * The module loader starts loading `AbstractNode.js` and encounters the `import` for `Leaf`
  * Module loader starts loading `Leaf.js` file, which requires `AbstractNode`
  * Since `AbstractNode` is already being loaded, it is immediately returned by the module cache
  * However, since the `AbstractNode` module hasn't executed past the first line (i.e. the `import` for `Leaf) the `AbstractNode` class has not been initialized
  * `Leaf` class tries to extend from an `undefined` value rather than from a valid class
* Basic fix is to import `Node` and `Leaf` in `AbstractNode` at the end of the file
  * However, this is an ugly work-around and doesn't scale well, especially in a large code base

## Internal module pattern

* Create an `index.js` and an `internal.js` file where the `internal.js` module is used to import and export everything from every local module in the project
* Every _other_ module in the project imports from the `internal.js` file and enver directly imports from other files in the project
* `index.js` file is the main entry point into the project and imports / exports everything from `internal.js` that should be exposed to the outside world
* By importing and exporting everything in `internal.js`, full control over module loading order without moving import statements
* Import statements are smaller, since importing from a single file
