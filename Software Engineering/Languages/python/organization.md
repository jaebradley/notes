# Organization

## [What is the optimal structure for a Python project?](https://www.reddit.com/r/Python/comments/18qkivr/what_is_the_optimal_structure_for_a_python_project/)
* Think of module hierarchy when structuring code
* Which module is high-level (GUI) and which is low-level (a module built upon a standard library)
* Import a module with a lower level of abstraction (i.e. lower complexity) in the module with higher complexity
  * GUI module imports storage access module and not the other way around

```
 - `vehicle`
- - `vehicle.__init__.py`
- - `vehicle.interface.py`
- - `vehicle.motorcycle`
- - - `vehicle.motorcycle.interface.py`
- - - `vehicle.motorcycle.scooter.py`
- - - `vehicle.motorcycle.cross.py`
- - `vehicle.car.py` 
```

## [Possibilities for Python classes organized across files?](https://stackoverflow.com/questions/106896/possibilities-for-python-classes-organized-across-files)
* A module is a distinct grouping that has one or two dozen closely-related classes
* Rule of thumb is that a module is a unit of reuse
* Can't always easily reuse a single class - should be able to reuse a module without any difficulties
* Everything in your library is either a module or a package of modules

## [](https://docs.python-guide.org/writing/structure/)
* Signs of a poorly structured project include
  * Multiple and messy circular dependencies
  * If each and every change in X's implementation breaks 20 tests in unrelated test cases this means you have too many assumptions about X in Y's code or the reverse
  * Multiple pages of `if` clauses and `for` loops with lots of copy-pasted procedural code and no proper segmenation (spaghetti code)

### Modules
* Don't namespace with underscores - use submodules instead
  * Prefer `library.plugin.foo` to `library.foo_plugin`
* Best is top-level import (i.e. `import modu` vs. `from modu import some_method`)
