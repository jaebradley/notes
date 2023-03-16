# [The Unfairly Unknown ForkJoinPool]https://medium.com/swlh/the-unfairly-unknown-forkjoinpool-c262777def6a

* Each task is split into subtasks until they cannot be split anymore, then they get executed in parallel and once they are all completed, the results are combined
* The default level of parallelism (number of threads) is the number of available processors in the system
* For CPU-intensive tasks, no benefit having a pool larger than the number of processors that are available
  * For I/O-intensive tasks where frequently waiting for I/O operations to compelte, there may be benefits from a larger pool
* Each of these worker threads has its own queue (double-ended queue)
* Each worker keeps scanning for available subtasks to be executed, with the main goal to keep worker threads as busy as possible and maximize CPU usage
  * A thread will only block when there are no available subtasks to run
* If a worker cannot find tasks to run on its own queue, it will try and steal tasksk from workers that are busier
* To guarantee that the owner of the queue and the "stealer" don't interfere with each other and grab the same task, the owner grabs from the head of the stack and the stealer grabs from the end of the stack
* By picking the most recent task, increases the chances of having the task resources still allocated in the CPU cache
* Due to the nature of these divisible tasks, the tasks that have been in the queue the longest are most likely to provide big chunks of work and haven't been split yet
