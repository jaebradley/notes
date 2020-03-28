# Item 1: Understand What Ruby Considers To Be True

* Every value is true _except_ `false` and `nil`
* Zero is `true` - unlike many programming languages
* Placing `false` on the left-hand side of `==` means that Ruby parses the expression as a call to `FalseClass#==` method which means that the method will only return `true` if right operand is also `false`
  * Whereas if `false` is right operand, expression may not work as expected since it relies on left operand overriding the `==` method and loosening the comparison

```ruby
class Bad
  def == (other)
    true
  end
end

# This will be false
false == Bad.new

# This will be true
Bad.new == false
```