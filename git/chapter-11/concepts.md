# Concepts

* `clone` is a copy of a repository
  * Each clone is an individual and autonomous repository
  * Clones allow developer to work without having to deal with polls and locks
* `remote` is a reference to another repository 
  * A shorthand name for a Git URL
* Git uses _tracking branches_ to keep track of data from other repos
  * Tracking branches are local branches that are proxies for the specific branch on the remote repository

## Bare vs. Development Repositories

* Repositories used for every-day development are "development" repositories
* Dev repositories have concept of working directory and current branch
* Bare repositories don't have concept of working directory
* Bare repositories serve as authoritative base for pushing, pulling, and cloning changes
* Repositories that developers push changes to should be bare

