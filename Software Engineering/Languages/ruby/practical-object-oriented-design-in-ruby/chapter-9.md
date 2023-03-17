# Chapter 9: Designin Cost-Effective Tests

* Common for programmers who are new to testing where tests they write cost more than the value those tests provide (and thus argue about worth of tests)
* Tests are canary in coal mines - when design is bad, testing is bad
  * Costly tests do not necessarily mean that application is poorly-designed
  * Possible to write bad tests for well-designed code
* Think of an object-oriented application as a series of messages passing between a set of black boxes
* Well-designed objects have boundaries that are very strong (like space capsule)
  * Nothing on outside can see in
  * Nothing on inside can see out
  * Only few messages can pass through airlocks
* Tests should concentrate on incoming or outgoing messages
  * Tests that make seertions about the values that messages return are tests of state
  * Messages without side-effects are called "queries"
  * Messages with side-effects (file gets written, database record is saved) are called "commands"
    * responsibility of the sending object to prove that they are properly sent
    * tests of behavior, _not_ state

## `Gear` and `Wheel` example

```ruby
class Wheel
  def initialize(rim, tire)
    @rim = rim
    @tire = tire
  end

  def diameter
    rim + (tire * 2)
  end
end

class Gear
  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end
end
```

* Downsides to implementation where `Wheel` is instantiated inside `Gear
  * If `Wheel`s are expensive to create, `Gear` test pays that cost
  * If `Gear` is correct but `Wheel` is broken, `Gear` test might fail misleadingly

```ruby
class Gear
  def initialize(wheel:)
    @wheel = wheel
  end

  def gear_inches
    ratio * wheel.diameter
  end
end
```

* Now, all `Gear` expects is a `wheel` argument that responds to `diameter`
* Can use a test double that responds to `diameter` to take on the role of a "Diameterizable" object
* If `Wheel` is updated to implement new interface (`diameter` is renamed to `width`), then `Diameterizable` must also change

## Proving Command Messages

* Imagine `Gear` takes `observer` argument that it calls method on when setting a cog

```ruby
class Gear
  def initialize(observer:)
    @observer = observer
  end

  def set_cog(new_cog)
    @cog = new_cog
    changed
  end

  def changed
    observer.changed(chainring, cog)
  end
end
```

* `Gear` has new responsibility of notifying `observer` when cogs or chainrings change
* Tests should prove that message gets sent
  * Should prove message gets sent without making assertions about result of `observer.changed`
  * `observer`'s tests are responsible for making assertions about the result of `changed` method
* Use `mock` to check what comes back - `mocks` are test of behavior, not state
  * All `mock` should do is remember what it sent
  * If object under test depends on result, `mock` can be configured to return an appropriate resul
  * Return value is beside the point - mocks are meant to prove messages get sent; the return result only matters to get tests to run successfully

## Testing Duck Types

* `Preparable` and `Preparer` from before can be tested in a single module, and then each concrete class that uses module can include the generic test in their concrete tests

```ruby
module PreparerInterfaceTest
  def test_implements_the_preparer_interface
    assert_respond_to(@object, :prepare_trip)
  end
end

class MechanicTest
  include PreparerInterfaceTest

  def setup
    @mechanic = @object = Mechanic.new
  end
end

class TripTest
  def test_requests_trip_preparation
    @trip = Trip.new
    @preparer.expect(:prepare_trip, nil, [@trip])
    @trip.prepare([@preparer])
  end
end
```

* `TripTest` creates a `mock` (`preparer`), tells the mock to expect a certain message, and then calls `Trip` instance with `preparer`
  * This proves that `Trip` collaborates with `Preparers` using correct interface

##  Using Role Tests to Validate Doubles

* Previous problem where `Wheel` changed but `Diamterizable` double didn't
* Create `DiameterizableInterfaceTest` (which asserts that `object` is called with `width`)
* `WheelTest` `includes DiameterizableInterfaceTest`
* `GearTest` also `includes DiamterizableInterfaceTest` and in it's `setup` creates a `DiameterDouble` on `object`

