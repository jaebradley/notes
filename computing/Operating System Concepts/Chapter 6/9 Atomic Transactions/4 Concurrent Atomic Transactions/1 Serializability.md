# 6.9.4.1 Serializability

* Can imagine two transactions that read and write data items A and B
* Serial schedule occurs when each transaction is executed atomically
  * T0 executes
    * read A
    * write A
    * read B
    * write B
  * T1 executes
    * read A
    * write A
    * read B
    * write B
* Two consecutive operations across two different transactions conflict if they both try to access the same data item and at least one of them is a write operation
* Non-serial schedule can produce conflicting operations
  * T0 executes
   * read A
   * write A (conflicts with T1 read A)
  * T1 executes
   * read A (conflicts with T0 write A)
   * write A
  * T0 executes
   * read B
   * write B
  * T1 executes
   * read B
   * write B
* If two consecutive operations of different transactions do not conflict, then we can swap the order of the operations to produce a new schedule
* If a schedule can be swapped to a serial schedule via a series of non-conflicting swap operations, the schedule is conflict serializable
   
