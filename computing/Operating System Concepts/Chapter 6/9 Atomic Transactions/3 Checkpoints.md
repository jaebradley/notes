# 6.9.3 Checkpoints

* After system failure, the transaction log can be reapplied
  * Since the transaction log is idempotent there is no harm in reapplying the operations (will just delay recovery)
  * Most of the transactions have updated the data in the data store, so only a small subset of the transactions ever need to be applied
* To avoid having to reapply all transactions, the checkpoint concept is used
  * Write all log records in volatile storage / main memory to disk
  * Write a checkpoint log record to disk
* This checkpoint log record streamlines the recovery process
  * Any transaction committed before the checkpoint is known to have been written to stable storage
  * So when recovering from system failure, these transactions can be skipped
* Previous recovery algorithm can be simplified to
  * Look for most recent checkpoint record
  * Find all the transactions that started more recently than that checkpoint record
  * If the transaction has a commit record, redo the transaction
  * If the transaction does not have a commit record, undo the transaction
