# 10.1 Deadlock

* Dining philosophers' problem
* 5 philosophers eating dinner at a table
* 5 chopsticks, one placed between each pair of diners
* To eat, philosophers need to acquire two chopsticks
* If each philosopher grabs a chopstick to their left, then they will wait forever to acquire the chopstick to their right (as it's held by the philosopher to their right)
* If thread 1 holds lock A and tries to acquire lock B, but thread 2 holds lock B and is trying to acquire lock A, these threads are in a deadlock
