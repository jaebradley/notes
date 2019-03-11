# Commits

* When a commit occurs, `Git` records a snapshot of the index and places that snapshot in the object store
* Snapshot does not contain a copy of every file and directory in the index because that would be very expensive from a memory perspective
* `Git` compares snapshot to previous snapshot to derive list of affected files and directories
* `blobs` are created for any changed files, and new trees for any directories that have changed, while reusing any objects that haven't changed
  * Remember that if the `SHA1` hash is the same, (even for a directory) that means that nothing has changed
* These snapshots are chained together, with each snapshot pointing to its predecessor

## Absolute Commit Names

* Each commit id is globally unique - across all repositories
* This is because the commit hash contains information about the state of the entire repository tree and the prior commit state
* Basically, there is one line of development leading up to and including the commit
