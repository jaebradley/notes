# [The Law of Leaky Abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)

## Example of Abstraction

* TCP is a way to transmit data that is reliable (it will arrive, and it won't be garbled / corrupted)
* IP is a method for transmitting data which is unreliable
  * If a bunch of messages are sent, only half of them may arrive and some of them might be in a different order than the ones that were sent
* TCP is built on top of IP (i.e. a tool that sends data reliably is built on top of an unreliable tool)

## Analogy for IP/TCP

* Send actors from Broadway to Hollywood in cars
* Not all the actors make it to Hollywood alive (car crash, etc)
* Another service puts actors in cars and drives them to Hollywood as well, but when a car crashes, the actor's identical twin is sent again instead
  * If there's an accident as these actors make their way to Hollywood, the service just reroutes the actors, not telling the move directors what happened
  * To the movie directors, it just looks like the actors arrived a little bit slower than usual and they don't even know about the accident

## Leaky Abstractions

* TCP doesn't guarantee that the message arrives - if the network cable connecting to your computer breaks and _no_ IP packets can get from your computer to the network, then TCP can't do anything about that and your message does not arrive
* TCP attempts to provide a complete abstraction of an underlying unreliable network, but sometimes, the network leaks through the abstraction and you feel the things that the abstraction cannot protect you from
* The Law of Leaky Abstractions: All non-trivial abstractions are to some degree leaky
* Iterating over a 2-D array can have radically different performance if done "horizontally" rather than "vertically" as one direction may result in more page faults than the other direction (and page faults are slow)
  * The "abstraction" to programmers is that they are supposed to pretend that they have a big flat address space, but this abstraction leaks when there's a page fault and certain memory fetches take more time than other memory fetches
* SQL language is meant to abstract away the procedural steps that are needed to query a database, instead letting the database figure out the procedural steps to query itself
  * Some SQL queries are thousands of times slower than logically equivalent queries (`where a=b and b=c and a=c` and `where a=b and b=c` may see performance differences even though they are logically the same)
  * You're not supposed to care about the procedure, but sometimes the abstraction leaks, and you have to look at a query plan and study what happened procedurally
* Car windshield wipers and headlights are supposed to "abstract" away the weather by helping you not care about the fact that it's raining
  * But still have to worry about hydroplaning or sometimes the rain is so strong that you con't see very far ahead so you go slowerin the rain
