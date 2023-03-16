# Chapter 7: Sharing Role Behavior with Modules

* `Preparer` interface from previous chapter was example of "role"
  * `Mechanic`, `TripCoordinator`, and `Driver` implement `prepare_trip`
  * `Trip` class was a `Preparable` (as roles often come in pairs)
* Most object-oriented languages provide a way to define a named group of methods that are independent of classes and can be mixed into any object
  * Ruby has modules, which provide a way to allow objects of different classes to play a common role using single set of code

## Scheduling a Trip

* Bicycles and other vehicles must undergo maintenance between trips
* `Schedule` class exists with this interface
  * `scheduled?(target, starting, ending)`
  * `add(target, starting, ending)`
  * `remove(target, starting, ending)`
* To incorporate maintenance time into `Schedule`, `Schedule` could check the class of `target`
  * To avoid class checking, `Schedule` could assume that each target implement a `lead_days` method
  * Since `Schedule` does not care about `target`'s class, but just that it responds to specific message, this suggests a `Schedulable` role / module

```ruby
module Schedulable
  attr_writer :schedule

  def schedule
    @schedule ||= ::Schedule.new
  end

  def schedulable?(start_date, end_date)
    !scheduled?(start_date - lead_days, end_date)
  end

  def scheduled?(start_date, end_date)
    schedule.scheduled?(self, start_date, end_date)
  end

  def lead_days
    0
  end
end
```
* Uses template method pattern to invite objects to provide specializations of the algorithm it supplies

## Lookup Chain

* When a single class includes several different modules, modules are placed in lookup path in reverse order of module inclusion
  * Methods of the last included module are encountered first
* Possible to add module's methods to a single object using `extend`
  * `extend` adds module's behavior to an object, `extend`ing a class with a module creates class methods in that class
* Objects can also have ad hoc methods be added directly on it's specific class (i.e. "Singleton Class")
* Lookup chain is something like
  * Check singleton class (methods defined on just this instance)
  * Check modules that have extended this instance
  * Check class methods
  * Check methods defined in included modules in class definition
  * Check superclass (with same approach)

## Writing Inheritable Code

* Liskov Substitution Principle - a subclass can be used anywhere a superclass would be, and objects that include modules can be trusted to interchangeably play module's role
* Template method pattern is fundamental codigin technique for creating inheritable code
  * Abstract code defines algorithm while concrete inheritors define specialization by overriding template methods
* Avoid writing code that requires inheritors to send `super` - instead use "hook messaages"
  * When class hierarches deepen, it's impossible to not use `super`
  * If previous `MountainBike` class with own `local_spares` was extended with a `MonsterMountainBike`, `MonsterMountainBike` would be forced to override `local_spares` and send `super`
* Avoid deep hierarchies of classes
