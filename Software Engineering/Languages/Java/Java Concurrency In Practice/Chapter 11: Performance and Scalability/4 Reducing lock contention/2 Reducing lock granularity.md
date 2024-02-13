# 11.4.2 Reducing lock granularity

## Thought Experiment

* Only one lock for the entire application instead of a separate lock for each object
* Highly likely that many threads compete for the global lock, increasing contention
* If lock requests were distributed across a larger set of locks, less contention

## Lock Splitting

* Increased scalability splitting lock guarding more than one independent state variable
* If multiple locks guard different variables each lock will be requested less often and less contention
* Can also delegate to separate thread-safe data structures (like a thread-safe `Set` implementation in the example below) because each data structure would be guarded by a different lock 

## Example

```java
@ThreadSafe
public class ServerStatus {
  @GuardedBy("this") public final Set<String> users;
  @GuardedBy("this") public final Set<String> queries;

  public synchronized void addUser(String u) { users.add(u); }
  public synchronized void addQuery(String q) { queries.add(q); }
}

// Now with lock splitting
@ThreadSafe
public class ServerStatus {
  @GuardedBy("users") public final Set<String> users;
  @GuardedBy("queries") public final Set<String> queries;

  public synchronized void addUser(String u) {
    synchronized (users) {
      users.add(u);
    }
  }
  public synchronized void addQuery(String q) {
    synchronized (queries) {
      queries.add(q);
    }
  }
}
```
