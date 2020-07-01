# [How To Use Static Type Checking In Python 3.6](https://medium.com/@ageitgey/learn-how-to-use-static-type-checking-in-python-3-6-in-10-minutes-12c86d72677b)

* Can declare a variable type like `some_variable_name: type`
  * `some_string: str = "Some String"
* Function's input / outputs can be declared like `def function_name(arg1: type, arg2: type) -> return_type`
* Running typechecking like `mypy` will automatically error - IDE (like `PyCharm`) will automatically run type checks and identify incompatible types
* Can import from `typing` module like `from typing import Dict, List`
* `Dict[str, int]` is a dictionary where keys are strings and values are ints
* Can combine complex data types like `List[Dict[str, int]]` which is a list that holds dicts that each have a string key and an int value
* Tuples allow a data type for each element - `Tuple[str, int, float]` is valid typing for `("Jaebaebae", 1, 2.3)`
* Can use the `Union` type to declare a function that can accept multiple types - `Any` to accept anything
