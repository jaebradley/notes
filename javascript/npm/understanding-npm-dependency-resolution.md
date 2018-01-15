# [Understanding `npm` dependency resolution](https://medium.com/learnwithrahul/understanding-npm-dependency-resolution-84a24180901b)

* Two modules, `ModuleA@1.0`, which depends on `ModuleB@1.0` and `ModuleC@1.0`, which depends on `ModuleB@2.0`
* **The sequence in which modules are installed is important**
* Since `ModuleA@1.0` is first, `npm` will install it, and its dependency (`ModuleB@1.0`) at the top-level of `node_modules`
* Expected Dependency Tree
  * `node_modules`
    * `ModuleA@1.0`
    * `ModuleB@1.0`
    * `ModuleC@1.0`
      * `ModuleB@2.0`
  * Since `ModuleB@2.0` is a different version than the top-level `ModuleB` it's nested within `ModuleC`
* Let's add two more modules
  * `ModuleD@1.0` that depends on `ModuleB@2.0`
  * `ModuleE@1.0` that depends on `ModuleB@1.0`
* Expected Dependency Tree
  * `node_modules`
    * `ModuleA@1.0`
    * `ModuleB@1.0`
    * `ModuleC@1.0`
      * `ModuleB@2.0`
    * `ModuleD@1.0`
      * `ModuleB@2.0`
    * `ModuleE@1.0`
  * Since `ModuleE@1.0` depends on the same version of `ModuleB` that exists at the top-level of `node_modules` it shares that `ModuleB` dependency
* Let's say that `ModuleA@2.0` depends on `ModuleB@2.0` and we upgrade `ModuleA` to the newest version
  * `npm` will remove `ModuleA@1.0`
  * Installs `ModuleA@2.0`
  * Leaves `ModuleB@1.0` since `ModuleE@1.0` still depends on it
  * Installs `ModuleB@2.0` as a nested dependecy of `ModuleA@2.0`
* Let's say that `Module@2.0` depends on `ModuleB@2.0` and we upgrade `ModuleE` to the newest version
  * `npm` will remove `ModuleE@1.0`
  * Installs `ModuleE@2.0`
  * Removes `ModuleB@1.0` since nothing depends on it
  * Installs `ModuleB@2.0` at the top-level, since `ModuleE@2.0` now depends on it
* Expected Dependency Tree
  * `node_modules`
    * `ModuleA@2.0`
      * `ModuleB@2.0`
    * `ModuleB@2.0`
    * `ModuleC@1.0`
      * `ModuleB@2.0`
    * `ModuleD@1.0`
      * `ModuleB@2.0`
    * `ModuleE@2.0`
  * Note how almost every single module has `ModuleB@2.0` as a nested dependency even though it exists at the top-level
* Can run `npm dedupe` to remove all the nested `ModuleB@2.0` dependencies
* Expected Dependency Tree
  * `node_modules`
    * `ModuleA@2.0`
    * `ModuleB@2.0`
    * `ModuleC@1.0`
    * `ModuleD@1.0`
    * `ModuleE@2.0`
* Install order for `package.json` is **always alphabetical**
