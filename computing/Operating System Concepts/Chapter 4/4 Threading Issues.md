# 4.4 Threading Issues

## 4.4.1 The `fork()` and `exec()` System Calls

* Some UNIX systems implement two versions of `fork()`, one that duplicates all threads in the process, and another that duplicates only the thread that invoked the `fork()` system call
* If a thread invokes the `exec()` system call, the pgoram specified in the parameter to `exec()` will replace the entire process - including all threads
