# Chapter 10. Clusters and Job Queues

## Parallel Pands with Dask
* For Pandas usage, if dataset is larger than Pandas can fit into RAM, Dask can split the dataset by rows into a set of partitioned DataFrames called a "Distributed Dataframe"
  * DataFrames are split by their index
  * Operations are map/reduce-d across each partition
* Need to specify the number of partitioned DataFrames - rule of thumb is as many partitions as cores, so that each core can be utilized

## Vaex for bigger-than-RAM DataFrames
* Vaex is an alternative library that will perform computations on only a subset of the rows required by the user
  * If you want to sum across two columns of a billion row dataset, and you only want to sum some sample of the billion rows, Vaex will only touch the sampled data and will not perform any computations on the nonsamplesd rows

## NSQ For Robust Production Clustering
* Queues are most useful in distributed processing whan an imbalance exists between production and consumption
  * If there is backpressure, can scale consumers until the message production rate and consumption rate are equal
* NSQ stores messages in memory, and then moves messages onto disk as the queue increases in size
* NSQ saves pending messages to disk when shutting down
  * Does not protect against catastrophic hardware loss
* If a consumer is shut down before responding to a particular message, NSQ will resend the message to another consumer
  * All messages in a topic will be sent to a consumer at least once

## Docker
* Docker creates a special namespace within the host operating system
* Because Docker relies on the `cgroups` Linux feature, when running Docker on a non-Linux host operating system environment, the Linux kernel must be run in a hardware-virtualized environment
  * Docker Machine uses VirtualBox for this hardware viritualization
  * There will be performance overhead associated with this hardware virtualization
