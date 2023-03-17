# [Parse, don't validate](https://lexi-lambda.github.io/blog/2019/11/05/parse-don-t-validate/?utm_source=pocket_saves)

## The essence of type-driven design

* Author uses example of a non-empty list type, which is modeled with a non-empty / present head value, and a list of remaining values (possibly empty)
  * There is a compiler guarantee that there's at least one value in this data type
* Typically, validating a type returns no information whereas parsing would return a value, if possible

## The power of parsing

* Parsers are functions that consume less-structured inputs and produce more-structured outputs
  * Parsers are partial functions - some values in the domain do not correspond to any value in the range, so all parsers must have some notion of failure
  * Parsers let users do checks on input up-front, right on the boundary between a program and the outside world, and once the checks are performed, they never need to be checked again

## The danger of validation

* Shotgun parsing is a programming antipattern whereby parsing and input-validating code is mixed with and spread across processing code—throwing a cloud of checks at the input
* A program that does not parse all of its input up front runs the risk of acting upon a valid portion of the input, discovering a different portion is invalid, and suddnely needing to roll back whatever modifications it already executed

## Parsing, not validating, in practice

* Example is a function that accepts a list of tuples (representing key/value pairs) and create a function that asserts there are not duplicates in the list
  * Validation function is fragile (easy to forget) since type checker would still succeed if the function was not called
  * Better solution is to choose a data structure that disallows duplicate keys by construction, like a `Map`
* Use data structures that make illegal states unrepresentable
  * Model your data using the most precise data structure you reasonably can
* Push the burden of proof upward as far as possible, but no further
  * Ideally, this should happen as quickly as possible, at the boundary of your system, before any of the data is acted upon
* Write functions on the data representation you wish you had, not the data representation you are given
* Treat void functions with deep suspicion - if the primary purpose of the effect is raising an error, it's likely there's a better way

