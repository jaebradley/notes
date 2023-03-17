# Item 13: Implement Comparison Via "<=>" And The Comparable Module

* The `<=>` operator is informally known as the "spaceship" operator
* When it doesn't make sense to compare to the receiver with the argument, than the comparison operator should return `nil`
  * Example is if the argument is an instance of another class or `nil`
  * Some classes, it might be useful to conver the argument, but for most use-cases it's best to return `nil` if the receiver and the argument are not instances of the same class
* If the receiver is less than the argument, return `-1`
  * If comparing the left and right operands with `<` should return `true` then return `-1`
* If the receiver is greater than the argument, return `1`
* If the receiver is equal to the argument, return `0`
  * The `==` operator will only return `true` when `<=>` returns `0`
* If want to use operators like `>=` don't need to implement all by hand, can just include the `Comparable` module
* If you define a `<=>` operator, need to change the implementation of `eql?` which delegates to `equal?` which doesn't make sense
  * Instead, make `eql?` alias for `==`
  * Also need to define a `hash` method
* Here's a `Version` class that represents a major / minor / patch version for say, a software release
  * The instance variables make calculating `<=>` easy as can delegate to `<=>` for `Fixnum`
  * Find the first value in the set that's non-zero, and return it
    * If all values are zero, `detect` will return `nil` and then `0` will be returned instead
  * Notice how the `hash` method delegates to `Array#hash` but including all the instance variables

```ruby
class Version
  attr_reader :major, :minor, :patch
  alias_method :eql?, :==

  def initialize(major, minor, patch)
    @major = major
    @minor = minor
    @path = path
  end

  def <=> (other)
    return nil unless other.is_a?(Version)

    [ 
      major <=> other.major,
      minor <=> other.minor,
      patch <=> other.patch,
    ].detect {|n| n.zero?} || 0
  end

  def hash
    [major, minor, patch].hash
  end
end
```