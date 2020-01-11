# [Using lazy enumerators to work with large files in Ruby](https://www.honeybadger.io/blog/using-lazy-enumerators-to-work-with-large-files-in-ruby/)

* Calling methods like `each` returns an enumerator which can be used for iterative operations
* Regular enumerators have problems with large collections because they will try and store entire collection in memory / will try and iterate over entire collection when only subset may be needed
* `(1..Float::INFINITY).reject` vs. `(1..Float::INFINITY).lazy.reject`
* When reading from a file, using `each_line` method will load entire file into memory before iterating over it
  * So instead, do something like `file.each_line.lazy` 
* This can also be used when dealing with sockets and pipes as well because they're represented by the IO class and have `each_line`, `each_char` and `each_codepoint` methods
