# 6.9.4.3 Timestamp-Based Protocols

* Each transaction has some unique timestamp associated with it
* More recent transactions will have a larger timestamp value (can use a monotonically-increasing counter, for example)
* These transaction timestamp determine the serializability order
  * If T1 occurs before T2, then the schedule / execution sequence of operations must be equivalent to a serial schedule in which transaction T1 appears before transaction T2
* Each data item has two timestamp values - a write and read value
  * These values represent the largest / most-recent timestamp of the last successfully executed write / read
* For transaction read operations:
  * If the transaction timestamp is before the latest write timestamp value for that item, the read operation is rejected and the transaction is rolled back
  * If the transaction timestamp is greater than or equal to the write timestamp value for that item, then the read operation is executed and the read timestamp for that item is set to the maximum of the current transaction and the existing read timetasmp value
* For transaction write operations:
  * If the transaction timestamp is less than the last read timestamp value for the item, the write operation is rejected and the transaction is rolled back
  * if the transaction timestamp is less than the last write timestamp value for the item, the write operation is rejected and the the transaction is rolled back
* A transaction that is rolled back is assigned a new timestamp and is restarted
* The timestamp protocol ensures conflict serializability, since conflicting operations are processed in timestamp order
* The timestamp protocol also ensures freedom from deadlock, because no transaction waits
