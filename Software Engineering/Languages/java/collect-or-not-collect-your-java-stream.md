$ [Collect Or Not Collect Your Java Stream](https://medium.com/hackernoon/collect-or-not-collect-your-java-stream-733821507ce)

* WHen working with streams, there is no actual way to know the size of the stream without materializing it, which at the same time implies that we could actually never find the size of it

```java
Stream<Integer> numbers = Stream.iterate(0, n -> n + 1);
numbers.size();
```

* If we try to get the `.size` of it, the program will hang forever since it's an unbounded stream
* Don't materialize streams until the end of the processing chain AND the materialization should only happen if we are certain that our stream is bounded at this point
* By passing around streams (from functions that receive and return Streams) computation stages can each perform their computation on the stream and nothing gets materialized until the final computation
  * Unless grouping, in which case the stream must be materialized

