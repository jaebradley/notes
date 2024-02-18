# Chapter 4: Composing Objects

## Designing a thread-safe class

* The state of an object with `n` primitive fields is just the `n`-tuple of its field values
* If the object has fields that are references to other objects its state will encompass fields from the referenced objects as well
  * For example, the state of a `LinkedList` includes the state of all the link node objects belonging to the list

### Gathering synchronization requirements

* By using final fields wherever practical, you make it simpler to analyze the possible states an object can be in
* Operation may have postconditions that identify certain state transitions as invalid
  * In the `Counter` example, if the current state is `17`, the only valid next state is `18`
  * When the next state is derived from the current state, the operation is necessarily a compound action
* If an operaton has invalid state transitions, it must be made atomic
* Multivariable invariants like the variables in the `NumberRange` class must fetch / update all related variables in a single atomic operation
  * You cannot update one variable, release it, and reacquire the lock and then update the other variables since this could involve leaving the object in an invalid state when the lock was released
  * When multiple variables participate in an invariant, the lock that guards them must be held for the duration of any operation that accesses the related variables

## Instance confinement

* Even if an object is not thread-safe, can still let it be used safely in a multithreaded program. For example, ensuring it can only be accessed by a single thread (thread confinement) or that all access to it is properly guarded by a lock
* Encapsulating data within an object confines access to the data to the object's methods, making it easier to ensure that the data is always accessed with the appropriate lock held - much easier to analyze and understand than if the object were accessible to the entire program

```java
@ThreadSafe
public class PersonSet {
  @GuardedBy("this")
  private final Set<Person> mySet = new HashSet<Person>();

  public synchronized void addPerson(Person p) {
    mySet.add(p);
  }
  
  public synchronized boolean containsPerson(Person p) {
    return mySet.contains(p);
  }
}
```
* Because `mySet` is `private` and not allowed to escape, the `HashSet` is confined to the `PersonSet`
* The only code paths that can access `mySet` are `addPerson` and `containsPerson`, and each of these acquiare the lock on the `PersonSet`, making `PersonSet` thread-safe even though the underlying `HashSet` is not

## Delegating thread safety

* For the previous counter example, since the only state of the class was the `AtomicLong`, the class itself was thread-safe because `AtomicLong` was thread-safe and requires no additional validity constraints on its state - the class _delegates_ its thread safety responsibilities to the `AtomicLong`

### Example: vehicle tracker using delegation

* Previous example used a vehicle tracker that wrapped ("monitored") a `HashMap` of `String`s to `Point`s
  * These `Point`s were mutable and needed to be copied when returning data from the vehicle tracker class
* Next iteration uses an immutable `Point` which does not need to be copied when returned
* Next iteration also uses a `ConcurrentHashMap` where all the keys and values of the `Map` are immutable
* The delegating class also creates an immutable copy of the mapping of locations

```java
@ThreadSafe
public class DelegatingVehicleTracker {
  private final ConcurrentMap<String, Point> locations;
  private final Map<String, Point> unmodifiableMap;

  public DelegatingVehicleTracker(Map<String, Point> points) {
    locations = new ConcurrentHashMap<String, Point>(points);
    unmodifiableMap = Collections.unmodifiableMap(locations);
  }

  public Map<String, Point> getLocations() {
    return unmodifiableMap;
  }

  public Point getLocation(String id) {
    return locations.get(id);
  }

  public void setLocation(String id, int x, int y) {
    if (locations.replace(id, new Point(x, y)) == null) {
      throw new IllegalArgumentException();
    }
  }
}
```
* Because `Point` is immutable, `getLocations` doesn't break encapsulation because it does not publish a reference to mutable state that is not thread-safe
* If thread A calls `getLocations` and thread B later modifies the location of some of the points, those are reflected in the `Map` returned to thread A
  * If a live copy is not preferred, can do something like `Collections.unmodifiableMap(new HashMap<String, Point>(locations));`

### Independent state variables

* Can delegate thread safety to more than one underlying state variable as long as the underlying state variables are independent meaning that the composite class does not impose any invariants (sets of assertions that must always hold true during the life of an object for the program to be valid) involving the multiple state variables
* Example is a graphical component that allows clients to register listeners for mouse and keystroke events
  * But since each set of listeners is independent of the other, the graphical component class can delegate its thread safety obligations to two different thread-safe lists

### When delegation fails

* Example is a `NumberRange` class that uses two `AtomicInteger`s to manage its state with a constraint that the first number must be less than or equal to the second

```java
public class NumberRange {
  private final AtomicInteger lower = new AtomicInteger(0);
  private final AtomicInteger upper = new AtomicInteger(0);

  // Unsafe check-then-act
  public void setLower(int i) {
    if (i > upper.get())
      throw new IllegalArgumentException();

    lower.set(i);
  }
  
  // similar logic for setUpper
}
```

* `setLocation` are check-then-act sequences but they are not atomic
* If the number range holds (0, 10) and one thread calls `setLower(5)` while another thread calls `setUpper(4)` with some unlucky timing, both will pass the checks in the setters and both modifications will be applied
  * However, the range (5, 4) is an invalid state
  * Because the underlying state variables `lower` and `upper` are not independent, `NumberRange` cannot delegate thread safety to its thread-safe state variables

## Adding functionality to existing thread-safe classes

### Client-side locking

* Extend the functionality of the class without extending the class itself by placing extension code in a helper class

```java
public class ListHelper<E> {
  public List<E> list = Collections.synchronizedList(ne ArrayList<E>());

  public synchronized boolean putIfAbsent(E x) {
    boolean absent = !list.contains(x);
    if (absent)
      list.add(x);
    return absent;
  }
}
```

* This class is problematic because the lock on `putIfAbsent` is not the same lock on the underlying `List`
  * This matters because `putIfAbsent` is not atomic relative to other operations on the `List` so there is no guarantee that another thread won't modify the list while `putIfAbsent` is executing
* Have to use the same lock that the `List` uses which entails guarding client code for some object X with the lock that X uses to guard its own state
* The documentation for `Vector` states that they support client-side locking using the intrinsic lock for the `Vector`
  * So can implement `putIfAbsent` successfully by wrapping the previous logic within the method in a `synchronized` block
* This approach is fragile because it entails putting locking code for class C into classes that are totally unrelated to class C
  * This couples the behavior of the derived class to the implementation of the base class, violating encapsulation of the synchronization policy

### Composition

* Class implments the `List` interface, taking a `List` as an argument, implementing the `putIfAbsent` on the class and delegating all other methods to the underlying `List` instance
* The extra intrinsic locking that the wrapper class implements may result in a small performance penalty, and is less fragile than attemping to mimic the locking strategy of another object
