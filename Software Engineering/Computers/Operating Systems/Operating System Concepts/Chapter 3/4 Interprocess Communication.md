# 3.4 Interprocess Communication

* In the shared-memory interprocess communication model, a region of memory that is shared by cooperating processes is established
  * Processes exchange information by reading and writing data to the shared region
  * Shared-memory is faster than message-passing as message-passing usually are implemented using system calls, and thus require kernel intervention
    * Shared-memory only needs system calls to establish shared-memory regions
* In the message-passing model, communication takes place by exchanging messages between cooperating processes
  * Message passing is useful for exchanging smaller amounts of data, because no conflicts need to be avoided

## 3.4.1 Shared-Memory Systems

* Typically, a shared-memory region resides in the address space of the process creating the shared-memory segment
* Other processes that wish to communicate using this shared-memory segment must attach it to their address space
  * Normally, the operating system prevents one process from accessing another process' memory - shared memory requires two or more processes agree to remove this restriction
* The processes are responsible for ensuring that they are not writing to the same location simulatenously

### Consumer / Producer processes

* Producer process produces information that is consumed by a Consumer process
* To allow producer and consumer processes to run concurrently, we must have available a buffer of items that can be filled by the producer and emptied by the consumer
  * If using a bounded buffer with a fixed buffer size, the consumer must wait if the buffer is empty and the producer must wait if the buffer is full
  * Implemented as a circular array with two pointers to the start of the buffer and the next free position in the buffer

## 3.4.2 Message-Passing Systems

### 3.4.2.1 Naming

* Processes that want to communicate must have a way to refer to each other
* Direct communication involves each process explicitly naming the recipient or sender of the message (like `send(receiving process, message)` - "send message to the receiving process")
* A direct communication link has the following properties
  * A link is associated with exactly two processes
  * There is only one link between each pair of processes
* A wrinkle to direct communication, that adds asymmetry for the receiver of a message is a scheme where the message sender must specify the receiver (like before), but the receiver will receive a message from any process
* With indirect communication, messages are sent to and received from ports ("mailboxes")
  * A link is established between two processes, if both processes have a shared mailbox
  * A link may be associated with more than two processes
  * Between two processes, there may be many different links / mailboxes
* Mailboxes may be owned by a process or by the operating system
  * If a mailbox is owned by a process / part of the address space of the process, then we must distinguish between the owner of the mailbox and a user of the mailbox
    * Owner can only receive messages through the mailbox - only one owner, so no confusion about which process should receive a message
    * User can only send messages to the mailbox
    * When the process that owns the mailbox terminates, the mailbox disappears and any user / message-sending process must be notified that the mailbox does not exist

