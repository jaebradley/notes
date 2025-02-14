# [What is an uninterruptible process?](https://stackoverflow.com/questions/223644/what-is-an-uninterruptible-process)
* Uninterruptible process is a process that happens to be in a system call (kernel function) and cannot be interrupted by a signal.
* A process can’t be killed in kernel mode because it could potentially corrupt kernel structures used by all the other processes in the same machine

## Classic example of an *interruptible* system call: `read`
* `read` can take a long time since it involves spinning up a hard drive
* During this time the process is sleeping, blocked on the hardware
* Kernel marks process as sleeping and calls the scheduler to switch to another process
* While the process is sleeping, the process can receive a Unix asynchronous signal
* Interruptible kernel tasks can be woken up from sleep via signals
  * Check for any expected signals, and return from system call
* Process is still only killable after return to user space
* Some system calls are not allowed to be interruptible in this way
  * These tasks are not expecting to be woken up by anything other than whatever it is waiting for
  * This is because the system call cannot easily be restarted, or because the system call is expected to be atomic
* If these system calls stall, the process can indefinitely remain in this unkillable state

