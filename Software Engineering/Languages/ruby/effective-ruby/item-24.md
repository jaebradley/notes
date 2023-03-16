# Item 24: Manage Resources With Blocks And Ensure

* If you rely on the garbage collection you should know there are no guarantees about when a resource will be released
* Whenever you're opening a resource (like a `File`) use an `ensure` to close it
  * Note how the `file` variable is initialized before being used - uninitialized variables will always be `nil`

```ruby
begin
  file = File.open
ensure
  file.close if file
end
```
* Can also use blocks to manage resources

```ruby
File.open do |file|

end
```

* Example `Lock` class meant to show how to handle blocks
  * The `block_given` method will return `true` if a block is associated with a method
  * So if a block _isn't_ given, it works like `File.open` where caller is responsible for unlocking it / running an `ensure`
  * Use `yield` to give `lock` instance to the block caller

```ruby
class Lock
  def self.acquire
    lock = new
    # ...
    if block_given?
      yield lock
    else
      lock
    end
  ensure
    if block_given?
      lock.unlock if lock
    end
  end
end
```