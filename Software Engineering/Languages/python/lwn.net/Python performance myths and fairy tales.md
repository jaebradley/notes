# [Python performance myths and fairy tales](https://lwn.net/SubscriberLink/1031707/73cb0cf917307a93/)

## Myth: Python is slow because it is interpreted
  * Author states that interpretation is a small part of what makes Python slow
  * Example is statement `p.x * 2`
  * In C/C++/Rust, this expression results in three operations: load value of `x`, multiply it by `2`, and then store the result
  * In Python, there is a long list of operations that are performed
    * Find the type of `p`
    * Call `__getattribute__()`
    * Unbox `p.x` and `2`
    * Box the resultc
    * These operations are not dependent on whether Python is interpreted or not, but rather the language semantics

## Myth: Python + static types means that Python compilers can skip past all these language semantic steps
```python
def add(x: int, y: int) -> int:
    return x + y

print(add(2, 3))
```

* Static typing is not enforced at run time, so `add` can be called with strings like `add('hello', 'world')`
* Author's point is that static types are useless from the point of view of optimization and performance
* Also, in Python, everything can change (like deleting class methods after they are defined), so static compilation can be problematic

## Dynamic
```python
import numpy as np

N = 10

def calc(v: np.ndarray[float], k: float) -> float:
    return (v * k).sum() + N
```

* While this looks like straight-forward logic, the compiler can't assume anything about the code
* There can be `import` hooks that change the expected behavior
* `N` cannot be assumed to be `10` because `N` could be reassigned somewhere else
* Because `calc` can be redefined, the type declarations on `calc` can change
* However, in most cases, the logic would do exactly what it looks like it should
* The gap between how the logic would normally be written and the language definition is what makes life complicated for the interpreter

## Compiler optimization challenges
```python
import random

class Evil:
    if random.random() > 0.5:
        def hello(self):
          print('hello world')

Evil().hello() # 🤷🏻‍♂️
```

* Half the time the method exists and half the time it doesn't - how would one compile this logic?

```python
def foo():
    p = Person('Alice', 16)
    print(p.name, p.age)
    assert isinstance(p, Person) # <<<

class Person:
    def __new__(cls, name, age):
        if age < 18:
            p = object.__new__(Student)
        else:
            p = object.__new__(Person)
        p.name = name
        p.age = age
        return p
```
* You can have a class with a dunder-new (i.e. `__new__()`), which returns something which is unrelated and is not an instance of the class

## Abstraction
* Author points out how abstracting logic again and again (composing functions, moving a tuple into a `dataclass`, etc) incurs a penalty in Python
* "Python to C" can also be problematic because at some point, values will need to be boxed/unboxed when executing logc in the compiled language and referencing those values in Python

## Memory Management in Python
* Computation is generally cheap, and memory is often times the bottleneck
* RAM accesses vs. cached data is quite slow
* Great performance means avoiding cache misses as much as possible
* Python has memory layouts that aren't cache-friendly

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

p = [Person('Alice', 16), Person('Bob', 21)]
```

* Each `Person` has two fields, which would ideally be colocated in memory
* Similarly, each `Person` element in the `list` would also be co-located
* In practice, these objects are scattered throughout memory
