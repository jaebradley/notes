# [How I solved a distributed queue problem after 15 years](https://www.dbos.dev/blog/durable-queues)
* Reddit operations were powered by distributed queues that sat in front of a database
* Example was an upvote, where the upvote was written to a queue and a cache
  * A success was returned to the user
* There was a queue runner that would attempt to execute the work item as well as create any additional work items to recalculate any business logic associated with that action
* Task parallelization was horizontally scalable by adding workers
* Could control the flow rate for certain operations / queues
* Flexible scheduling that could be cron-based, or on some repeatable cadence
* Task queue suffered from durability
  * Example is that the queue itself was stored in memory, so if the queue was lost then operations were lost
* Durable queues that checkpoint the status of queued tasks in persisted storage (Postgres)
  * Can resume failed jobs from last completed step and can handle power loss
* Core abstraction of durable queues is a workflow of tasks
  * Document processing task that splits a document into pages and each page into separate tasks
* Checkpointing works by recording the inputs for a task submission
* Tasks that invoke another tasks are recorded as children of the parent task
  * Thus, there is a persistent record of tasks and their relationships
* Durable queue is best for business-critical tasks that occur at lower volumes
