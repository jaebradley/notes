# [How to think about durable execution](https://hatchet.run/blog/durable-execution)
* Author starts with a task queue, a system that ensures background tasks get scheduled and executed on a separate set of workers
* Message brokers are used as an intermediate persistence layer to avoid losing messages if a worker crashes while processing a message
* If the background task is naturally idempotent (i.e. it is stateless) and can update application in a single atomic action (like a database transaction) then a simple tasks queue is most-likely sufficient
  * As a concrete example: file upload that has two steps
    * Call an external API to process a file upload
    * Update file's status to `PROCESSED` in a persistence layer (like a database)
  * If the worker crashes while processing this task and the message broker will resend the task to a new worker on task failure, the application will end up in a consistent state
* Examples of complex tasks that cannot be modeled as a single idempotent task
  * Tasks that have stateful side-effects like mutating a key-value store
  * Tasks that call a variety of different microservices which also may have stateful side-effects
* Durable execution is one approach to executing and managing state of interrelated tasks (workflows)
  * Other approaches are DAG-based systems, Celery chains and chords, event-sourcing
  * Durable execution allows intermediate workflow state to be persisted in a separate data store
  * Enables workflows to start from this checkpointed state

## Durable execution
* External durable execution state stores persist each workflow's incremental state in an external system
* The workflow has the ability to skip any subtasks / any action persisted to the durable event log
* Example is a workflow that processes a payment and then sends a confirmation email
  * On the first execution of this workflow, the executing machine runs out of memory, forcefully terminating the process
    * This OOM occurs after processing the payment but _before_ sending the confirmation email
  * On the second execution of the workflow, the workflow can automatically skip the payment processing step as the execution history shows that the step has already executed
* Workflows are retried until they run to completion
* Each subtask / unit of execution invoked from the workflow is executed exactly once using an idempotency key
* Workflows must be deterministic
  * Workflows track an append-only event log to ensure that the ordering of subtasks is invariant
* Determinism can be a problematic property to enforce
  * Need to ensure that iterating over an object or array does not change between executions
  * Network or database execution that may fail or return a different result when executed a second time
    * These side effects need to occur in the individual tasks that the workflow calls
  * Implementation changes that rewrite part of the execution history, for example switching the order of the confirmation email and payment processing from the previous example

