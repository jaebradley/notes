# Item 6: Know How Ruby Builds Inheritance Hierarchies

* Classes are themselves objects
* Classes are basically containers of variables ("class variables") and methods ("class methods")
* Modules are also objects and instances of the `Module` class
* A singleton class is an anonymous and invisible class in the inheritance hierarchy - they are also referred to as eigenclasses or metaclasses
  * They can be introspected through `singleton_class` and `singleton_*` methods
* When a class includes a module, Ruby creates a singleton class and silently inserts it as the superclass of the defined class
  * This singleton class is "anonymous" and "invisible" so `MyClass.superclass` will skip over it
  * Inheritance hierarchy
    * `Object`
      * `SomeModule`
        * `MyClass`
  * When Ruby is searching for a method, it will visit each module in reverse order - the most recently included one is first
  * Modules can never override methods from the class that includes them - modules are inserted "above" the including class, so Ruby always checks the class before moving upward (except when `prepend` is defined)

```ruby
my_instance = MyClass.new
def my_instance.my_method
  "My Method"
end
```

* Ruby implements the above method, which only exists on the `my_instance` object, by using a singleton class
  * It adds the `my_method` method on the singleton class as an instance method, then inserts this anonymous class as the class of the `my_instance` object
  * Even though this singleton class is the class for `my_instance` the `class` method for Ruby will skip over it and return `MyClass`
  * The singleton method has a receiver (in this case `my_instance`) which is similar syntax to how class methods are defined (using `self`). So class methods are actually stored as instance methods on a singleton class.
* Helpful methods
  * `singleton_class` returns the singleton class, or creates it
  * `ancestors` returns array of all classes and modules that make up inheritance hierarchy (skips over singleton classes)
  * `included_modules` returns `ancestors` - classes