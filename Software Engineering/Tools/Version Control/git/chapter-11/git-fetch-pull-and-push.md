# Git fetch, pull, and push

* `git pull <options> <repository> <refspec>
  * If the repository is not defined by either a Git URL or a remote, then `origin` is used by default
  * If a `refspec` is not defined then the `fetch` refspec is used of the given remote
  * If a repository is defined by a refspec is not, Git fetches the `HEAD` of the specified repository
* `pull` is inherently two operations - a `fetch` and then a `merge` or `rebase`

## `fetch`

* When fetching, Git tries to locate the repository 
* If URL or remote is not used, then the default, `origin` is used
* It then uses the `refspec` for `origin` defined in the configuration file
* Git figures out what commits are missing given the `/refs/heads/*` in the fetch refspec (this is the default, your refspec could be much more specific)
* Git looked at remote repo `tmp/Depot/public_html`
* Git took it's topic branch `master` (located in `/refs/heads/`) and placed them in local `origin/master` branch
  * Or to be even more specific, local `/refs/remotes/origin/master`
* Commit ids are included for any further inspection

```bash
# Output might look something like
From /tmp/Depot/public_html
commit1..commit2 master -> origin/master
```

## `merge`

* Git looks at configuration file for branch `master`, let's say (it might look something like)
* The following basically means
  * When `master` is the current, checked out branch
  * Use `origin` as the default remote
  * In the `merge` step, use the `refs/heads/master` branch **on the remote** to merge into **local** `master`

```bash
[branch "master"]
  remote = origin
  merge = refs/heads/master
```

* To rebase instead of merge, add the `rebase=true` configuration option

## Creating a local branch based on a remote tracking branch

* `git branch feature1 origin/master` will create the following entry in the configuration file
  * You may also see message "Branch feature1 set up to track remote branch refs/remotes/origin/master"

```bash
[branch "feature1"]
  remote = origin
  merge = refs/heads/master
```
