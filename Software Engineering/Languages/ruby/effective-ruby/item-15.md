# Item 15: Prefer Class Instance Variables To Class Variables

* Class variables (`@@`) are attached to a class and are immediately visible to all instances of that class
* Class variables in a superclass are shared between it and all of its subclasses
  * Any instance of one of those classes can access the shared class variables and modify them
* Usecase example in the book is building a `Singleton` class where there's a single `instance` class method that relies on a `@@single` class variable
  * If `@@single` is not defined, a new instance is set as the return value of the `instance` class method
  * If `@@single` is defined, the first instance is returned
  * Issue is that the second instance of the `Singleton` class (or a subclass) will return whatever the first instance of the `Singleton` class returned
* So instead, change `@@single` to be a class instance variable (i.e. `@single`)
  * Classes are objects, so "class methods" are just instance methods, on the `class` object
  * Class instance variables (`@`) are only accessible at the class definition level or from inside class methods (unlike class variables)

```ruby
class Singleton
  private_class_method :new, :dup, :clone

  # this will return same instance after first time Singleton (or any class that inherits from Singleton) is instantiated
  def self.instance
    @@single ||= new
  end

  # instead, use a class instance variable so that the instance is memoized for a given class
  def self.instance
    @single ||= new 
  end
end
```