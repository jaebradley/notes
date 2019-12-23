# [Three Ways to Make Your Ruby Object Thead-Safe](https://blog.arkency.com/3-ways-to-make-your-ruby-object-thread-safe/)

* Freeze your object / ensure it does not have any state (instance variables)
  * Inside the `initialize` method, can call `freeze`
  * If any instance variables are added in future, they will throw an exception (`can't modify frozen X`)
* When making an object thread-safe, need to ensure that all sub-dependencies / objects are _also_ thread-safe (so really a graph of thread-safe objects)
* Can use `ThreadLocalVar` from `concurrent-ruby` to create variables that have different variables across threads
  * "Often used as an instance variable in objects which must maintain different state for different threads"
* Can also use mutexes to protect state
  * Object state is shared between all threads, but access is limited to single thread at any given time

```ruby
def initialize
  @semaphore = Mutex.new
  @subscribers = []
end

def add(subscriber)
  @semaphore.synchronize do
    @subscribers += [subscriber]
  end
end
```

* Can also use thread-safe data structures like `Concurrent::Array` which will lock the object for every method call, ensuring that only a single thread can be reading / accessessing thread at any given time
* If creating new instance whenever using, no need to worry about thread-safety
  * Need to apply thread-safety strategies to object's dependency tree (e.g. if creating a new object instance every time, create new object instances for every dependency)
* Many classes are not thread-safe in Ruby by default because the author did not expect them to be used from multiple threads
