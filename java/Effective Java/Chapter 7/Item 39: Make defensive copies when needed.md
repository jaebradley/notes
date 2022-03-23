# Item 39: Make defensive copies when needed

* Example is a `Period` class that takes a `Date` `start` private member and an `end` `Date` private member and returns both member variables via getters
* The `Period` class is not immutable as the `Date` class is not immutable (can call `setYear` on the `Date` instance returned by `getStart` / `getEnd` and modify the internal `Date` value
* Instead, getters should return defensive copies of each mutable parameter to the constructor
  * `this.start = new Date(start.getTime())`
* Note that defensive copies are made _before_ checking the validity of parameters, and the validity check is performed on the copies rather than on the originals
  * This protects the class against changes to the parameters from another thread during the window of vulnerability between the time the parameters are checked and the time they are copied
* Don't use `Date`'s `clone` method to make defensive copies because `Date` is nonfinal and the clone method is not guaranteed to return an object whose class is `Date` - it could return an untrusted subclass
* Modify accessors to return defensive copies of mutable internal fields
  * The `getStart` method would return `new Date(this.start.getTime())`
