# Chapter 4: Creating Flexible Interfaces

* Public vs. Private interfaces like a kitchen where menu is "public" interface and then all messages that occur inside kitchen are the "private" interface(s)
  * Allows customer to ask for what they want without having to know HOW it's made
* Shift from "I know I need this class and what should it do" to "I need to send this message, who / what should respond to it"
  * You don't send messages because you have objects, you have objects because you send messages

## Preparing Trips for Customers

* Need to prepare trips on a given date & difficulty
* Need to know what bicycles are suitable
* `Customer` asks `Trip` for suitable trips
* Each `Trip` calls `Bicycle` for suitable bicycles
* However, `Customer` knows how other objects collaborate

## Preparing Bicycles

* All bicycles need to be vetted as mechanically sound before a trip
* Initial idea
  * `Trip` tells `Mechanic` how to prepare each bike (clean tires, pump tiers, fix brakes)
  * `Trip` knows about `Mechanic` so if `Mechanic` changes (like adding a new preparation step), `Trip` must also invoke this new method
* Next idea
  * `Trip` asks `Mechanic` to prepare bicycles, giving most responsibility to `Mechanic`
* Final idea
  * An object's context is the things it knows about other objects
  * `Trip` knows that it has to have a `Mechanic` object that's going to respond to `prepare_bicycle`
  * Objects with complex context are hard to use and hard to test
  * WHAT `Trip` wants is to be prepared - that it must be prepared is within `Trip`'s responsibilities
  * HOW `Trip` should be prepared, and specifically, that _bicycles_ should be prepared is the task for `Mechanic`
  * `Trip` calls `Mechanic` with `prepare_trip`, passing itself as the argument
  * `Mechanic` calls `Trip`'s `bicycles` method
  * `Mechanic` prepares each returned `Bicycle`
  * In future, `Trip` could place many objects into an array and send each a `prepare_trip` message, and thus, trusting each preparer to do whatever it needs to do letting one extend `Trip` without modifying it
  * First approach is "I know what I want and I know how you do it"
  * Second approach is "I know what I want and I know what you do"
  * Third approach is "I know what I want and I trust you to do your part"

## Back to Original Problem

* `Customer` sends `suitable_trips` message to a `TripFinder` class
* `TripFinder` class sends `suitable_trips` message to `Trip` class
* `TripFinder` iterates through each trip and sends `suitable_bicycle` message to `Bicycle`

## Demeter

* Prohibits routing a message to a third object through a second object of a different type
  * "Only talk to your immediate neighbors" or "only use one dot"
* `customer.bicycle.wheel.tire`
  * If `Wheel$#tire` changes, the underlying value may change which raises the cost of the change
  * Chain that reaches across many objects, of different types to get to behavior
* `hash.keys.sort.join`
  * The intermediate object types are consistent
  * `hash.keys` returns an `Enumerable`
  * `hash.keys.sort` also returns an `Enumerable`
  * `hash.keys.sort.join` returns a `String` (which we'll look past a little)
* "There's some behavior way over there that I need right here and I know how to go get it"
* Code knows what it wants and how to get it
  * This means that the implementatino is bound to a very specific implementation and cannot be reused in any other context


