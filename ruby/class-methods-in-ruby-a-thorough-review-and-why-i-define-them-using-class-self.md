# [Class Methods in Ruby: A Thorough Review & Why I Define Them Using class << self](https://medium.com/rubyinside/class-methods-in-ruby-a-thorough-review-and-why-i-define-them-using-class-self-af677ede9596)

* Given an array, if an `average` method was necessary, can add behavior to array by doing something like `def array_instance.average`
  * Obviously, another array instance would not have `average` method - Ruby stored `average` method on a class instance (the "singleton" class) that _only_ `array_instance` pointed to
  * Every instance has own singleton class, which is where singleton methods are stored
  * When a method is called on an object, singleton class is first place Ruby looks for method (then it looks at the regular class and ancestor chain)
* Ruby classes are objects, so class methods are methods defined on a specific instance of `Class`
* Author makes argument that `class << self` is more explicit that methods are being defined in singleton class scope
  * By using the `def self.method` approach, the scope is still in regular class but just taking advantage of Ruby's ability to define methods on specific instances from anywhere
    * `self` pointing to `Class` instance where the method is defined
