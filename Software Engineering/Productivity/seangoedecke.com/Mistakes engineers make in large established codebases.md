# [Mistakes engineers make in large established codebases](https://www.seangoedecke.com/large-established-codebases/)

## The cardinal mistake is inconsistency

* You must resist the urge to make your little corner of the codebase nicer than the rest of it
* If you do your auth like other API endpoints that have stuck around for a long time, you can follow that path without having to know all the surprising things that the codebase does
  * In the auth example, things like bots, which might have similar behavior of users and/or have special auth treatment
  * Or internal support tooling that allows a company user to authenticate on behalf of another user
* Being inconsistent makes it harder to make general improvements
  * Having a single consistent auth path makes it easier to introduce a new type of user
  * End up with the hardest 5% of endpoints to update being left out of scope because now a user type only works for some-but-not-all endpoints

## Is anything else important?

* Need to develop a good sense of how the service is used by end users
  * Which endpoints are hit most often?
  * Which endpoitns are the most crucial?
  * What are the "hot logical paths"
  * One common mistake is a "tiny tweak" that is in the hot path for a critical flow that causes big issues
* Be very very reluctant to introduce new dependencies
  * Ongoing cost in security vulnerabilities and package updates
* If you ever get the chance to remove code, take it with both hands
  * Riskiest work in large codebases
  * Instrument the code to identify callers, drive them to zero to be sure it's safe to remove
  * Always worth doing
* Front-load changes that affect other teams' code
* You cannot split up a large established codebase without first understanding it
  * "I have seen large codebases successfully split up, but I have never seen that done by a team that wasn't already fluent at shipping features inside the large codebase"
