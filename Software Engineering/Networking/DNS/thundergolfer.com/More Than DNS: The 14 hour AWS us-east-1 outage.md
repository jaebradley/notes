# [More Than DNS: The 14 hour AWS us-east-1 outage](https://thundergolfer.com/blog/aws-us-east-1-outage-oct20)
* To maintain population of all DNS entries for `dynamodb.us-east-1.amazonaws.com`, Amazon runs three DNS Enactors
  * `us-east-1a`, `us-east-1b`, and `us-east1c`
  * These Enactors perform mutation *without* coordination
* One of these Enactors became extremely slow (root cause of latency is unknown)
* They use a "keep last `n`" garbage collection mechanism to remove old DNS plans
  * Typical to garbage collect old machine images
  * The last `n` must *never* include an active resource
* The plan applied by the slow Enactor fell out of the `n`  generations safety window and became old, eligible for deletion
* DNS Planner makes append-only changes while the DNS Enactor makes forward progress against the plan log and maintains the window of active DNS records
* `DropletWorkflowManager` (DWFM) - droplets are physical servers upon which all EC2 instances run
* DWFM depends on DynamoDB to complete heartbeats
* If the heartbeat with a server stops, then DWFM's control of the server is cut off
  * Without this control, no creation or state transition can occur on an EC2 instance
* Due to the DynamoDB outage, there were at least three orders of magnitude more broken leases than normal
* With a huge queue of leases to reestablish, there was congestive collapse where "efforts to establish new droplet leases took long enough that the work could not be completed before they timed out"
