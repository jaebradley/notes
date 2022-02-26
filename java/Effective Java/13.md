# Item 13: Minimize the accessibility of classes and members

* A well-designed module hides all of its implmentation details, cleanly separating its API from its implementation
* Modules then communicate only through their APIs and are oblivious to each others' inner workings
* Make each class or member as inaccessible as possible
* If a top-level class or interface can be make package-private, it should be
* If a package-private top-level class or interface is used by only one class, consider making the top-level class a private nested class of the sole class that uses it
* All members should be `private`, if possible
* It is not acceptable to make a class, interface, or member a part of a package's exported API to facilitate testing
* Can expose constants via `public static final `fields
* A non-zero length array is always mutable, so it is wrong for a class to have a public static final array field, or an accessor that returns such a field
  * Can make the array private and add a public method to return a copy of the private array
