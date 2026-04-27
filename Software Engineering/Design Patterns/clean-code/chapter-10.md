# Chapter 10

* If we cannot derive a concise name for a class, then it's likely to be too large
* The more ambiguous the class name, the more likely it has too many responsibilities
  * Things like `Processor`, `Manager`, or `Super` hint at aggregations of responsibilities
* Things like `if` / `and` / `but` / `or` also indicate that the class does too many things
* Single Responsibility Principle also indicates that a single class has one responsibility to change
* A system with small classes has no more moving parts than a system with a few large classes
* The primary goal in managing such complexity is to organize it so that a developer knows where to look to find things and need only understand the directly affected complexity at any given time
  * A system with larger, multipurpose classes always hampers us by insisting we wade through lots of things we don't need to know right now
* In general, the more variables a method manipulates the more cohesive the method is to its class

## Organizing For Change

```java
public class Sql {
  public String create()
  public String insert(Object[] fields)
  public String selectAll()
  public String sselect(Column column, String pattern)

  private String selectWithCriteria(String criteria)
}
```

* The Sql class must change when a new type of statement is added
* Thus it's open to changes
* If there was instead a `CreateSql`, a `SelectSql`, an `InsertSql`, etc. classes when `update` needs to be added, none of the existing classes need to change
* Additionally, the code in each class becomes much smaller and simpler and also, easier to test
* This pattern also illustrates the Open-Closed principle where classes should be open for extension but closed for modification
* With the different subclasses, the `Sql` class is open to allow new via a new subclass, but each concrete class is "closed" (i.e. does not have to make any modifications)

## Isolating From Change

* If there's a `Portfolio` class that depends on some third-party API (like a `TokyoStockExchange` class), instead of designing `Portfolio` to take a specific `TokyoStockExchange` instance, create a `StockExchange` `interface`
* Then have the `TokyoStockExchange` class implement this interface and then have `Portfolio` take a `StockExchange` interface as a constructor argument
* When testing, can just implement a `StockExchange` interface that returns exactly the data we want to test without having to instantiate a `TokyoStockExchange` class explicitly
* This is the Dependency Inversion Principle, which states that classes should depend on abstractions and not concrete details
