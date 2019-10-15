# Chapter 5: Reducing Costs with Duck Typing

* Duck typing are public interfaces that are not tied to any specific class
* A Ruby object can implement many different interfaces - users should not be concerned about its class
  * Class is just one way for an object to acquire a public interface
  * It's not what an object is that matters, it's what it does

## Back to `Trip`s and `Mechanic`s

```ruby
class Trip
  attr_reader :bicycles, :customers, :vehicles

  def prepare(mechanic)
    mechanic.prepare_bicycles(bicycles)
  end
end

class Mechanic
  def prepare_bicycles(bicycles)
    bicycles.each {|bicycle| prepare_bicycle(bicycle) }
  end

  def prepare_bicycle
    #
  end
end
```

* `prepare` has no explicit dependency on `Mechanic` but it does depend on receiving an object that responds to `prepare_bicycles`
* Need to support both a trip coordinator and a driver
  * So create a new `TripCoordinator` and `Driver` class and change `Trip`'s `prepare` to execute correct preparation behavior based on the type of preparer

```ruby
def prepare(preparers)
  preparers.each{|preparer|
    case preparer
    when Mechanic
      preparer.prepare_bicycles(bicycles)
    when TripCoordinator
      preparer.buy_food(customers)
    when Driver
      preparer.gas_up(vehicle)
      preparer.fill_water_tank(vehicle)
    end
  }
  end
end
```

* A lot of dependencies
  * It relies on a bunch of specific classes
  * It knows name of the messages that each class understands
  * It knows all arguments that messages require
  * Knowledge increases risk
* This style exacerbates over time
  * New preparer will mean new `when` branch to `case` statement
* Other signs of a duck
  * Using `kind_of?` or `is_a?`
  * Using `responds_to?`
  * "I know who you are and because of that I know what you do"

## Using a `Preparer` Duck Type

* What does `prepare` want / need? It wants to prepare the trip
  * Expects each input to be a `Preparer`
* What message should a `Preparer` have? Probably `Preparer#prepare_trip`.
* `Preparer` has no concrete definition / it's an abstraction / agreed-upon public interface
* Thus, `Mechanic`, `TripCoordinator`, and `Driver` should behave like `Preparers` and implement `prepare_trip`

