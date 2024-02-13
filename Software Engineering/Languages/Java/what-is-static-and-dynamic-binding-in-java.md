# [What Is Static And Dynamic Binding In Java](https://javarevisited.blogspot.com/2012/03/what-is-static-and-dynamic-binding-in.html)

* Static binding occurs during compile time while dynamic binding occurs during runtime
* `private`, `final`, and `static` methods and variables use static binding and are bound by the compiler while virtual methods are bound during runtime based upon the runtime object
* Static binding uses type / class information while dynamic binding uses the object to resolve binding
* Overloaded methods are bound using static binding while overridden methods are bound using dynamic binding

## Static Binding Example

```java
public class StaticBindingTest {
 
    public static void main(String args[])  {
       Collection c = new HashSet();
       StaticBindingTest et = new StaticBindingTest();
       et.sort(c);
     
    }
   
    //overloaded method takes Collection argument
    public Collection sort(Collection c){
        System.out.println("Inside Collection sort method");
        return c;
    }
 
   
   //another overloaded method which takes HashSet argument which is sub class
    public Collection sort(HashSet hs){
        System.out.println("Inside HashSet sort method");
        return hs;
    }
     
}

/**
Output:
Inside Collection sort method
*/
```

* overloaded `sort` method - one accepts `Collection` while the other accepts `HashSet`
* Called the `sort` method with a `HashSet` object that is referenced with the `Collection` type
* Method call was bound at compile time based on type of variable, which is `Collection`

## Dynamic Binding Example

```java
public class DynamicBindingTest {

    public static void main(String args[]) {
        Vehicle vehicle = new Car(); //here Type is vehicle but object will be Car
        vehicle.start();       //Car's start called because start() is overridden method
    }
}

class Vehicle {

    public void start() {
        System.out.println("Inside start method of Vehicle");
    }
}

class Car extends Vehicle {

    @Override
    public void start() {
        System.out.println("Inside start method of Car");
    }
}

/**
Output:
Inside start method of Car
*/
```

* `Car` extends `Vehicle` and overrides the `start` method
* The `start` method calls the method from `Car` subclass because object referenced by `Vehicle` type is the `Car` object
* Resolution happens only at runtime because objects are created during runtime
* Dynamic binding is slower than static binding because it occurs at runtime
