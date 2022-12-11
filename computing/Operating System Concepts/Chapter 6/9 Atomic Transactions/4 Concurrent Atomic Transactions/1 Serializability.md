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
