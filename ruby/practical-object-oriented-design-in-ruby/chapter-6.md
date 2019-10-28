# Chapter 6: Acquiring Behavior Through Inheritance

* Inheritance is a mechanism for automatic message delegation
  * Messages are forwarded from subclass to superclass

## Example using Bicycles

```ruby
class Bicycle
  attr_reader :size, :tape_color

  def initialize(args)
    @size = args[:size]
    @tape_color = args[:tape_color]
  end

  def spares
    { chain: '10-speed',
      tire_size: '23',
      tape_color: tape_color }
  end

end
```

* Need to support mountain bikes now
  * Mountain bikes have rear suspension, while previous bokes have front suspension only
  * Other differences

## Anti-pattern: Using a `style` variable

```ruby
# Inside Bicycle class

def spares
  if style == :road
    { chain: '10-speed',
      tire_size: '23',
      tape_color: tape_color }
    }
  else
    { chain: '10-speed',
      tire_size: '2.1',
      rear_shock: rear_shock }
    }
  end
end
```

* Downsides of this `if`-based branching logic
  * If add a new `style`, must change `if` statement
  * Default option might be do something unexpected
  * Default strings are duplicated across `if`-statement (like `10-speed`)
* The code contains an `if` statement that checks an attribute that holdes the category of self to determine what message to send to self
* Class of an object is merely a specific case of an attribute that holds the catory of self, so previous anti-pattern where `if` statement checking on `class` of object to determine what message to send to that object is similar
  * "I know who you are and because of that, I know _what you do_"
* Lookout for `type` and `category` as variable names that might indicate an underlying pattern
  * What is a class if not a category or a type?

## Using an Abstract Superclass

* `Bicycle` is superclass for both `MountainBike` and `RoadBike`
  * `MountainBike` should be everything `Bicycle` plus some more
  * Any object that expects a `Bicycle` should be able to interact with a `MountainBike` without having to know that they're interacting with a `MountainBike`
* Abstract classes exist to be subclassed - they provide a common repository for behavior that is shared across a set of subclasses
* It almost never makes sense to create an abstract superclass with only one subclass

### Moving methods to common superclass

* `RoadBike` has `size` and `spares` method
  * `MountainBike` doesn't implement these methods, so it breaks

```ruby
class Bicycle
  attr_reader :size

  def initialize(args={})
    @size = args[:size]
  end
end

class RoadBike < Bicycle
  attr_reader :tape_color

  def initialize(args)
    @tape_color = args[:tape_color]
    super(args)
  end
end
```

* `RoadBike` overrides `initialize` by providing a receiver for this method (it's own implementation)
  * If both `initialize`s need to run `RoadBike` is obligated to call `Bicycle`'s `initialize` using `super`
* When moving methods from an abstract class to a concrete class, be careful as some logic may be left behind where it should not apply to every subclass

### Separating Abstract from Concrete

* Both `RoadBike` and `MountainBike` implement a version of `spares` method

```ruby
class RoadBike < Bicycle
  def spares
    { chain: '10-speed',
      tire_size: '23',
      tape_color: tape_color }
  end
end

class MountainBike < Bicycle
  def spares
    super.merge({rear_shock: rear_shock})
```

* `MountainBike#spares` expects superclass to implement `spares` (since it uses `super`), but `Bicycle` does not currently implement `spares` (so `MountainBike#spares` breaks)
  * Fixing problem is not as simple as promoting existing code from `RoadBike` as `RoadBike#spares` has values that are very specific to `RoadBike`s (like the `chain` and `tire_size` values)
* Solution is something like
  * All bicycles have chain and tire size
  * All bicycles share same default for chain
  * Subclasses provide defaults for tire size
  * Concrete instances of subclasses are permitted to ignore defaults and supply instance-specific values

## Using Template Method Pattern

* `default_chain` and `default_tire_size` are added as two methods
* Goal is to have subclasses implement the methods and define their "specialization"
* The technique of defining a basic structure in the superclass and sending messages to acquire subclass-specific contributions is known as the template method pattern
* Subclasses can inherit the default implementation of `default_chain` or `default_tire_size` from `Bicycle` or supply it's own default
* `Bicycle` must implement every method, even if implementation is raising a `NotImplementedError`

### Coupling between superclass and subclass

* Previous `MountainBike#spares` called `super` and `merge`d the superclass's values with it's own
  * This represents coupling - `MountainBike` knew things about the superclass (that it implements `spares` and returns a `hash`)
* Any knowledge of other classes creates dependencies and dependencies couple objects together
* The downside of this coupling is that if `super` is not called, the behavior might not be as expected

### Decoupling subclasses using hook messages

```ruby
class Bicycle
  def initialize(args={})
    # constructor

    post_initialize(args)
  end

  def post_initialize(args)
    nil # to be overridden by superclass if need to do something on initialization
  end
end

class RoadBike < Bicycle
  def post_initialize(args)
    @tape_color = args[:tape_color]
  end
end
```

* Using `post_initialize` hook doesn't just remove `super`, it removes `initialize` method altogether as `RoadBike` no longer needs to control initialization
* `RoadBike` is still responsible for what initialization it needs, but is no longer responsible for when the initialization occurs
* `RoadBike` knows less about `Bicycle`, reducing coupling between the two
  * `RoadBike` doesn't know when `post_initialize` will be called
* Same approach can be used to remove `super` from `spares` method

```ruby
class Bicycle
  def spares
    { tire_size: tire_size,
      chain: chain }.merge(local_spares)
  end

  def local_spares
    {}
  end
end

class RoadBike < Bicycle
  def local_spares
    { tape_color: tape_color }
  end
end
```

* By specifying a `local_spares` method, it allows `RoadBike` to still supply specific behavior, but reduces coupling to `Bicycle` as `RoadBike` no longer needs to know about `Bicycle` and that `Bicycle` implements `spares`.
