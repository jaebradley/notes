# Item 12: Understand The Different Flavors Of Equality

* `equal?` checks to see if two objects are exactly the same - i.e. if they have the same `object_id` / pointing to the same chunk in memory
  * `equal?` should not be overridden - it should be the same for all objects
  * Probably want to override `==` to compare objects
    * Agreed upon behavior should be that it returns `true` if two objects represent the same value
    * Default implementation of `==` is `equal?`
* When overriding `==` need to override `hash` method
  * `hash` method is used by the `Hash` class to decide the key for a given object
  * When two objects have a `hash` method collision, this is resolved by comparing the objects with `eql?`
  * Objects that have the same `hash` value and result in `true` when compared with `eql?` are considered the same key

```ruby
class Color
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def hash
    name.hash
  end

  def eql?(other)
    name.eql?(other.name)
  end
end

# using two Color instances with the same name in a hash will resolve to the last Color instance that was added to the hash
# since the two instances will be considered equivalent
```

* `case equality operator` (`===`) is used in `case` expressions
  * `case x when 'blah'` => `if 'blah' === x'`
  * Note how left operand (`blah`) becomes receiver of message and right operand is sole argument to method call
  * Operators are not necessarily commutative because behavior is decided by left operand
  * Knowing what operand becomes the "left" operand when using the `===` operator (which becomes the receiver of the `===` operator) tells you which implementation of the operator you're dealing with
  * Default `===` delegates to `==`
    * The `Regexp` class defines `===` if it's argument is a string that matches the receiver / regex expression

```ruby
# true since receiver of === is Regexp class
/er/ === `Tyler`

# false since receiver of === is String class
'Tyler' === /er/
```

* Classes and modules have a common implementation of `===` that returns `true` if right operand is instance of left operand (reversed version of `is_a?`)
  * `[1, 2, 3].is_a?(Array)` is equivalent to `Array === [1, 2, 3]`