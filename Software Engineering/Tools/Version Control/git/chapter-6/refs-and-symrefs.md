# refs and symrefs

* A ref is a SHA1 hash id that refers to an object in object store
* A symref is a name that points to an object in object store
* Local branch names, remote-tracking branch names and tag names are all refs
* Each symbolic ref has an explicit, full name that begins with `refs/` and each symbolic ref is stored within `.git/refs/`
  * `refs/heads/{ref}` - local branches
  * `refs/remotes/{ref}` - remote tracking branches
  * `refs/tags/{ref}` - tags
  * local branch named `some-local-branch` is a short form of `refs/heads/some-local-branch`
* Special symrefs
  * `HEAD` - most recent commit on current branch
  * `ORIG_HEAD` - `merge` records previous version of `HEAD` in `ORIG_HEAD` before adjusting to new value
    * `ORIG_HEAD` can be used to recover or revert the previous state
  * `MERGE_HEAD` - when merge is in-progress, the tip of the other branch is temporarily recorded in `MERGE HEAD`
    * `MERGE_HEAD` is the commit that is being merged into `HEAD`
