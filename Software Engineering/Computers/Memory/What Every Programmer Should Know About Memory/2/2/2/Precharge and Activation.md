# Precharge and Activation

* Before a new RAS signal can be sent, the currently latched row must be deactivated and the new row must be precharged
* The precharge signal has no dedicated line - some implementations issue it by lowering the Write Enable and RAS line simultaneously
* It takes the Row Precharge time / cycles until the row can be selected
  * This time overlaps with the memory transfer, which is good, but the precharge time takes longer than the memory transfer time, so the next RAS signal needs to wait some number of cycles
* One additional timing constraint is that SDRAM modules need time after a RAS signal before it can precharge another row
  * This time can be pretty high (2 to 3 times the Row Precharge time)
  * This can be a problem if after a RAS signal, only one CAS signal follows, and the data transfer is finished in a few cycles


