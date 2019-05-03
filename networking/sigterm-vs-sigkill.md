# [SIGTERM vs. SIGKILL](https://major.io/2010/03/18/sigterm-vs-sigkill/)

* Anytime `kill` is used on a process, it's sending a process a signal
* Standard C applications have a header file that handles certain signals
* `kill 2563` (`SIGTERM`) is sent to a process - that process can
  * Stop immediately
  * Stop after short delay after cleaning up resources
  * Keep running
* Most applications will clean up resources and stop, some may not
* `kill -9` sends `SIGKILL`
  * `SIGKILL` cannot be ignored by a process
  * process cannot catch the signal and act on it
  * Kernel may not be able to kill process

