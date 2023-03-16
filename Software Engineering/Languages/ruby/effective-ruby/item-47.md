# Item 47: Avoid Object Literals In Loops

* `errors.any? { |e| %w(F1 F2 F3).include? e.code }` - will will create `4n` objects (where `n` is number of loops)
  * `%w(F1 F2 F3)` => `["F1", "F2", "F3"]` which means that these four objects are created each time the block is invoked
  * Each time an object is created, this might trigger a partial or full garbage-collection run
* Much better to move constant out to it's own variable (and appropriately frozen) and thus only four objects are created once when code is loaded
* Same thing with string literals - `errors.any?{ |e| e.code == "FATAL" }` will recreate the same string object over and over again
  * In Ruby 2.1+, by freezing the string, it will only allocate a single string on first invocation and subsequent invocations will use the same string object
  * Once a frozen string literal is introduced, it can be shared throughout a program