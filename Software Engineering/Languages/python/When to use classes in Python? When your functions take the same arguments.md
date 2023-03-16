# [When to use classes in Python? When your functions take the same arguments](https://death.andgravity.com/same-arguments)

* If you have functions that take the same set of arguments, consider using a class

## Example: HighlightedString

* To highlight string matches, there is a function that takes a string and a list of slices and adds before/after markers for the strings described by the slices
  * `water on mars` + `slice(9, 13)` => `water on <marker>mars</marker>`
* Helper function that splits string such that highlights have odd indices
  * `split_highlights` function that takes a string value and a list of slices (highlights)
* Validation function that only allows non-overlapping slices with positive start and end indices and no steps
  * `validate_highlights` function that takes a string value and a list of slices (highlights)
* `HighlightedString` class with string value and list of slices that represent highlights as attributes
  * `apply` and `split` methods
  * validation occurs on construction (i.e. in the `__init__`)

```python
string = HighlightedString('water on mars', [slice(9, 13)])
string.apply('<start marker>', '<end marker>') // water on <start marker>mars<end marker>
string.split() // ['water on', 'mars', '']
```

* The data + logic are now bundled together
* Shows / demonstrates / communicates intent - not just a string + slices, it's a highlighted string
* Bundling the logic in a class increases the discoverability of actions via code completion, for example
* Validating the slices in the construction of the class ensures invalid objects cannot exist
* Make the class immutable so that other functions cannot mutate the internal attributes and cause unexpected behavior
