# [Design Hit Counter](http://blog.gainlo.co/index.php/2016/09/12/dropbox-interview-design-hit-counter/)

* Build count of all website visitors over past 1 minute
* Simple case of no concurrent requests and single machine
  * Insert each visit into database with timestamp
  * Count over database within timestamp range
* Use a queue to keep track of count every second
  * Every second, remove the earliest (i.e. last) sum of counts from the queue (and total sum), and add the sum of counts from the previous sum (and also add to total sum)
  * This way, space is only limited to a queue that is 60 items long, where each item is an integer
* Concurrent requests can be countered by using a lock to protect the list, but locking can be expecnsive and lead to bottlenecking if there are a lot of concurrent requests
* Distribute counter
  * Distribute requests equally across many machines
  * Each machine is responsible for storing their 60-second sums
  * Global sum takes each machine's sums and totals them up

