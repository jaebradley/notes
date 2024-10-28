# [Story Points are Pointless, Measure Queues](https://www.brightball.com/articles/story-points-are-pointless-measure-queues)

* Points / complexity measures may be wildly different between teams (i.e. Team A may find a task to be a `5` while Team B might find the same task to be a `20`)
* But then compare teams based on the points they complete
* Fibonacci sequence is used to represent variability - a `1` is a very precise measurement but a `13` is really representative of a “range” from `9-19`
* But then, all the points are summed (even though we know these points measure a variable “complexity” value) and used as if they accurately measure something meaningful
* “Starting today, all my 1-point stories are 2-point stories - poof I’ve doubled my velocity. Problem solved”
* Default function of “velocity” is to convert points to time as average points per sprint, which typically breaks down to X points per 2 weeks
* Points aren’t time, and highly variable - yet deadlines are built using them
* Invest a lot of time in the process of estimating story points, without a valuable output. For example, teams that spend 30+ minutes discussing whether a story was worth 3 points vs. 5 points, without writing down a single detail of their reasoning
* Then the business uses these unreliable numbers to judge performance, even though the numbers are “broken”

## what if we substitute something else for story points?

* Business wants to convert effort into time, so why not just estimate with time
* Again there’s a “relative” team problem, but at least the team is working in the same units (time)
* “This will take 2 weeks” = “This will take 2 weeks if our entire team has no interruptions once we start work next week after this dependency has been resolved”
* Time can work for small teams - single dev / pair of devs who have worked together can probably agree on a timeline and hit it consistently

## meet donald reinertsen

* Focus on the efficiency of the organization as a system
* Goal is to ensure that the flow of work is maximized
* Queue is a list of work to be done - backlog of stories, task list
* Closer team is to 100% capacity utilization, the larger these queues grow, dramatically increasing the variability of the work, and thus making estimates inaccurate
* Having a buffer means having a buffer time and filling it with nothing (or else it’s not a buffer)

## solving the conundrum

* Author suggests mob programming, i.e. team works on task together
* Author’s argument is that this is what happens during a grooming session, except that rather than producing a meaningless number at the end of session, produce something closer to an implementation plan
* Come out of the process with a known task list and an identification of uncertainties
* Some uncertainties can need research “spikes”

## a list of tasks is a queue

* Replaced story points with a theoretically more reliable value of small broken down tasks after removing uncertainties
* Document exactly when, why, and how any scope change occurred, and this in turn will update enture projection live as the project progresses
* The task lists should spell out why a “job” or “feature” is taking some amount of time
* These changes were added on some date, for the reasons based on some feedback

## queues are leading indicators

* Queue size increases can be seen well in advance
* Support queue (or unplanned work) has a flow rate outside of internal control
* One way to control flow rate is better quality control and rapid response to support issues

