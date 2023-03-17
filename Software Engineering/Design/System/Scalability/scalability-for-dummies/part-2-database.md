# Part 2 - Database

## Path 1: Stick with MySQL

* Master-slave replication (read from slaves, write to master)
* Upgrade master by adding RAMRAMRAM
* Eventually will have to start sharding, denormalization, and SQL tuning
* Every new action to keep db running will get more and more expensive

## Path 2: NoSQL

* Do joins in application code
* DB requests will still get slower and slower - need to introduce caching
