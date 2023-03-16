# [Understanding The Template Method Design Pattern](https://blog.bitsrc.io/design-patterns-series-the-template-method-f1e49315ff6d)

* Invariant steps are implemented in abstract base class
  * Abstract base class consists of abstract method where the subclasses can implement their behavior and a method which defines the sequence of how the invariant steps will be called

```javascript
class BaseTemplateClass {
  firstStep() {
    // blahblahblah
  }

  secondStep() {
    // more blahblahblah
  }

  thirdStep() {
    // more more blahblahblah
  }

  algorithm() {
    firstStep();
    secondStep();
    thirdStep();
  }
}
```

* Analogy is recipe for producing shawarma
  * The "algorithm" for making shawarma is `bake bread`, `add vegetable fillings`, `add chilli pepper`, `add meat`, `roll and wrap`
* So for chicken shawarma, override `add meat` method and extend the `ShawarmaRecipe` class

```javascript
class ChickenShawarmaRecipe extends ShawarmaRecipe {
  meat() {
    // do something with chicken
  }
}
```

* The concrete classes shouldn't modify the `runAlgorithm` method - just the implementation of all the steps included in the `runAlgorithm`