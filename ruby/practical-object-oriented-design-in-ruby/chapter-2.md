# Chapter 2: Designing Classes with a Single Responsibility

* Easy to change code should
  * Changes have no unexpected side-effects
  * Small changes in requirements require correspondingly small changes in code
  * Existing code is very reusable
* Rephrase method as question to ask class
  * "Gear, what is your ratio?" - makes sense
  * "Gear, what is your tire size?" - makes way less sense
* Describe class in one sentence
  * If use `and` or `or`, class probably has more than one responsibility and they aren't very related
* Highly cohesive classes are related to a central purpose
  * Single Responsibility Principle doesn't mean that class only does one thing but that everything it does is very related to it's purpose
* Wrap instance variables in a public method
  * If instance variable is used in 10 places and needs to change, if using public getter, can change implementation of getter for variable to reflect the necessary change
* Hide data structures
  * When processing data that's in an array, code can become dependent on array structure (hard-coding indices based on idea that arrays are two-dimensional)
  * This is a leaky abstraction because the array's structure is now leaking into code
  * Can use a `struct` or a `class` to wrap the array so instead of `cell[0]` and `cell[1]` you can create a `Wheel.new(rim: cell[0], tire: cell[1])` and then use that `Wheel` instance with a more descriptive / usable API
  * Can have one method do translation, and thus the code that knows about the internal array structure is isolated
* Small methods can have a clarifying effect on the class / makes the responsibility of the class more obvious
* Small methods encourage reuse and make rearranging behavior easier
* In chapter's Gear & Wheel example, eventually a Wheel `struct` is defined in the Gear class
  * However, this suggests that a Wheel will only exist in context of Gear - this is obviously not true
  * So Wheel is abstracted out to it's own class and it calculates a `diameter` that's used by the Gear class

