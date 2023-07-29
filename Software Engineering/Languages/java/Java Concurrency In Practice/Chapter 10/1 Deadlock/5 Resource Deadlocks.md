# 10.1.5 Resource deadlocks

* Two resource pools (like connection pools for two different databases)
* Resource pools are usually implemented with semaphores to facilitate blocking when the pool is empty
* If a task needs to connect to two databases and the connections are not requested in the same order then two threads could deadlock waiting for a database connection to the other database
  * Thread A holds a connection to DB 1 and is waiting for a connection to DB 2
  * Thread B holds a connection to DB 2 and is waiting for a connection to DB 1
* Tasks that wait for the results of other tasks are the primary source of thread-starvation deadlock
