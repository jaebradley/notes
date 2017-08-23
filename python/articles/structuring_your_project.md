# [Structuring Your Project](http://docs.python-guide.org/en/latest/writing/structure/)

## Basic Project Structure
Things you should have ([example repository](https://github.com/kennethreitz/samplemod))
- [ ] `README`
- [ ] `LICENSE`
- [ ] `setup.py`
- [ ] `requirements.txt`
- [ ] `sample_module`
- [ ] `docs` (package reference documentation)
- [ ] `tests`
- [ ] `Makefile`

* Your library does not belong in an ambiguous `src` or `python` subdirectory
* If a module consists of only a single file, you can place it directly in the
  root of your repository
* Suggests creating a `tests/context.py` file

```python
import os
import sys
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))

import sample
```

* Distributing tests within a module often increases complexity
* `Makefile` is useful for defining generic project tasks

## Signs of Poorly Structured Code

* Multiple / messy circular dependencies
* Hidden coupling (one change breaks 20 tests)
* Heavy use of global state or context
* Spaghetti code
* Ravioli code
  * Hundreds of similar little pieces of logic, without proper structure
  * If you can't remember if you should use a `FurnitureTable`, `AssetTable`,
    or `Table`, you could have ravioli code

## Modules

* Module names should be short, lowercase, and avoid special characters (`.`, `?`)
* Don't use underscores, use submodules.

```python
# OK
import library.plugin.foo

# BAD
import library.foo_plugin
```

* `import modu` will look for `modu.py` in the same directory as the caller
  * If it is not found, the Python interpreter will search for `modu.py` in the "path"
    recursively and raise an `ImportError` exception if it is not found
  * If `modu.py` is found, the interpreter will execute the module in an isolated
    scope
    * Any top-level statement in `modu.py` will be executed including other
      imports.
    * Functions and class definitions are stored in the module's dictionary
    * The module's variables functions and classes will be available to the
      caller through the module's namespace
    * Some languages have an `include file` pattern that the preprocessor uses to
      take all code found in the file and "copy" it into the caller's code
    * In Python, the "included" code is isolated in a module namespace, which means
      that you generally don't have to worry that the included code could have
      unwanted side-effects, like overriding an existing function with the same name
    * Don't do `import *` - it makes code harder to read and makes dependencies
      less compartmentalized

```python
# BAD
from modu import *

# BETTER
from modu import some_function

# BEST
import modu
x = modu.some_function('foo')
```

## Packages

* Any directory with an `__init__.py` file is considered a Python package
* Assume a file `modu.py` in directory `pack` that is imported via `import pack.modu`
  * Looks for `__init__.py` file in `pack` and executes all top-level statements
  * Looks for `pack/modu.py` and executs all of its top-level statements
  * Any variable, function, or class defined in `modu.py` is available in the
    `pack.modu` namespace
  * Do not put too much code in `__init__.py` files
  * Take advantage of this syntax: `import very.deep.module as mod`

## OOP

* In Python, everything is an object (i.e. functions are "first-class" objects)
* Unlike Java, Python does not impose OOP as the main programming paradigm
  * Perfectly reasonable to have a Python project that uses very few class definitions,
    inheritance, etc.
  * Modules provide a natural encapsulation / abstraction layer
* Classes are useful when state needs to be tied to certain functionality
  * However, holding state in instantiated objects can be vulnerable to concurrency
    problems and race conditions
  * Thus, the idea that stateless functions are a better programming paradigm
* Generally use functions and procedures with as few implicit contexts and side-effects as possible
  * A function's implicit context is made up of any global variables or state that
    are accessed
  * Side-effects are changes that the function makes to it's implicit context
* Functions without implicit context or side-effects are called "pure functions"
  * They are deterministic
  * Easier to change a replace if they need to be refactored or optimized
  * Easier to test with unit-tests
  * Easier to manipulate, decorate, and pass around

## Dynamic Typing

* Variables are not a segment of the computer's memory where some value is written,
  they are names pointing to objects
* Thus, variable `a` can be set to a number, then a string, and then to a function
* Avoid using the same variable name for different things

```python
# BAD
a = 1
a = 'some string'
def a():
  print('Print me')

# GOOD
count = 1
message = 'some message'
def print_me():
  print('Print me')
```

* No efficiency gain to reusing names - assignments create new objects

## Mutable / Immutable Types

* Mutable types (like a list or a dictionary) allow in-place modification (`list.append()`)
* Immutable types, like `x = 6` have no way to change state
* Because mutable types are not stable, they cannot be used as dictionary keys
* Strings are immutable
  * This means that using `join` to glue strings together is more efficient than concatenation
  * List comprehension is generally better and faster for creating a list than
    looping with `append()`

```python
# create a concatenated string from 0 to 19 (e.g. "012..1819")
#
# BAD
nums = ""
for n in range(20):
  nums += str(n)   # slow and inefficient
# GOOD
nums = []
for n in range(20):
  nums.append(str(n))
# BETTER
nums = [str(n) for n in range(20)]
# BEST
nums = map(str, range(20))
```
