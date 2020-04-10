# Item 9: Be Alert For Ruby's Most Vexing Parse

* The exclamatation point usually means that the method will mutate the receiver but can also warn about a potentially harmful action
* Appending `=` to the end of a method's name turns it into a setter that updates internal state
  * Techncailly, `=` is part of the method name, but Ruby allows whitespace between it and rest of name
* There's a parsing ambiguity between variable assignment and calling a setter method

```ruby
class Counter
  attr_accessor :counter

  def initiatize
    counter = 0
  end
end
```

* Obviously `counter = 0` will set a local variable, `counter` to `0`
  * To call the setting, need to assign an explicit _receiver_ - i.e. `self` to the method name
  * So needs to be `self.counter = 0`
* Putting `self` in front of everything is a little "ugly" / litters the code unnecessarily - so with no setter methods, can safely remove the use of `self` and rely on parsing rules
* When Ruby sees an identifier it will first check to see if there's a variable in the current scope and if it doesn't find one, then tries to look up the identier as a method