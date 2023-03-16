# [Working with Threads in Ruby](https://medium.com/@kopilov.vlad/working-with-thread-in-ruby-948cd7e5f1a8)

* Threads are useful for I/O operations
  * HTTP requests, database commands
* Typical example is making many HTTP requests

```ruby
urls = ['first', 'second', 'third']
urls.each do |url|
  response = open(url)
end
```

* Getting the response from each URL is synchronous and blocking
  * If there were many URLs it would take a long time
  * Can use threads to speed up execution

```ruby
urls = ['first', 'second', 'third']
urls.each do |url|
  threads << Thread.new(url) do |t|
    response = open(t)
  end
end

# run all threads
threads.each{ |thread| thread.join }
```

* Implementing a thread pool
  * Thread pool has a queue of tasks and an array of threads
  * Each thread takes a task from the queue, executes some logic and then takes another task
