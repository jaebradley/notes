# [Interfaces - The Most Important Software Engineering Concept](https://blog.robertelder.org/interfaces-most-important-software-engineering-concept/)

## Laptop Analogy

* "System" is the laptop computer
* "Environment" is the laptop operator's hands
* "Interface" is any part of the interaction between the hands and the computer that is not exclusively attributable to one or the other, but can only be attributed to both
* The interace "contract" is then the location of all the keys (for example, where all the QWERTY keys are found)
  * Another part of the interface contract is the utilization of the keys, like short press vs. a long press
* So could still use the laptop if its internals were made differently (but functionality was not modified), but if the keys changed or the screen changed, then that would introduce an interface change and problems when interacting with it

## `add_numbers` function as an interface contract

* Example contract guarantees
  * Function that exists
  * Takes two parameters
  * Returns one value
* Interface does not guarantee anything around
  * Whether the function will ever halt
  * The run-time complexity of the function
  * The amount of memory required to execute the function
  * Side-effects like global variables

## What is a 'Module' or 'Abstraction'?

* Author's mental model of a "module" is one of those wooden child's toy cubes with different shapes cut out of the sides of the box
* The "interface" of the cube imposes very strong contraints about how the external world can interact with what is inside
* If you want to interact with the cube, you must do so through the means it exposes to you
* Don't care what's inside the cube, only care about the interface it exposes to the world

## Abstraction Leaks

* Map interface represents a data structure consisting of pairs of keys and values
* Map interface guarantees that all map keys are unique
* Map interface does not guarantee any key ordering when iterating over them
* Making assumptions about the key ordering for a map can cause problems when swapping one map implementation out for another one
* Abstract leak exists when it is possible for an implementation to affect the environment in a way that was not agreed upon in the interface
* Problems only occur when a part of the environment begins to rely on one of these unspecified environmental effects that originated from the system in question

## How to Cut Corners Efficiently

* If you have to cut corners in your project, do it inside the implementation, and wrap the implementation with a well-defined interface
