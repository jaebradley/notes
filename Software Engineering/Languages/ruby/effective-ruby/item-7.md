# Item 7: Be Aware Of The Different Behaviors Of `super`

* If no arguments are given **and no parenthese are used** `super` will invoke target method with _all_ arguments given to enclosing method and forwards method block if one is associated with it
  * `ArgumentError` exception if target method accepts same number of arguments as enclosing method
* When don't want to pass any arguments to overriddent method, write `super` with empty set of parentheses like `super()`
* Note that `super` walks the inheritance hierarchy (including super classes and modules) and will stop at the first matching method