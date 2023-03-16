# [Module#extend: Understanding Ruby Singleton Classes](https://medium.com/@jeremy_96642/module-extend-understanding-ruby-singleton-classes-9dea718c80f2)

* `extend`ing a module makes the module's methods available as class methods
* Instance methods live on the class reference for that instance
  * Saves memory so each instance of class doesn't need to have copies of every instance method
* Ruby class methods do not really exist - they are just instance methods that look and feel like class methods
* Singleton class lives "invisibly" between instance and it's class
  * Class method is a method that lives on singleton class
  * `Employee.some_class_method` is actually invoking `Employee` class, which is instance of Ruby's `Class` class
  * When `Employee` class is created, a singleton class is created on-the-fly that contains `:some_class_method`
  * `extend` adds methods on class' singleton class

```ruby
some_employee = Employee.new(1)

def some_employee.new_method
  puts "some new method"
end

some_employee.singleton_class.instance_methods.include?(:new_method)
# true

some_employee.class.instance_methods.include?(:new_method)
# false
```
