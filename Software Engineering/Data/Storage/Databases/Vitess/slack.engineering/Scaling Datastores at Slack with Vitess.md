# [Scaling Datastores at Slack with Vitess](https://slack.engineering/scaling-datastores-at-slack-with-vitess/)
* Initially, Slack was Linux + Apache + MySQL + PHP
* All data was stored on three primary database clusters
  * All data for a given company / workspace was stored on the same shard, so the application just needed to connect to a single database
  * Metadata cluster was used as a lookup table that mapped a workspace to the underlying shard
    * To find the shard for a particular workspace, there had to be a lookup against this metadata table first
  *  Other data not tied to a specific workspace like the application directory
* Each shard was provisioned with at least two MySQL instances
  * These instances were located in different datacenters and replicated via asynchronous replication
* Advantages
  * Intuitive to have all data for a single workspace stored on a single database host
  * Easy to debug as a Slack engineer could connect to the database host
  * Easy to initially scale: as more companies signed up, more database shards could be provisioned
* Disadvantages
  * As larger and larger customers were onboarded, available hardware limits were reached
  * As the company grew, there were products that didn't assume that all data for a team was stored on the same database shard
  * Large enterprise customers lead to database hotspotting and overprovisioning most shards, leaving the long tail of shards, underutilized
  * When a database shard experienced an outage, every single customer whose data was also on that shard also experienced a full Slack outage
