# Item 16: Duplicate Collections Passed As Arguments Before Mutating Them

* Most objects are passed around by reference and not as actual values
  * Exception is the `Fixnum` class whose objects are always passed around by value and not be reference
  * True for objects passed as method arguments - method will receive reference to object and not a new copy
* Obviously, avoid mutating method arguments and return a new collection instead (i.e. use `reduce`)
* `dup` vs. `clone`
  * `clone` respects the frozen state of the receiver so if the original object was frozen, then the `clone`d copy will also be frozen
    * `dup` never returns a frozen object
  * If receiver has singleton methods, `clone` will duplicate those in singleton class
    * `dup` doesn't do this so original object and copy may not respond to same message
  * Most of the time, probably want to use `dup` instead of `clone` especially in case when planning on mutating object since `clone` may return a frozen object which obviously cannot be mutated
* `dup` and `clone` return shallow copies meaning that the collection is duplicated but the underlying elements are not
  * A quick and dirty way of deep cloning a collection is by using the `Marshall` class to serialize / deserialize object (using `Marshal.load(Marshal.dump(some_object))`)
  * Obviously, need to consider memory needed to duplicate object as well as byte stream of `Marshal::dump`
  * Not all objects can be serialized by `Marshall` like objects with closures or with singleton methods
    * `IO` and `File` classes will raise a `TypeError` when called by `Marshall:dump`