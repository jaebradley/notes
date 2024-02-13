# Item 38: Check parameters for validity

* For public methods, use the `@throws` tag to document the exception that will be thrown if a restriction on parameter values is violated
* Typically, the exception will be `IllegalArgumentException`, `IndexOutOfBoundsException` or `NullPointerException`
* Non-public methods should generally check their parameters using assertions (i.e. the `assert` keyword)
  * Assertions throw `AssertionError` if they fail, but unlike normal validity checks, they have no effect and no cost unless enabled by the `-ea` flag to the `java` interpreter
* Important exception to checking a method's parameters before performing its computation is the case in which the validity check would be expensive or impractical and the validity check is performed implicitly in the process of doing the computation
  * `Collections.sort` must be mutuall comparable
  * If the objects in the passed in collection are not mutually comparable, the comparisons will throw `ClassCastException`, which is exactly what the sort method should do
  * Therefore, little point in checking ahead of time if the elements in the list were mutually comparable
* The exception that the computation would naturally throw as the result of an invalid parameter value should match the exception that the method is documented to throw
