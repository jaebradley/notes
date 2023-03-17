# Understanding `npm install`'s Algorithm

The algorithm is outlined in [the `npm install` documentation](https://docs.npmjs.com/cli/install#algorithm)

* load existing `node_modules` tree from disk
* clone the tree
* get the `package.json` and add the data to clone
* walk clone and add any missing dependencies
  * dependencies will be added at the highest level possible without breaking other modules
* compare clone to original and make list of actions (`install`, `update`, `remove`, and `move`) for conversion

## Examples

### Example 1

* `A` depends on `B` and `C`
* `B` depends on `C`
* `C` depends on `D`

The dependency structure will look like

* `A`
  * `B`
  * `C`
  * `D`
* The `C` dependency that `B` has is satisfied by the fact that `A` depends on `C` and installed it at the highest level
* `D` is installed at highest level because nothing conflicts with it

### Example 2

* `A` depends on `B` and `C`
* `B` depends on `D@1`
* `C` depends on `D@2`

The dependency structure will look like

* `A`
  * `B`
  * `C`
    * `D@2`
  * `D@1`
* `npm` processes the dependencies in-order
* So when it sees `B` it will process `B`'s dependency tree before getting to `C`'s
* Since `B` depends on `D@1`, it installs `D@1` at the highest level
* Since `C` depends on `D@2` and installing `D@2` at the highest level would conflict with `D@1`, it gets installed within `C`

**`npm install` is deterministic but different trees may be produced in dependencies are requested for installation in different order**
