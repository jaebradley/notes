# [Python Names and Values](https://nedbatchelder.com/text/names1.html)

```python
x = 23
y = x
```

* `x` and `y` both refer to the same value
* Neither `x` nor `y` have "increased status" - each refers to the value in exactly the same way
* Reassigning `x` to `12` won't reassign `y` to `12` - `y` will still be associated with the value `23`
  * There are no "permanent" aliases
* Assigning a value never copies data and it never makes a new value
* Assignment makes the name on the left refer to the value on the right
* If a mutable value (like a list) has more than one name, and the mutable value changes (like appending an item to the list), then all names see the change
* Numbers, strings, and tuples are all immutable in python
* `x = x + 1` is rebinding
  * `x + 1` creates a new object
  * Assignemnt statement assigns the name `x` to the new object

## `__iadd__`

* `x += y` and `x = x.__iadd__(y)` are the same
  * `+=` depends on the type of `x` because the type of `x` provides the implementation of `__iadd__`
* Under the hood, lists implement `__iadd__` by calling `extend` (and then returning `self`)
  1. `nums.extend(new value)` (mutating operation)
  1. `nums = nums` (rebinding that is a no-op)

## References

* In a way, a specific list element (`my_list[0]`) is a reference to a value
* Object attributes, dict values and dict keys are all references


## Assignment

```python
X = ...
for X in ...
def X(,,,):
class X(,,,):
import X
```

* All examples of assignments - the all make the name `X` refer to a value
* When for-looping, each loop assigns the element in the sequence to the variable name (like `x`)
  * Doing something like `x = x * 10` inside the `for` loop to try and mutate the input list such that every element is multiplied by `10` does not work
  * `x` is another name for `input list[0]`
  * When you have two names referring to one value, and you reassign one of the names to another value, the other name doesn't change
  * So while `x` may have changed to be multiplied by `10`, `nums[0]` still refers to the current value

### Function arguments are assignments

* When a function is called with a parameter it's as if something like `function parameter = passed in value` had occurred
* Within the function, the passed in value has two names
  * The name within the calling stack frame (i.e. the parent stack frame)
  * The name within the current function's stack frame (i.e. the parameter name, like `x`)

#### `append_twice` function

```python
def append_twice(a_list, val):
  a_list.append(val)
  a_list.append(val)

nums = [1, 2, 3]
append_twice(nums, 7)
print(nums)
# [1, 2, 3, 7, 7]
```

* `nums` is assigned to the parameter name `a_list`
  * Two "names" for the list - `nums` in the calling frame, and `a_list` in the current stack frame

```python
def append_twice_bad(a_list, val):
  a_list = a_list + [val, val]
  return

nums = [1, 2, 3]
append_twice_bad(nums, 7)
print(nums)
# [1, 2, 3]
```

* As in `append_twice`, both `nums` and `a_list` initially refer to the same list
* But then, an entirely new list is created by concatenating the `a_list` value and `[val, val]`. This new list is then assigned to the name `a_list`
* When the function exits, the stack frame is destroyed, and the name `a_list` is destroyed
* The new list referred to by `a_list` was not referred to by any other values, so `a_list` is also reclaiemd
* In the calling stack, there is no effect because the original list was never modified

```python
def append_twice_good(a_list, val):
  a_list = a_list + [val, val]
  return a_list

nums = [1, 2, 3]
nums = append_twice_good(nums, 7)
print(nums)
# [1, 2, 3, 7, 7]
```

* In this case, since the list is returned, the list is now the value for the name `nums`

## Scopes

* Values have no scope
* When a function has a local variable, that means that the variable names is scoped to the specific function
  * When the function returns, the name is destroyed
* If the name's value has other references, those values will "live" beyond the function call
* So the name is local, but not the value
