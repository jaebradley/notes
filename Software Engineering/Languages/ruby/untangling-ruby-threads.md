# [Untangling Ruby Threads](https://thoughtbot.com/blog/untangling-ruby-threads)

* Ruby has the concept of a GIL - a global interpreter lock
  * GIL only allows one thread to execute at a time, so no multi-threading
* Typically, ruby spends a lot of time not executing, but rather waiting for I/O (http requests to complete, database queries, file system operations)
  * ruby can spend time working while waiting

## Example

* Search service that calls many other services to aggregate results
* If called one at a time, the total wait time of any call to the service is the sum of calling each of the services one at a time
* Using threads decreases the total wait time to (hopefully) the length of the longest request

```ruby
def search
  services.map do |service| 
    service.search
  end
end

def threaded_search
  services.map do |service|
    Thread.new do
      service.search
    end
  end
end.map(&:value)
```

* By creating a new Thread, a new thread is created that is separate from the main thread's execution and passes block to new thread
  * Always executing in context of a thread
  * Main thread is Thread.main
  * Current thread is Thread.current
* Calling `value` causes main thread to wait for all child threads to complete by calling `join` method
  * Not calling `join` on threads will cause main thread to continue without waiting and exit before child threads finish (killing them)

