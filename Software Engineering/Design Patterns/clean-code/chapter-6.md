# Objects And Data Structures

```java
public class Point {
  public double x;
  public double y;
}

public interface Point {
  double getX();
  double getY();
  void setCartesian(double x, double y);
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```
* Benefit of second interface is that it's not clear whether the implementation uses rectangular or polar coordinates as it can support both
  * It also enforces setting data in a single method even though reading individual properties is allowed
* Meanwhile the first example is clearly implemented in rectangular coordinates and those coordinates are manipulated independently
  * Thus, the implementation of _how_ to change those coordinates is exposed because the user of the class would have to change those coordinates and handle changing the rectangular values of a `Point`
* Benefit of a class is to allow users to manipulate the essence of the data through an interface without having to know the essence of the data

```java
public interface Vehicle {
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}

public interface Vehicle {
  double getPercentFuelRemaining();
}
```

* The first interface exposes data values, while the second expresses the data in abstract terms / what the data represents / it's essence

## Data/Object Anti-Symmetry

* Objects hide their data behind abstractions and expose functions that operate on that data
* Data structures expose their data and have no meaningful functions

```java
public class Geometry {
  public double area(Object shape) throws NoSuchShapeException {
    if (shape instanceof Square) {
      // do something
    } else if (shape instanceof Rectangle) {

    } else if (shape instanceof Circle) {

    }

    throw new NoSuchShapeException();
  }
}

```

* If a `perimeter` function were added to the "procedural" `Geometry` class, the different shape clases (`Square`, `Rectangle`, `Circle`) would be unaffected
* On the other hand, if a new shape is added (e.g. `Triangle`) all functions in `Geometry` would need to be changed to deal with the new shape

```java
public class Square implements Shape {
  public double area() {

  }
}

public class Rectangle implements Shape {
  public double area() {

  }
}
```
* Procedural code makes it easy to add new functions without changing the existing data structures
  * Also it makes it hard to add new data structures as all functions must change
* OO code makes it easy to add new classes without changing existing functions
  * Also it makes it hard to add new functions because all classes must change

## Law Of Demeter

* A module should not know about the innards of the objects it manipulates
* A method `f` of a class `C` should only call the methods of `C`, an object created by `f`, an object passed as an argument to `f`, an object held in an instance variable of `C`
* `f` should not call methods of any of the objects returned by the allowed method (basically, avoid nested function calls)
* There is a `ctxt` object that is called to get the absolute path of a scratch file

```java
ctxt.getAbsolutePathOfScratchDirectoryOption();
ctxt.getScratchDirectoryOption().getAbsolutePath();
```

* Neither feels good - the first option could lead to an explosion of methods on the `ctxt` object while the second assumes `getScratchDirectoryOption` returns a data structure and not an object
* Since the path will be used to generate a `BufferedOutputStream` later on, the best approach is to have the `ctxt` object return this like ctxt.createScratchFileStream(fileName)`
  * `ctxt` hides its internals and the current method does not violate the Law of Demeter by preventing it from having to navigate through objects it shouldn't know about
