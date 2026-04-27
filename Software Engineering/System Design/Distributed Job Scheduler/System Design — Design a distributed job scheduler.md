# [System Design — Design a distributed job scheduler (KISS Interview series)](https://medium.com/@raxshah/system-design-design-a-distributed-job-scheduler-kiss-interview-series-753107c0104c)

## System Interface
* Submit Job (return `202`)
* View Job (for a given user and job ID)
* List Jobs (for a given user, and paginated job)

## High-Level Design
* User submits job via API (behind load balancer)
* Request is persisted in database
* Job executor service polls due jobs from database
  * Puts due jobs in queue
* Job executor service executes job business logic
  * Updates the final result in the file system
  * Update the status of the job as completed

### Database design
* No ACID support
* Schema design
  * User ID
  * Job ID
  * Job Status
  * Scheduled Execution Time

### Polling Tasks That Are Due
* Partition based on X minute size bucket window
  * Create an index of the job execution time and job id
  * Select job IDs that have a minute execution time matching some value
  * Slow query performance if many jobs have been received in a particular time window
* Partition based on X minute size bucket window AND a shard ID
  * Add a (random) shard ID to further partition the dataset
  * Each downstream worker will be associated with a shard ID
 
## Job Scheduler Flow
* Every X minutes, the leader node assigns an authoritative timestamp, and a shard ID to each worker node
* Worker node fetches all the jobs matching the timestamp and the shard ID and inserts the jobs into a Kafka queue
* Leader node re-assigns job fetching query to a new worker

## Job Executor Flow
* Multiple consumers pulling data from Kafka queue
* Leader and follower workers
  * Leader polls queue for new jobs
  * Follower polls leader for new jobs
* When a job is removed from the queue, leader node updates the job status to claimed
* When a follower node picks up the job, the job status is updated to processing
* When a follower node completes the job, the follower node uploads the job to some persistence layer (S3, for example)
  * Updates job status to completed
* Can also have a health checking service that receives health checks from each worker process
  * If a worker process does not respond with a health check in some pre-determined amount of time, the job is considered to have failed
  * The job is inserted back into the Kafka queue
