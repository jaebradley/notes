# Chapter 3: Managing Dependencies

```ruby
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog = cog
    @rim = rim
    @tire = tire
  end

  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end

  def ratio
    chainring / cog.to_f
  end
end
```

* `Gear` has at least four dependencies on `Wheel`
  * `Gear` expects another class called `Wheel`
  * `Gear` expects a `Wheel` instance to respond to `diameter`
  * `Gear` knows that `Wheel.new` requires a `rim` and a `tire`
  * `Gear` knows the first argument to `Wheel.new` should be `rim`, the second should be `tire`
* Some degree of dependency between these two classes is inevitable (they _must_ collaborate) but most dependencies are unnecessary
  * Increase chance that `Gear` will be forced to change if `Wheel` changes (i.e. "coupling")
* When `Gear` hard-codes a reference to `Wheel` deep inside its `gear_inches` method, it is explicitly declaring that it is only willing to calculate gear inches for instances of `Wheel`
  * `Gear` refuses to collaborate with any other kind of object, even if that object has a diameter and uses gears
  * It is not the class of the object that's important - it's the `message` you plan to send it
  * `Gear` needs access to an object that can respond to `diameter` - `Gear` does not care about the class of that object, it just needs an object that knows `diameter`
  * `Gear` instead can be initialized with an object that can respond to `diameter`
  * Moving the creation of a new `Wheel` instance outside of `Gear` decouples the two classes
    * The migration from explicit dependencies to implicit dependencies "injected" through the constructor is called "dependency injection"

## Isolate Instance Creation

* Move `Wheel` instantiation to the `initialize` method as a "private" variable
* Move `Wheel` instantiation to a `wheel` method that "lazily" creates new instance of `Wheel` using `||=`
* These changes make `gear_inches` simpler and make the dependency on `Wheel` more explicit

## Isolate Vulnerable External Messages

* Imagine that `gear_inches` was much more complex

```ruby
def gear_inches
  # computation
  foo = result_of_computation * wheel.diameter
  # more computation
end
```

* Removing the external dependency to `Wheel#diamter` by encapsulating it in it's own method (that just delegates), and thus `Gear#gear_inches` now depends on a message sent to `self`
  * If `Wheel` changes the name or signature of `Wheel#diamter`, the side-effects to `Gear` will be confined to this one simple wrapping method

## Choosing Dependency Direction

* Depend on things that change less often than you do
  * Some classes are more likely to change than others
  * Concrete classes are more likely to change than abstract classes
  * Changing a class that has many dependents will result in widespread consequences
* Gear depends on a "duck" that responds to `diameter` (i.e. "duck" is an "interface")
