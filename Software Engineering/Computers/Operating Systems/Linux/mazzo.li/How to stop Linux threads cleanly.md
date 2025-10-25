# [How to stop Linux threads cleanly](https://mazzo.li/posts/stopping-linux-threads.html)
* Want to terminate a thread ensuring that memory is freed, locks are released, logs are flushed when doing so
  * Can shut down a thread without cleanup with `SIGKILL`

## (Quasi-)busy looping
```
while (true) {
  if (per_thread_stop) {
    break;
  }

  // perform work in this thread
}
```
* Sometimes not desirable in the case of foreign code that is not controlled, like a third-party library that does blocking network calls

 ## We need to talk about signals
 * Main way to interrupt execution of a thread without explicit coordination of the interrupted thread
 * Signals can arise because of some hardware exceptions or initiated by software
   * Example of software-initiated signals is shell sending `SIGINT` to the foreground process when pressing `CTRL` + `C`
 * Hardware initiated signals are generally handled immediately while software initiated signals are handled when a CPU is about to re-enter user mode after the kernel has done some work
   * In the case of a process-directed signal, the kernel picks a thread in the process to handle the signal without any guarantees about which thread is selected
 * Is the signal has been blocked by the receiving thread, it will wait to be handled until it is unblocked
 * If the signal has not been blocked, it may be ignored, handled in the "default" manner, or handled using some custom signal handler
* Is a thread is in a syscall (reading from a socket) and a signal needs to be handled, the syscall will return early with error code `EINTR` after the signal handler has run

## Thread cancellation, a false hope
* `pthread_cancel` sends a signal to the thread and the then the libc sets up a handler so that when the cancel signal is received, the thread winds down
* Since signals can arise in any code block, like while a lock is being held
```
lock()
// critical work
unlock()
```
* We want to avoid thread cancellation when we're holding resources (locks, memory, etc) where cleanup logic is not executed

## You cna't cleanly stop thread running code you don't control
* If you need to interrupt foreign code reliably, it is better to isolate it in its own process
* While foreign code might leak temporary files or other resources, the most relevant state would be cleaned up by the operating system when the process dies
* Can enable thread cancellation only at specific times
```
pthread_setcancelstate(PTHREAD_CANCEL_DISABLE);
while (true) {
  pthread_setcancelstate(PTHREAD_CANCEL_ENABLE);
  // syscall that might block indefinitely, like reading from a socket
  pthread_setcancelstate(PTHREAD_CANCEL_DISABLE);
  // perform some other work
}
```

## Homegrown thread cancellation
* Work with signals directly by picking some signal, like `SIGURS1` as the stopping signal, installing a handler which sets the stopping variable, and checking the various before doing blocking system calls
* However, there are some race conditions that can occur when signal handling this custom signal and also executing other system calls, like reading data from a socket

## Changing the sigmask atomically
* `select/poll/epoll_wait` have variants that take a sigmask argument
```
struct pollfd pollsock = {
  .fd = sock,
  .events = POLLIN,
};
if (ppoll(&pollsock, 1, nullptr, &usr1_unmasked) < 0) {
  if (errno == EINTR) {
    break;
  }
  die_syscall("ppoll");
}
ssize_t recvlen = recvfrom(sock, buffer.data(), buffer.size(), 0, nullptr, nullptr);
```
