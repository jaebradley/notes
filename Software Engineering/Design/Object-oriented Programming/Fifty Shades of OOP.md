# [Fifty Shades of OOP](https://lesleylai.info/en/fifty_shades_of_oop/)

## Method Syntax
* Even in languages without methods, it is common to see functions effectively serving as methods by taking the relevant data ("the instance") as their first argument
* Method chaining is often more ergonomic than nested function calls (hence why the pipe operator is attractive in functional languages)

## Information Hiding
* More modern OOP languages support information hiding via access specifiers like `private`
* Functional programmers argue that you don't need to maintain invariants and don't need information hiding if data is immutable
* OOP encourages mutable objects that may be maintained as invariants
* Information hiding encourages the creation of self-contained objects that "know how to handle themselves"

## Encapsulation
* Encapsulation is often confused with information hiding, but they are distinct
* Encapsulation is bundling data with functions that operate on that data

## Interfaces
* "No part of a complex system should depend on the internal details of any other part"

## Inheritance
* Inheritance is a single mechanism that enables dynamic dispatch, subtyping polymorphism, interface/implementation segregation and code reuse
* While flexible, this flexibility makes it easy to misuse
* Inheritance hierarchies are rigid

## Subtyping Polymorphism
* Subtyping describes an "is a" relationship between two types
* Structural subtyping is a form of implicit subtyping where one type contains all the features of another type

## Message Parsing
* "I thought of objects like biological cells and/or individual computers on a network that are only able to communicate with messages"
