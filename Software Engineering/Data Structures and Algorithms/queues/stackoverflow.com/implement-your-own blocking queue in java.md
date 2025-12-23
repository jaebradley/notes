# [implement-your-own blocking queue in java](https://stackoverflow.com/questions/20110013/implement-your-own-blocking-queue-in-java)
* `put` and `get` are both `synchronized` methods, which means only one thread can enter them at a time
* So Thread A can enter `put` and some other Thread B can enter and start executing instructions in the `get` method before Thread A has exited the `put` method
* Using re-entrant locks, there's no need for double-checking as the lock is shared between `put` and `get`
* Only one thread can enter one of these methods at a time
* Unlike synchronized methods which end up creating different monitors, only the threads waiting because the queue is full will be notified when there's more space
  * Same goes for threads waiting because the queue is empty
  * Ultimately leads to better CPU utilization
 
````java
Condition isFullCondition;
Condition isEmptyCondition;
Lock lock;

public BQueue() {
    this(Integer.MAX_VALUE);
}

public BQueue(int limit) {
    this.limit = limit;
    lock = new ReentrantLock();
    isFullCondition = lock.newCondition();
    isEmptyCondition = lock.newCondition();
}

public void put (T t) {
    lock.lock();
    try {
       while (isFull()) {
            try {
                isFullCondition.await();
            } catch (InterruptedException ex) {}
        }
        q.add(t);
        isEmptyCondition.signalAll();
    } finally {
        lock.unlock();
    }
 }

public T get() {
    T t = null;
    lock.lock();
    try {
        while (isEmpty()) {
            try {
                isEmptyCondition.await();
            } catch (InterruptedException ex) {}
        }
        t = q.poll();
        isFullCondition.signalAll();
    } finally { 
        lock.unlock();
    }
    return t;
}
```
