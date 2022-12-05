# 6.6.3 The Dining-Philosopher's Problem

* Scenario is a table with 5 philsophers sitting around it
* To each philosopher's left and right is a chopstick
* Each philosopher may pick up one chopstick at a time
* When a philosopher has their chopsticks, they eat without releasing her chopsticks
* When a philosopher is finished eating, they put down their chopsticks
* Classic synchronization problem as it represents the need to allocate several resources among several processes in a deadlock-free and starvation-free manner
* Simple potential solution is to represent each chopstick with a semaphore, initialized to `1`
  * When a philosopher picks up a chopstick, the `wait` operation on that chopstick's semaphore is executed
  * When a philosopher puts down a chopstick, the `signal` operation is executed on that chopstick's semaphore
  * Deadlock occurs when each philosopher decides to pick up their left chopstick simulatenously (setting all the chopstick semaphores to `0`)
    * When the philosophers try and pick up their right chopstick, none of the chopsticks are available and they all will be delayed forever (since no philosopher can "finish" eating and put down their left chopstick)
* Some potential solutions to deadlock problem
  * Only allow four of the philosophers at the table at any given time
  * Allow philosopher to pick up chopsticks only if both are available
  * Use asymmetric solution (odd philosopher picks up left then right, even philosopher picks up right then left)
