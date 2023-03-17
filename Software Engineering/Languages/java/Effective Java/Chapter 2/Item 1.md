# Item 1: Consider static factory methods instead of constructors

* The normal way for a class to allow a client to obtain an instance of itself is to provide a public constructor
* Instead, a class can provide a public static factory method, which is simply a static method that returns an instance of the class
* An advantage of static factory methods is that unlike constructors, they have names
  * For example, a constructor like `BigInteger(int, int, Random)` which returns a `BigInteger` that is probably prime, would have been better expressed as a static factory method named `BigInteger.probablePrime`
* A class can only have a single constructor with a given signature, which programmers have worked around by providing two constructors whose parameter lists differ by the order of their parameter types
  * No API users will remember the appropriate constructor
* Unlike constructors, static factories are not required to create a new object each time they are invoked
  * They allo immutable classes to use preconstructed instances, or to cache instances as they are constructed and dispense them repeatedly to avoid creating unnecessary duplicate objects
  * `Boolean.valueOf` never creates an object - it just returns `Boolean.TRUE` or `Boolean.FALSE`. This strategy can greatly improve performance since object creation can be expensive
* A third advantage of static factory methods is that unlike constructors, they can return an object of any subtype of their return type
  * One application of this flexibility is that an API can return objects without making their classes public (i.e. hiding implementation classes)
  * Requires the client to refer to the returned objects from a static factory method by its interface rather than its implementation class
  * `EnumSet` has no public constructors and only has static factories
    * These factories return one of two implementations depending on the size of the underlying enum type
    * The client has no insight into these two implementation classes i.e. clients neither know nor care about the class of the object they get back from the factory, they only care that it is some subclass of `EnumSet`
* The main disadvantage of providing only static factory methods is that classes without public or protected constructors cannot be subclassed
* Another disadvantage is that they are not readily distinguishable from other static methods
  * Can avoid this by adhering to some common naming conventions like `valueOf`, `getInstance`, `of`, `newInstance`, etc
