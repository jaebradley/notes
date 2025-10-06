# [Introducing BDD](https://dannorth.net/blog/introducing-bdd/)

## A simple sentence template keeps test methods focused #
* Author's example: writing a validator for inputs from a screen
  * `ClientDetailsValidatorTest` with test methods like `testShouldFailrForMissingTitle`
* This validator also did age validation
  * Author ended up writing cumbersome test method names, so ended up creating a new class (`AgeCalculator`) with its own test class
  * Now the `ClientDetailsValidatorTest` only needs a single test around age calculation to make sure it interacts properly with the calculator
* If a class is doing more than one thing, it usually is an indication that there should be other classes that do some of the work
* New service is an interface describing _what it does_ and this new service is passed to the class's constructor (aka dependency injection)
