# [Dependency Inversion Principle: How Google Developers write code](https://paigeshin1991.medium.com/dependency-inversion-principle-how-google-developers-write-code-f6cbd3b530a6)

* High-level modules should not depend on low-level modules. Both should depend on abstractions.

## What is Abstraction?

```java
class Benz  {

    public void drive() {
                
    }

}

class CarUtil {

    public static void drive(Benz benz) {
        benz.drive();
    }

}
```

* `CarUtil` is tightly coupled to the `Benz` class as changes to the `drive` method will impact the `CarUtil#drive` method
* Can remove the tight coupling by creating a `Car` interface with a `drive` method where the `CarUtil#drive` method takes a `Car` interface (instead of the `Benz` class like before)
  * `Benz` class also implements `Car` interface
* Both `CarUtil` and `Benz` depend on abstractions (in this case, the `Car` interface)
