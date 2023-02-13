# Item 5: Avoid creating unnecessary objects

* Extreme example of creating unnecessary objects: `String s = new String("some string");`
* The argument to the `String` constructor is itself a `String` instance, and is functionally identical to all of the objects created by the constructor call
  * This can create millions of `String` instances needlessly - instead use `String s = "some string"`
  * Uses a single `String` instance, rather than creating a new one
  * Guaranteed that the object will be reused by any other code running in the same virtual machine that happens to contain the same string literal
* `Boolean.valueOf` is almost always preferable to the constructor `Boolean()` because the static factory method never creates a new object while the constructor does
* In example, a `Person` class instantiates a `Calendar` object in an instance method
  * Instead, create the `Calendar` object in a static initializer so that the instances are created once instead of every time the instance method is invoked
* An adapter (or view) is an object that delegates to a backing object, providing an alternative interface to the backing object
  * An adapter has no state byond that of its backing object, so there's no need to create more than one instance of a given adapter to a given object
  * For example, the `keySet` method of the `Map` interface returns a `Set` view of the `Map` object, consisting of all the keys in the map
    * A naive interprestation of the `keySet` method is that it would have to create a new `Set` instance
    * However, it can return the same `Set` instance (for a given `Map` instance)
    * All of the returned `keySet` `Set`s are functionally identical - when one of the returned objects changes, so do all the others because they are backed by the same `Map` instance
    * While it is harmless to create multiple instances of the `keySet` view object, it is also unnecessary
* Autoboxing can create unnecessary objects (`Long sum = 0L`)
  * This creates unnecessary `Long` instances - prefer primitives to boxed primitives and watch out for unintentional autoboxing
