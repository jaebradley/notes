# Enforce noninstantiability with a private constructor

* Occasionally, you'll want to write a class that is just a grouping of static methods and static fields
  * Such classes have acquired a bad reputation because some people abuse them to avoid thinking in terms of objects
  * They have valid use cases like grouping related methods (i.e. `java.lang.Math`)
* These utility classes were not designed to be instantiated, however, the compiler provides a public, parameterless default constructor
* Do not attempt to enforce noninstantiability by making a class abstract
  * The class can be subclassed and instantiated
  * It misleads user into thinking the class was designed for inheritance
* Instead, make the class noninstantiable by including a private constructor

```java
private SomeClass() {
  throw new AssertionError();
}
```

* `AssertionError` is to provide insurance in case the constructor is accidentally invoked from within the class, guaranteeing that the class will never be instantiated under any circumstances
* Prevents the class from being subclassed since all constructors must invoke a superclass constructor
