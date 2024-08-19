# Chapter 4. Constraining Types

* Developers should physically not be able to create types that are contradictory / invalid in your system

## Exceptions

* Use them for exceptional use cases that you don't expect to happen, but still want to guard against, like the network going down
* Don't use exceptions for normal behavior, like not finding an element when searching through a list
* Return type can be enforced but exceptions cannot

## `Optional`

* Intent is communicated more clearly as a developer should expect `None` as a possibility
* Further distinguish between the absence of a value from an empty value
  * When a function returns an empty list, was it a valid result with no elements or was there an error that occurred?

## `Union`

* Author's example is a `Snack` class made up of a `name`, `condiments`, `error_codes`, and whether the `Snack` was `disposed_of`
* Given these properties, the `disposed_of` value should only be set to `True` if there are a non-zero amount of `error_codes`
* There is the case where an illegal state is now representable (i.e. the `disposed_of` value is set to `True` when error codes don't exist)
* Make this state unrepresentable by creating two classes - a `Snack` class made up of a `name` and `condiments` and an `Error` class made up of an `error_code` and a `disposed_of` value
  * The return type is changed from the initial `Snack` class to a `Union[Snack, Error]` to represent the "error" state

## `Literal Types`

* `Literal` types allow you to restrict the variable to a very specific set of values
* `error_code: Literal[1, 2, 3, 4, 5]` means that the typechecker will log errors when an `error_code` of `10` exists
* Introduced in Python `3.8`

## `Final Types`

* `Final` was also introduced in Python `3.8`
* Indicates to the typechecker that a variable cannot be bound to another value
* `Final` is best used when the variable's scope spans a large amount of code, like a module