# Random Stuff (To Be Better Organized Later)

* [Ternary Operator](https://stackoverflow.com/a/394814/5225575)
  * `a if condition else b`
* [Shallow copying vs. Deep copying](https://stackoverflow.com/a/3975388/5225575)
  * `b = a.copy()` means that `b` and `a` point to two different objects, but
    the underlying values in both objects will have the same references
    ![alt-text](https://i.stack.imgur.com/Vtk4m.png)
  * `b = copy.deepcopy(a)` means that `b` and `a` are different objects and their
    underlying values have different references
    ![alt-text](https://i.stack.imgur.com/BO4qO.png)
* `some_string.lower()` to convert to lowercase
* `ord('a')` to get the `int` value of a character
* `chr(some_int_value)` to get the character for an `int` value
* `zip(iterable1, iterable2)`
  * Takes two iterables of equal size (other functions to deal with unequal size
    cases) and combines the first element of the first iterable with the first
    element of the second iterable, and so on, to produce an iterable of tuples
  * `zip([1, 2, 3, 4], [4, 3, 2, 1])` would output `[ (1, 4), (2, 3), (3, 2), (4, 1) ]`
* `lambda` is just a way to create an anonymous function
  * `lambda x: x * x` is an anonymous function that produces the square of an input
* `map(function, iterable)`
  * Takes a function and an iterable, and applies the function to each element
    of the iterable
  * `map(lambda x: x * x, [1, 2, 3])` would apply the square lambda function to
    each element in the list `[1, 2, 3]` outputting a `map` object with the
    elements `[1, 4, 9]`
* `enumerate(iterable)`
  * Takes an iterable and returns another iterable with the index and value of
    the first iterable
  * `enumerate(['a', 'b', 'c'])` returns `[(0, 'a'), (1, 'b'), (2, 'c')]`
* [Generate a relative file path](https://stackoverflow.com/a/32973383/5225575)
  ```python
  import os

  current_directory = os.path.dirname(os.path.realpath('__file__'))

  #For accessing the file in the parent folder of the current folder
  filename = os.path.join(fileDir, '../same.txt')
  ```
* [`JSON`](http://stackabuse.com/reading-and-writing-json-to-a-file-in-python/)
  * `dump` takes a Python object and writes it to the specified file (or file-like) object
  * `dumps` takes a Python object and returns the `JSON` string
  * `load` reads the `JSON` string from a file (or file-like) object and parses it into a Python `dict`
  * `loads` allows you to pass in the `JSON` string itself
* `//` is integer division and `/` is float division
* `**` is power operator
* List comprehension with conditional
  ```python
  ''.join([character.upper() if character.islower() else character.lower() for character in s])
  ```
  * String Validation
    * `isalnum` - checks if all characters are alphanumeric (`a-z`, `A-Z`, `0-9`)
    * `isalpha` - checks if all characters of a string are alphabetical (`a-z`, `A-Z`)
    * `isdigit` - checks if all characters of a string are `0-9`
    * `islower` - checks if all characters are lowercase characters (`a-z`)
    * `isupper` - checks if all characters are uppercase characters (`A-Z`)
  * String Alignment
    * `ljust(width, fill_character)` - returns a left-aligned string of the given width with extra space taken up by the specified fill character
    * `center(width, fill_character)` - like `ljust` but center-aligned
    * `rjust(width, fill_character)` - like `ljust` but right-aligned
  * `textwrap` module
    * `wrap` - converts a string into a list of strings with a max length
    ```python
    import textwrap
    string = "This is a very very very very very long string."
    wrapped_values = textwrap.wrap(string, 8)
    # wrapped_values => ['This is', 'a very', 'very', 'very', 'very', 'very', 'long', 'string.']
    ```
    * `fill` - takes the list returned by `wrap` and returns a string with each element of the list on a new line
