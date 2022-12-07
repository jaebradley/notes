# 6.9.2 Log-Based Recovery

* Write-ahead logging - a log data structure on disk that stores information like 
  * the transaction name
  * data item identifier / name to write
  * data item prior to write
  * data item after the write
* Before a write starts, the log record associated with the write is written to the log file
* Performance penalty for writing to disk, as well as increasing the amount of disk storage necessary
* The log can be used to undo or redo a transaction depending on the failure (like a system failure) or action (transaction is aborted)
  * The undo operaton restores the value of all data updated by the transaction to the old values (values prior to write operation)
  * The redo operation restores the value of all data updated by the transaction to the new values
  * These operations must be idempotent (multiple executions must have the same result as a single execution)
* If a transaction is aborted, can restore previous state of data by executing undo
* For a system failure, need to undo a transaction if the log contains the transaction start record but does not contain a transaction commit record
  * Transaction would need to be redone if log contains the start and commit records
