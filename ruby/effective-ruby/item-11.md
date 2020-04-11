# Item 11: Create Namespaces By Nesting Code In Modules

* Ruby uses two techniques for finding constants
  * First, it inspects the current lexical scope and all of the enclosing lexical scope
  * Then, it will search the inheritance hierarchy (can use constant defined in superclass from subclass)
* Module definitions create a lexical scope - which is why if a variable is defined within a module, and then a class is defined inside a module, the class can use the constant
* Lexical scope is different form the namespace created by the module - lexical scope has to do with the physical space where the constants are defined and used

```ruby
module Example
  SOME_CONSTANT = 'SOME CONSTANT'

  class AnotherExample
    def example
      SOME_CONSTANT
    end
  end
end

module Example
  SOME_CONSTANT = 'SOME CONSTANT'
end

class Example::AnotherExample
  def example
    # raises NameError
    SOME_CONSTANT
  end
end
```

* Namespace and lexical scope are created with module definition in second example, but both are closed
* Class is defined in correct namespace but doesn't share same lexical scope as `SOME_CONSTANT` and cannot access it unqualified
  * Need to do `Example::SOME_CONSTANT`