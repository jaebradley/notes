# Cloning and Tracking Branches

* When a `clone` occurs
  * Local development branches in the remote repository are cloned as tracking branches
    * `refs/remotes` in cloned directory
  * `origin` remote is created (by default) that points to original repository URL
    * Origin remote has no knowledge of all it's clones
  * Creates a default `fetch` `refspec`
    * This `refspec` basically says that local development branches on the remote should be available to the cloned repository with the `origin/` prefix

* Remote-related operations
  * `fetch` - Retrieves data from remote repository
  * `pull` - `fetch`es and also merges any data into specified branch
  * `push` - Transfers data from local, cloned repository to remote repository
  * `ls-remote` - Show references within a remote

* Tracking branches
  * Local "topic" branch `foo` is actually located at `refs/heads/topic`
  * Local tracking branch `foo` from remote `origin` is actually located at `refs/remotes/origin/foo`

