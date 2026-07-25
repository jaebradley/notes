# [Chapter 8: Abstraction and Classes](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1162/course-reader/Ch8_AbstractionAndClasses.pdf)

## Abstraction
* Author uses stapler as an example of abstraction
* Stapler manufacturers spend effort considering tradeoffs between different materials and designs to build progressively better staplers
* These manufacturers design a complex, but easy-to-use product
* Abstraction is the description of an object that omits all but a few salient details
* Author goes through exercise of describing a coffee mug with different levels of abstraction: "matter", "an object", "beverage container", all the way to, "white ceramic coffe mug with a small crack in the handle...."
* What is important to people that use staples is the fact that staplers fasten paper together, not their inner workings (i.e. _how_ they staple paper together)

## The Wall of Abstraction
* The information barrier between a product and how it works
* The manufactor is on one side of this barrier, and is responsible for creating a product that can meet certain product requirements
* The end user is on the other side of the barrier, and doesn't particularly care how the product works
  * They don't want to know what type of metal the casing is made from, how what type of spring pushes the staples to the front of the staple, etc
  * End user trusts that the manufacturer's team of dedicated engineers understand these details

## Abstractions are Imprecise
* Author uses example of a car mechanic and a broken radiator
* Mechanic replaces radiator - this "version" of the car is not the exact same "version" of the car before the replacement
  * One of the car's fundamental components has been modified
  * However, from the driver's perspective, nothing about how the car drives has changed
* Car has a different "implementation" than th original car (i.e. the literal components that make up the car are different), but the new version of the car has the same abstraction as the previous car
* In this analogy, looking under the hood of the car and checking the car's internals would be piercing the "wall of abstraction"

## Interfaces
* Author continues with the stapler analogy: manual and digital staplers might provide the same data or operations, but the two staplers might differe in _how_ they support that data and operations
* A digital stapler might provide the number of remaining staples through a digital display whereas a manual stapler might "provide" this data via the user opening the stapler cover and counting the number of staples
* A digital stapler might staple papers together via the press of a button whereas a manual stapler staples papers together via the user manually pressing the handle of the stape
* These "interfaces" provide the set of "commands" and "queries" that can be executed, and informs the way the end user interacts with the object
