# [Understanding Java Streams](https://medium.com/swlh/understanding-java-streams-e0f2df12441f)

* Declarative paradigm: Streams are written specifying what has to be done, but not how
* Lazily-evaluated: Until a terminal operation is called, the stream won't be doing anything, will just have defined what the pipeline is doing

## Phases of a Stream

* Split, Apply, and Combine phases
* Split phase is data collected from a collection, converting a data source to a Stream in order to process the data
* Apply phase occurs where every operation in the pipeline is applied to each element in the sequence (intermediate operations)
* Combine phase is completion with a terminal operation and the results are "materialized" into a new collection

## Stream internals

* Stream operations are stored internally using a `LinkedList` where each stage gets assigned a bitmap
  * There are 4 bits, `SIZED` represents the first bit, `DISTINCT` represents the second bit, `SORTED` represents the third bit, and `ORDERED` represents the final bit
    * `ORDERED` means whether the order in which elements are streamed should be preserved on collection
  * `1010` would capture `SIZED` and `SORTED`
* `map` will clear `SORTED` and `DISTINCT` because data may have been changed, but will preserve the `SIZED` flag as the size of the stream will never change using `map`
* `filter` will clear the `SIZED` flag since the size of the stream may have changed, but will preserve the `SORTED` and `DISTINCT` flags
* Each operation combines the flags from the previous stage with its own flags, generating a new set of flags
* Imagine a `Set` of `String`s, that are streamed and have `distinct` called on themn at some point in the streaming operations - the stream will skip the `distinct` stage since the `DISTINCT` flag is already set

## Execution

* Stateless operations are `filter`, `map`, `flatMap` etc
* Stateful operations are `sorted`, `limit`, `distinct`
* If a Stream contains stateful operations, the pipeline is divided into sections using the stateful operations as delimiters

### Stateless Pipelines

* Imagine a `List` of `Employees` that are `filter`ed and then `map`ped before being recollected into a `List`
* Not every `Employee` is first filtered before being mapped - when logging is added, the first `Employee` is filtered and mapped, and then the next `Employee` is filtered and mapped, etc
* So in this case, Streams are more like "pipeline"s, where each pipeline is responsible for an element in the Stream

### Stateful Pipelines

* Stateful operations buffer all the elements until it reaches the last element and then it emits a result
* From previous example of a `List` of `Employees`, imagine `filter`ing, then `sorted`, then `map`ping
  * In this case, all the `filter`ing happens, then the sorting, then the `map`ping
  * This is because the stateful operation (`sorted`) divides the pipelines into two sections
  * `sorted` cannot emit a result until all elements have been filtered, so it buffers them before emitting any result to the next stage (`map`)

### Parallel Streams

* `trySplit` method splits the collection into chunks that could be processed by different threads
  * So each thread owns a `filter` / `map`  pipeline (for example)
* Instead of having one single set of linked operations, there are multiple copies of linked operations and each thread applies these operations to the chunk of elements that it's responsible for
* Once all results are completed by each thread, each result gets merged to produce one single and final result
* Uses the common `ForkJoinPool`

## Short-circuiting Terminal Operations

* Operations like `limit`, `findFirst`, `findAny`, etc. can stop searching once they have found what they're looking for
