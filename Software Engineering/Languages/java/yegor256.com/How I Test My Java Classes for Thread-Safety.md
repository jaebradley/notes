# [How I Test My Java Classes for Thread-Safety](https://www.yegor256.com/2018/03/27/how-to-test-thread-safety.html)
* Example program: an in-memory bookshelf that stores books (by ID) in a `ConcurrentHashMap`
```java
class Books {
  final Map<Integer, String> map =
    new ConcurrentHashMap<>();
  int add(String title) {
    final Integer next = this.map.size() + 1;
    this.map.put(next, title);
    return next;
  }
  String title(int id) {
    return this.map.get(id);
  }
}
```
```java
class BooksTest {
  @Test
  public void addsAndRetrieves() {
    Books books = new Books();
    int threads = 10;
    ExecutorService service =
      Executors.newFixedThreadPool(threads);
    Collection<Future<Integer>> futures =
      new ArrayList<>(threads);
    for (int t = 0; t < threads; ++t) {
      final String title = String.format("Book #%d", t);
      futures.add(service.submit(() -> books.add(title)));
    }
    Set<Integer> ids = new HashSet<>();
    for (Future<Integer> f : futures) {
      ids.add(f.get());
    }
    assertThat(ids.size(), equalTo(threads));
  }
}
```
* Pool of threads is created via an `ExecutorService`
* Each of the `Callable` objects will add a book to the bookshelf
* Each of these `Callable`s will be executed in some unpredictable order by some of the threads from the thread pool
* The results from these `Callable`s  is aggregated using the `Future` objects produced by submitting these `Callable`s to the `ExecutorService`
* While multi-threaded, in reality, it’s not guaranteed that multiple parallel threads are executing at any given time
  * This can be verified by using `Atomic*` objects to track overlapping thread execution like
```java
AtomicBoolean running = new AtomicBoolean();
AtomicInteger overlaps = new AtomicInteger();
Collection<Future<Integer>> futures =
  new ArrayList<>(threads);
for (int t = 0; t < threads; ++t) {
  final String title = String.format("Book #%d", t);
  futures.add(
    service.submit(
      () -> {
        if (running.get()) {
          overlaps.incrementAndGet();
        }
        running.set(true);
        int id = books.add(title);
        running.set(false);
        return id;
      }
    )
  );
}
```
* Author uses a `CountDownLatch` to synchronize all the submitted tasks to pause their execution until all tasks have been submitted
  * Only after all tasks have been submitted are the tasks unlatched and parallel execution of the critical path (adding books) occurs