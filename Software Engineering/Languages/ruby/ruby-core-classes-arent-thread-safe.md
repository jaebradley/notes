# [Ruby core classes aren't thread safe](https://www.jstorimer.com/pages/ruby-core-classes-arent-thread-safe)

* Imagine an `Inventory` class that takes a hash - it has a `decrease` method that decrements the specified hash key by 1

```ruby
inventory = Inventory.new(hats: 4000)
threads = Array.new

40.times do
  threads << Thread.new do
    100.times do
      inventory.decrease(:hats)
    end
  end
end

threads.each(&:join)
puts inventory[:hats]
```

* There are 40 threads that decrease inventory 100 times each - this means that the total `hats` should be 0 if `Inventory` is truly thread-safe
* `MRI` returns 0 but JRuby and RBX do not
  * This might suggest that core classes are thread-safe but they are actually not
* Concurrent mutations need to by synchronized
  * Multiple threads share variables and try to modify them at the same time (shared global or class variables)
* Use mutex (mutual exclusion) to synchronize access to certain sections of code such that only one thread can execute code that a mutex synchronizes
* MIL produced the correct result without the mutex because of the Global Interpreter Lock
  * If you're using multiple threads on a multi-core CPU, if code is running on MRI it will not run in parallel
  * MRI makes some exceptions for concurrent IO like waiting for a database response - MRI will allow another thread to run in parallel
  * JRuby and RUbinius don't have such a global lock, which is why they produced incorrect results before - this allows code to be truly parallel
