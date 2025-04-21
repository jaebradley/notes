# [When A Class Is Loaded And Initialized In JVM](https://javarevisited.blogspot.com/2012/07/when-class-loading-initialization-java-example.html#axzz4uMIMWleJ)

* Initialization of the class takes place after a class is loaded
  * This means initializing all static members of the class
* Class initialization occurs when 
  * an instance of a class is created using the `new()` keyword or using reflection via `class.forName`
  * a static method of a class is invoked
  * if class is a top-level class (not a nested class within a class) and an assert statement is lexically nested within the class is executed
  * a static field of a class is assigned
* Classes are initialized from top to bottom so fields declared at the beginning of the class are initialized before fields declared at the bottom
* Super classes are initialized before sub-classes
* If class initialization is triggered due to access of static field, only the class which has declared the static field is initialized and it doesn't trigger initialization of the super class or sub-class even if the static field is referenced by the sub-class, sub-interface or by the implementation class of the interface
* Interface initialization in Java does not cause super interfaces to be initialized
* Static fields are initialized during static initialization of class while non-static fields are initialized when an instance of the class is created
  * This means that static fields are initialized before non-static fields in Java
* Sub-class constructors implicitly call super-class constructors before doing any initialization which guarantees that non-static or instance variables of the super class are initialized before the sub-class

```java
/**
 * Another Java program example to demonstrate class initialization and loading in Java.
 */

public class ClassInitializationTest {

    public static void main(String args[]) throws InterruptedException {
 
       //accessing static field of Parent through child, should only initialize Parent
       System.out.println(Child.familyName);
    }
}

class Parent {
    //compile time constant, accessing this will not trigger class initialization
    //protected static final String familyName = "Lawson";
 
    protected static String familyName = "Lawson";
 
    static { System.out.println("static block of Super class is initialized"); }
    {System.out.println("non static blocks in super class is initialized");}
}

/**
Output:
static block of Super class is initialized
Lawson
*/
```

* If `familyName` was declared as a compile-time constant (via the `final` keyword`, class initialization of the super-class would not have occurred
* Only super-class is initialized even though static field is referenced using sub-type
