# [Scaling Slack's Job Queue](https://slack.engineering/scaling-slacks-job-queue-687222e9d100)

* Slack uses a job queue to do background processing that are too time-consuming for a web request
* Slack experienced production outage due to job queue when database slowdown caused job execution slowdown which led to Redis reaching it's memory limit
  * Redis had no more free memory which meant that it could no longer enqueue new jobs
  * Slack's system actually required Redis memory to _dequeue_ job so even after underlying database slowdown was resolved, the job queue was still facing processing issues
* Initial job queue architecture
  * Each job has a unique id associated with it
  * Checks to see if another job with the same id already exists in the queue that the job is assigned to
  * Workers poll Redis looking for new jobs - when it finds a new job, it moves the job from the pending queue to a list of in-flight jobs and spawns an async task to handle the job
  * When the task is complete, worker removes the job from in-flight jobs
    * If job has failed, worker moves it to another retry queue and if it continues failing, to a list of permanently failed jobs to be investigated
* Problems with the initial architecture
  * Memory limits with respect to enqueue / dequeue rates - if jobs were enqueued faster than they were dequeued then the memory limit would be reached and unable to dequeue jobs
  * Adding workers to handle additional job load to Redis would also increase load on Redis through extra polling
* Main solution was to replace Redis' in-memory store with durable storage (like Kafka) to prefent memory exhuastion and job loss
* Slack's strategy was to add Kafka in front of Redis rather than replacing Redis with Kafka - this was to establish a minimum viable change while avoiding significant changes to application logic
* Slack created a simple Kafka service that had a HTTP POST API where requests containing a Kafka topic, partition, and message would be enqueued to Kafka
  * Slack biased availability over consistency - when writing to Kafka, only waited for leader to acknowledge request and did not wait for acknowledgement of replication across brokers
    * Lowest latency possible, but risk of lost messages if broker leader dies before replication
  * Kafkagate implements a synchronous write and returns errors in failure cases or timeouts, which simplifies client interface / behavior
* Messages are relayed from Kafka topic to the corresponding Redis cluster
  * Uses a Consul lock to ensure that all Kafka topics in the job queue have only one relay process associated with them
  * The service consuming from a Kafka partition will only advance offset if the message is successfully written to Redis
  * If errors occur while executing a job / message, the job / message is re-enqueued into Kafka instead of dropping message - allows ability to diagnose job failures without losing jobs and without having a job-specific error block all progress for a given queue
* Rollout included writing to both existing system and new system (i.e. Kafka)
  * However, once system that read from Kafka and wrote to Redis read a message, it would drop that message so that Redis wouldn't do duplicate work
    * Still allowed for safe testing of new code path using production traffic
  * Implemented heartbeats for every Kafka partition, every minute
* Web application can handle high enqueue rates as messages are written to durable (rather than in-memory) storage and then service that reads from Kafka is tuned if enqueue rate is out-pacing deqeue rate
