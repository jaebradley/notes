# [The `yield` keyword in Ruby](https://medium.com/rubycademy/the-yield-keyword-603a850b8921)

* `yield` allows ability to pass a set of additional instructions during a method invocation
* When a block is recognized by the Ruby parser, it will literally replace the `yield`s in the method with the specified logic in the block
* If a `yield` exist in a method but the method is not given a block, the method will raise a `LocalJumpError`
* If a block is supposed to be optional then use `Kernal.block_given?`
* `yield` can take arguments

```ruby
def yield_args
  hello = 'hello'
  world = 'world'
  yield(hello, world)
end

yield_args{ |hello, world| puts "#{hello} #{world}" }
```

* `yield` can also return so the execution of the specified block can be assigned to a variable

## Rewriting `Array#map`

* Notice the `block_given?`call that returns the existing array if no block is given
* Then iterate through the elements applying the specified block to each element and pushing the result value to the array of results

```ruby
class Array
  def my_map
    return self.dup unless block_given?

    ary = []

    self.each do |element|
      ary << yield element
    end

    return ary
  end
end
```
