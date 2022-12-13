# 7.3 Handling abnormal thread termination

* One of the few times when you might want to consider catching `RuntimeException` is when you are calling unknown, untrusted code through an abstraction such as `Runnable`

## Example

* If a worker thread task in a thread pool throws an unchecked exception, let the thread die, but send notification that the thread has died so that some recovery may occur

```java
public void run() {
  Throwable thrown = null;
  try {
    while (!isInterrupted()) {
      runTask(getTaskFromQueue());
    }
  } catch (Throwable e) {
    thrown = e;
  } finally {
    exitThread(this, thrown);
  }
}
```
