# Item 30: Use enums instead of `int` constants

* To associated data with enum constants, declare instance fields and write a constructor that takes the data and stores it in the fields
* Enums are immutable by nature, so all fields should be final
* The fields can be public, but it's better to make them private and provide public accessors

```java
public enum Planet {
  private final double mass;

  Planet(double mass, ...) {
    this.mass = mass;
  }

  public double mass() { return this.mass; }
}
```
* `java.math.RoundingMode` is an enum that represents a roudning mode for decimal fractions
  * Used by `BigDecimal` but by making `RoundingMode` a top-level enum, the library designers encourage anybody who needs a rounding mode to reuse the enum leading to increased consistency across APIs
* `Operation` enum example that calculates a value based on "applying" an operation to two values

```java
public enum Operation {
  PLUS

  double apply(double x, double y) {
    switch (this) {
      case PLUS: return x + y;
    }

    throw new AssertionError("Unknown operation: " + this);
  }
}
```

* If you add a new enum constant but forget to add a corresponding case to the `switch` the enum will still compile but will fail at runtime
* declare an abstract `apply` method in the enum type and override it with a concrete method for each constant in a constant-specific class body (constant-specific method implementations)


```java
public enum Operation {
  PLUS { double apply(double x, double y) { return x + y; } },

  abstract double apply(double x, double y);
}
```

* Compiler will enforce `apply` method when a new enum is added
* Enums automatically have a `valueOf(String)` method that translates a constant's name into the constant itself
* If you override the `toString` method in the enum type, consider writing a `fromString` method to translate the custom string representation back to the corresponding enum
* Next example is a "payroll" calculation that uses an enum of the days of the week to calculate pay
  * Dangerous from a maintenance perspective because adding values to the enum will require checking the shared "pay" method to ensure that no case needs to be modified in order to calculate the correct amount
  * The idea is to force the end-user to pick an overtime pay strategy each time an enum constant is added
  * Move the overtime pay computation into a private nested enum and pass an instance of the strategy enum to the constructor of the public enum
  * The public enum then delegates the overtime pay calculation to the strategy enum
  * Consider the strategy enum pattern if multiple enum constants share common behaviors

  ```java
enum PayrollDay {
  MONDAY(PayType.WEEKDAY),
  SUNDAY(PayType.WEEKEND);

  private final PayType payType;
  PayrollDay(PayType payType) { this.payType = payType; }

  double pay(double hoursWorked, double payRate) {
    return payType.pay(hoursWorked, payRate);
  }

  private enum PayType {
    WEEKDAY {
      double overtimePay(double hours, double payRate) {
        // some implementation
      }
  }

  WEEKEND {
    double overtimePay(double hours, double payRate) {
      // some other implementation
    }
  }

  abstract double overtimePay(double hours, double payRate);

  double pay(double hoursWorked, double payRate) {
    double basePay = hoursWorked * payRate;
    return basePay + overtimePay(hoursWorked, payRate);
  }
}
  ```
