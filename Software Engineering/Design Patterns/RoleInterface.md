# [RoleInterface](https://martinfowler.com/bliki/RoleInterface.html)

* A role interface is defined by looking at a specific interaction between suppliers and consumers
* A supplier component will implement several role interfaces, one for each pattern of interaction
* A `HeaderInterface` is an explicit interface that mimcs the implicit public interface of a class
  * Take all the public methods of a class and declare them in an interface
  * Can then supply alternative implementations of the class
  * `RoleInterface`s are the opposite of `HeaderInterface`s
* Example is a network of activities that are organized in a DAG
  * Each activity has a duration, which means that that the earliest start time of any activity can be calculated given the finish times of all of the activities predecessors
* A header interface would be an `Activity` and would mirrors the public methods on the `Activity` class

```java
public interface Activity {
  Instant earliestStart();
  Instant earliestFinish();
  Instant latestFinish();
  Instant latestStart();
}

class ActivityImpl implements Activity {
  List<Activity> predecessors();
  List<Activity> successors();
}
```

* With role interfaces, look at how the collaborating objects are used
  * A successor is only used for its latest start value and a predecessor is only used for its earliest finish value
  * So create a `Successor` and `Predecessor` interface with the associated values
  * And then instead of two `List<Activity>`, there's a `List<Predecessor>` and a `List<Successor>`
* The strength of a role interface is that it clearly communicates the actual collaboration between an activity and its successors
  * Often a class doesn't use all the methods of a class, so it's good to show which ones are actually needed
  * A header interface forces you to implement every method, even if you're not going to need them, but a role interface only needs to implement exactly what's needed
