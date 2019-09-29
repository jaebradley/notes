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

## Interview Notes

* Implementing such a counter came up in an interview
* Things to consider
  * When storing counters in an array, need to be cognizant of ensuring array does not take up a ton of memory (i.e. clearing out-dated values from the array)
  * When storing counts in array where each index of array is the counts from N minutes ago, use `push` and `pop` to keep the relative order of array elements
    * So when first count comes in 2 minutes after left count, all current counts need to move 2 indices over
    * Can avoid using `push` and `pop` by keeping a reference to the index that represents the last value
      * Each time need to `push` or `pop`, move the index representing last value over one index (making sure to wrap around to front)

```javascript
// First minute get count of 10
[x, 0, 0, 0, 10] 

// Second minute get count of 11
[11, x, 0, 0, 10]

// Third minute get count of 12
[11, 12, x, 0, 0, 10]

// Note how value before "x" (which represents last value) is the count from the latest minute
```

