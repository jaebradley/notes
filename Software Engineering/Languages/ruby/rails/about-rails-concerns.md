# [About Rails concerns](https://medium.com/@carlescliment/about-rails-concerns-a6b2f1776d7d)

* Concerns is a way to include a module in a class, providing a mixin
* If concerns are used to reduce size of class then this is "refactoring" is purely cosmetic
* Using concerns mask explicitness
  * This is more pronounced when there are many many concerns in a single class
  * Execution flow is hard to follow, especially if naming is not particularly explicit 
  * If only one class implements a concern, then it's reusability may be limited
* Opportunities for bidirectional dependencies
  * Often times a concern sets instance variables as part of it's internal logic
  * This is problematic because the concern now knows about how it's going to be used
  * Future consumers need to remember to declare / use the instance variables
* Good concerns should be able to work in isolation so need to be as dependency-agnostic as possible
  * Concrete and limited responsibility
  * Main responsibilities should be framework or infrastructure-related - should not contain business logic
  * Business logic should be modeled in classes / value object / services
* Concerns are a little harder to test
* Concerns promote the _is a_ relation between classes / behavior
  * This means that an object inherits behavior directly so more and more responsibilities are aggregated to the object as long as concerns are added
  * Segregation of responsibilities come with _has a_ behavior via composition
