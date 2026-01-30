# 8.5 Parallelizing recursive algorithms

* Loops whose bodies contain nontrivial computation or perform potentially blocking I/O are frequently good candidates for parallelization (as long as iterations are independent)

```java
void processInParallel(Executor exec, List<Element> elements) {
  for (final Element e : elements) {
    exec.execute(new Runnable() {
      public void run() { process(e); }
    });
  }
}
```

* `processInParallel` returns as soon as all the tasks are queued to the `Executor` (vs. waiting for all of them to complete)

## Depth-first sequential recursion vs. parallelizable recursion

```java
public<T> void sequentialRecursive(List<Node<T>> nodes, Collection<T> results) {
  for (final Node<T> n : nodes) {
    results.add(n.someComputation());
    sequentialRecursive(n.getChildren(), results);
  }
}

public<T> void parallelRecursive(final Executor exec, List<Node<T>> nodes, Collection<T> results) {
  for (final Node<T> n : nodes) {
    exec.execute(new Runnable() {
      public void run() {
        results.add(n.someComputation());
      }
    });
    parallelRecursive(exec, n.getChildren(), results);
  }
}

ExecutorService exec = Executors.newCachedThreadPool();
Queue<T> resultQueue = new ConcurrentLinkedQueue<T>();
parallelRecursive(exec, nodes, resultQueue);
exec.shutdown();
exec.awaitTermination(Long.MAX_VALUE, TimeUnit.SECONDS);
// resultQueue should now be populated
```

* The depth-first traversal is still sequential in the `parallelRecursive` implementation
* The `someComputation` method call for each node is executed in parallel
