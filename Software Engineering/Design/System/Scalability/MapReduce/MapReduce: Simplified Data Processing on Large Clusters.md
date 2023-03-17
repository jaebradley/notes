# [MapReduce: Simplified Data Processing on Large Cluster](https://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)

# Introduction

* Computation takes a set of input key/value pairs and produces set of output key/value pairs
  * The Map funciton, takes an input pair and produces a set of intermediate key/value pairs
  * These intermediate pairs are grouped by their intermediate key I and passed to the Reduce function
  * The Reduce function, accepts an intermediate key I, and a set of values associated with the Key (via an iterator) and merges the values together
* Consider counting number of occurrences of each word in a large collection of documents
  * Each word could be mapped to an "intermediate" key/value pair of the word as the key and 1 as the value (i.e. the "count")
  * Then reduce would sum all the counts for a given word
* Real-world example: Inverted Index
  * Map function parses each document and emits a sequence of (word, document ID) pairs
  * Reduce function accepts all pairs for a given word and sorts the corresponding document IDs and emits a (word, list(document ID)) pair - these output pairs are the inverted index

# Implementation

* Map invocations are distributed across multiple machines by automatically partitioning the input data into a set of M splits
* Reduce invocations are distributed by partitioning the intermediate key space into R pieces using a partitioning function like `hash(key) mod R)`
* MapReduce library splits input files into M pieces of 16-64 MB per piece
  * Starts up many copies of the program on a cluster of machines
* One of the machines is the master and the other machines are workers
  * There are M map tasks and R reduce tasks that the master is responsible for assigning by picking idle workers to work on these tasks
* A worker assigned a map task reads the contents of the corresponding input split
  * Parses key/value pairs out of input data and passes each pair to the Map function
  * The intermediate key/value pairs produced by the Map function are buffered into memory
* Buffered intermediate key/value pairs are written to local disk, partitioned into R regions
  * Location of these buffered pairs on local disk are passed back to master, who will then forward these locations to the reduce workers
* Remote worker uses RPC to read the buffered data from the local disks of map worker
  * Reduce worker reads intermediate data, sorts by intermediate key so all occurrences of the same key are grouped together
  * If amount of intermediate data is too large to fit in memory, external sort is used
* Reduce worker iterates over sorted intermediate data and for each unique intermediate key, passes key and set of all intermediate values to the Reduce function
  * Output of Reduce function is appended to final output file for this reduce partition

## Master Data Structures

* For each map and reduce task, master stores the state and the identity of the worker machine
* For each completed map task, master stores the locations and sizes of the R intermediate file regions produced by the map task

## Fault Tolerance

* Master pings every worker periodically
  * If it does not get a response in a certain period of time, it marks the worker as failed
  * Any map tasks completed or in-progress by the worker are reset back to the initial "idle" state and are eligible for rescheduling
  * An in-progress reduce task on a failed worker is reset to "idle"
* Completed map tasks are re-executed on failure because their output is stored on the local disk of the failed machine
* Completed reduce tasks do not need to be re-executed since their output is stored on a global file system

### Semantics in the Presence of Failures

* If map and reduce are deterministic, the distributed implementation (with failure handling) will produce the same output as would have been produced by a non-faulting sequential execution of the entire program
* Atomic commits for both map and reduce task outputs
* Each in-progress task writes its output to private temporary files
* When a map tasks completes, the worker sends a message to the master and includes the names of the R temporary files in the message
  * If the master receives a completion message for an already completed map task, it ignores the message, else it records the names of the R files in a master data structure
* When a reduce task completes, the reduce worker atomically renames its temporary output file to the final output file

## Locality

* Conserve network bandwidth by taking advantage of the fact that the input data (managed by Google File System) is stored on the local disks of the machines that make up the cluster
* Google File System divides each file into 64 MB blocks and stores several copies of each block (typically 3 copies) on different machines
* MapReduce master takes the file block location information into account
  * Attempts to schedule map tasks on machines that contain a replica of the corresponding input data, or a machine that is adjacent to task's intput data (like on a worker machine that is on the same network switch as the machine containing the data)

## Backup Tasks

* One of the common causes that lengthens the total time taken for a MapReduce operation is a "straggler"
  * A machine that takes an unusually long time to complete one of the last map or reduce tasks in the computation
* Stragglers can occur due to a bad disk, which would slow read performance, CPU resource competition if there are many other tasks scheduled on the same machine
* To deal with stragglers, when a MapReduce operation is close to completion, the master schedules backup executions of the remaining in-progress tasks
  * The task will be marked as completed when the primary or backup execution completes

# Refinements

## Partitioning Function

* There are R reduce tasks / output files
* Data gets partitioned across these tasks using a partitioning function on the intermediate key
* By default, this partitioning function is `hash(key) mod R`
* In some cases, useful to partition data by some other function of the key, like if the keys were URLs, maybe all the entries for a single host should end up in the same output file
  * `hash(Hostname(urlkey)) mod R`

## Combiner Function

* From the word counting example, each map task will produce many many records of the form (word, 1)
* These records will be sent over the network to a single task
* User can specify an optional combiner function that does partial merging of data before it is sent over the network
* The only difference between a reduce function and a combiner function is how the MapReduce library handles the output of the function
  * Reduce function output is writtent to final output file
  * Combiner function output is written to an intermediate file that will be sent to a reduce task
