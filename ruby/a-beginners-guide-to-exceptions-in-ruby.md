# [A Beginner's Guide To Exceptions in Ruby](http://blog.honeybadger.io/a-beginner-s-guide-to-exceptions-in-ruby/)

```ruby
# rescues all errors and puts the exception object in `e`
rescue => e

# # Rescues only ZeroDivisionError and puts the exception object in `e`
rescue ZeroDivisionError => e
```

* Exceptions generally have useful data
  * `class.name` (`ZeroDivisionError`)
  * `message` ("divided by 0")
  * `backtrace`
* Many ways to `raise`
  * `raise RuntimeError.new("Some error")`
  * `raise RuntimeError, "Some error"`
  * `raise "Some error"` (This only `raise`s `RuntimeError`s)
* Custom errors
  * `class SomeError < StandardError`
* Rescuing all exceptions
  * `rescue Exception => e`
    * Handles errors from "Signals" (like `ctrl + c` to exit a program )
    * Handles `SyntaxError`
  * `rescue StandardError => e`
    * Handles app's exceptions
