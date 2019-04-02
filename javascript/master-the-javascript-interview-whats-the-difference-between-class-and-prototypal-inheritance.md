# [Master the JavaScript Interview: What’s the Difference Between Class & Prototypal Inheritance?](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)

* Class-based design forms single-ancestor parent/child hierarchies and creates the tightest coupling available in object-oriented design
* Class inheritance creates parent/child object taxonomies as a side-effect
  * Taxonomies are almost impossible to get right for all new use cases
  * Use of base classes leads to fragile base class problem - difficult to fix when you get them wrong
  * Inflexible hierarchy (eventually, all evolving hierarchies are wrong for new use cases)
  * Due to inflexible hierarchy, new use cases are shoe-horned by duplication rather than adaptation
  * Class inheritance forces you to inherit everything - "I wanted a banana but instead I got a gorilla holding a banana and the entire jungle"
* Favor composition over inheritance is short for favor composition over _class_ inheritance
* Concatenative inheritance (mixins) - inheriting features directly from one object to another by copying the source object's properties
  * `Object.assign`ing a bunch of source objects into a target
* Prototype delegation - object tree that is searched for particular properties
  * Can combine concatenative inheritance to create a single delegate from multiple sources
* Functional inheritance - using functions, not as constructors, but as "factories" for producing objects with properties via concatenative inheritance
* Consider this inheritance tree

```text
   A
   |
   B
  / \
 C   D
```

* `D` is a new use case and needs different initialization behavior than `C`
  * `C` breaks because it depends on the existing behavior
* There are features between `A`, `B`, `C` and `D` that need to be used in a variety of ways
* `C` and `D` don't use every feature of `A` and `B` - only want a subset of the behavior
* Can do this using composition like `const C = compose(feat1, feat2, feat3)`
