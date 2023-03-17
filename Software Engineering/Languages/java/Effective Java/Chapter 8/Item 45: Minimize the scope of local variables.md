# Item 45: Minimize the scope of local variables

```java
for (int i = 0, n = expensiveComputation(); i < n; i++) {
  // do something
}
```

* The second variable, `n`, is used to store the limit of the first, avoiding the cost of a redundant computation on every iteration
  * Use this idiom if the loop test involves a method invocation that is guaranteed to return the same result on each iteration
