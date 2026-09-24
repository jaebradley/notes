# [Ep #128: Two-Phase Commit — The Correct Answer Nobody Uses in Production (Part 2)](https://thearchitectsnotebook.substack.com/p/ep-128-two-phase-commit-the-correct)
* Coordinator asks every participant: can you commit this data? (`prepare` step)
* Each participant does whatever local work is necessary to guarantee that they can commit the referenced data
  * This usually involves lock acquisition and writing to a durable log
* Commit must be durable to server crash and restart
  * i.e. the commit decision (`yes` or `no`) must be durably logged before the coordinator sends the commit decision to all participants
  * Otherwise, if the coordinator crashes, the participants will be blocked indefinitely
* If every participant responds with `yes`, then the coordinator tells each participant to commit
* If even a single participant responds with `no` or times out, the coordinator tells each participant to abort, rolling back the data changes
* Every participant has to hold locks until the coordinator tells it to either `commit` or `abort`
  * This can lead to a deadlock where the coordinator might be dead
  * While the coordinator is recovering, these rows are locked and unavailable to all other transactions that want to introspect them

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/136b39e8-027b-4c56-b4e6-8880e55c5a75" />

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/5eb73d07-fed4-4e46-b1cc-222a2ee2c46c" />

