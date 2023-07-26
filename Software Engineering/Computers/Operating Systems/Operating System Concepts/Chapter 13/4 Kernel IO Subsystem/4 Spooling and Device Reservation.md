# 13.4.4 Spooling and Device Reservation

* Spool is a buffer that holds output for a device that cannot accept interleaved data stream
  * An example of such a device is a printer
* Applications may want to print their output concurrently, without having output mixed together
  * OS intercepts all output to the printer
  * Each application's output is spooled to a separate disk file
  * When application finishes printing, spooling system sequentially queues the matching spool file to the printer
  * Spooling is managed by system daemon process or an in-kernel thread
