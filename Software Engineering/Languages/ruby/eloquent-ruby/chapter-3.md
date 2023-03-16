# Chapter 3: Take Advantage of Ruby's Smart Collections

* Use `*` parameter to allow a method to take an arbitrary argument set
* `*` is called a splat
* Parameter is an array of all the arguments

```ruby
def x(*args)
  args.each{ |arg| puts arg }
end
```

* Can do something similar with hashes, if hash is at end of argument list

```ruby
# Instead of
some_method({ :first => 'first', :second => 'second' })

# Do this instead
some_method(:first => 'first', :second => 'second')
```

* `inject` / `reduce` takes a block and calls block with each element in collection
  * Block takes two args, the current result and the current element
  * Current result is the return value of previous call to block
  * If no value is set as initial value, first element is used
* Hashes (unlike many other programming languages) have order
* Ruby tends to use collections instead of classes in many scenarios
  * Collections have a lot of helpers and tend to be powerful enough
  * If classes can be avoided, all the better
* `some_array[20000]` will initial an array with `20,001` elements
