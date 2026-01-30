# 10.1.2 Dynamic lock order deadlocks

```java
public void transferMoney(Account fromAccount, Account toAccount, DollarAmount amount) {
  synchronized (fromAccount) {
    syncrhonized (toAccount) {
      // process transaction
    }
  }
}
```

* Lock order depends on order of arguments passed to `transferMoney`
* Deadlock scenario
  * Thread 1: `transferMoney(accountA, accountB, someDollarAmount)`
  * Thread 2: `transferMoney(accountB, accountA, someOtherDollarAmount)`
  * Thread 1 acquires lock on `accountA` and waits for lock on `accountB`
  * Thread 2 acquires lock on `accountB` and waits for lock on `accountA`
* Look for nested lock acquisitions
* Induce an ordering on the locks and acquire them according to induced ordering consistently throughout the application
* `System.identityHashCode` returns value returned by `Object.hashCode` and can be used to induce lock ordering
  * If two objects have the same hash code, use a third lock
  * Third "tie-breaking" lock is acquired before either of the account locks
  * Ensures that only one thread at a time performs task of acquiring any two arbitrary accounts
