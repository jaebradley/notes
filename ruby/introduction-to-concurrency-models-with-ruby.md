# [Introduction to Concurrency Models with Ruby Part 1](https://engineering.universe.com/introduction-to-concurrency-models-with-ruby-part-i-550d0dbb970)

* Difference between concurrency and parallelism
  * Concurrency is having a single person juggle many balls with one hand - person is only catching / throwing one ball at a time
  * Parallelism is having multiple people juggle their own sets of balls simultaneously

## Processes

* Running multiple processes is not about concurrency but about parallelism

```ruby
# single-threaded example
# takes ~500ms and utilizes 1 CPU
range = 0...10_000_000
number = 8_888_888
puts range.to_a.index(number)

# multiple parallel processes
# takes ~300ms and utilizes more than 1 CPU
range1 = 0...5_000_000
range2 = 5_000_000...10_000_000
number = 8_888_888
puts "Parent #{Process.pid}"
fork { puts "Child1 #{Process.pid}: #{range1.to_a.index(number)}" }
fork { puts "Child2 #{Process.pid}: #{range2.to_a.index(number)}" }
Process.wait 
```

### Pros

* `fork` method creates a child process and parent process waits for all child processes to finish using `Process.wait`
* Processes don't share memory so you can't mutate data from one process to another
* Processes are only way to utilize more than a single core since there is a Global Interpreter Lock
* Forking child processes helps avoid unwanted memory leaks - once process finishes, it releases all resources

### Cons

* Processes use a lot of memory - running hundreds of processes may be a problem
* Processes are slow to create and destroy
* Need to monitor orphan processes (child processes whose parents have finished or terminated) or zombie processes (child processes which complete execution but still occupy space in process table)

## Threads

* Only one thread can be executing at any given time within a single process, even with multiple CPUs (due to GIL)
* With GIL no need to make Ruby data structures thread-safe
  * Though GIL doesn't guaratee Ruby code is thread-safe

```ruby
@executed = false
def ensure_executed
  unless @executed
    puts "executing!"
    @executed = true
  end
end
threads = 10.times.map{ Thread.new { ensure_executed } }
threads.each(&:join)

# prints "executing!" twice
```

* 10 threads are created, which execute `ensure_executed`
* `join` is called on each of them so main thread waits until all other threads are finished
* `executing!` is printed twice because threads share same `@executed` variable
* read and set operations are not atomic meaning that once a value is read it could be changed by another thread before value is set
* Thread releases GIL when it hits blocking I/O operations like HTTP requests, DB queries, writing / reading from disk and `sleep`

```ruby
# takes a little over 1 second
threads = 10.times.map do |i|
  Thread.new { sleep 1 }
end
threads.each(&:join)
```

* All 10 threads slept for 1 second - when one thread hit `sleep` it passed execution to another thread without blocking GIL

### Pros

* Uses less memory than processes - possible to create thousands of threads
* Threads are useful when there are slow blocking I/O operations
* Can access the memory area from other threads if necessary

### Cons

* Requires very careful synchronization to avoid race-conditions, usually by using locking primitives, which may lead to deadlocks
  * Difficult to write, test, and debug thread-safe code
* Code must be thread-safe including any dependencies

