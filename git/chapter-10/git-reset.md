# Git Reset

* **You shouldn't rewrite, alter, or change any part of a branch that's made available and might be present**
* `git reset` adjusts the `HEAD` ref to a given commit
  * By default, it also changes the index to match the commit
* As the name suggests, the point is to recover known states for `HEAD`, index, and working directory
* Three options
  * `--soft`
    * Changes the `HEAD` ref to point to a given commit
    * Index and working directory are unchanged
  * `--mixed`
    * Changes the `HEAD` ref to point to given commit
    * Index is changed to match tree at commit
    * Working directory is unchanged
  * `--hard`
    * Changes the `HEAD` ref to point to given commit
    * Index and working directory are changed to reflect tree at commit
    * **This is destructive - new files / modifications are lost**
* `git reset` moves the commit branch's `HEAD` to a specific commit

