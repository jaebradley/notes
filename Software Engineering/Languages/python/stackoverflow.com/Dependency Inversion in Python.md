# [Dependency Inversion in Python](https://stackoverflow.com/a/68911711/5225575)

* Dependency Inversion Principle consists of two parts
  * High-level modules should not depend on low-level modules - both should depend on abstractions
  * Abstractions should not depend on details. Details should depend on abstractions
* A module can be a function, a class, a file, a piece of code
* Baking bread program with a `cook` method that depends on creating a `Bread` object and calling the `bake` method on the constructed `Bread` object
* `cook` depends on `Bread`
* This means that when you want to bake cookies, and you have to create a `Cookies` object, the `cook` method will take a parameter that will branch the logic
  * This approach does not scale as adding more foods increases the complexity of the code
* Create a `Bakable` interface / abstraction that has a `bake` method
* `cook` now takes a `Bakable` object (i.e. an object that implements the `bake` method)
* `Bread` and `Cookies` implements the `Bakable` interface

