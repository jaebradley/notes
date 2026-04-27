# [Why Can't I Delete My Branch After Squash Merge?](https://stackoverflow.com/questions/41946475/git-why-cant-i-delete-my-branch-after-a-squash-merge)

```text
--0--0--0     <-- master
   \
    A--B--C   <-- feature
```

* Remember that a branch points only to a single specific commit
* That commit points back to a previous commit, etc.
* These backward pointers form the actual branch

## After `rebase`

```text
--0--0--0             <-- master
   \     \
    \     A'--B'--C'  <-- feature
     \
      A--B--C   <-- old feature (abandoned)
```

* `rebase` copies previous commits to new commits appended to tip of `master`

## After `merge --squash`

```text
--0--0--0--S          <--master
         \
          A'--B'--C'  <--feature
```

* `merge --squash` make one new commit that is a "squash copy" of the previous commits that are on `feature`

## How does Git determine if a branch can be deleted?

* Git determines if `feature` can be deleted by checking if `feature` has been merged into `master`
* Git determines if something has been merged into `master` by checking if `master` can reach the tip of `feature` (in this case `C'`) by traveling "backwards" ("leftwards")
* For the following `merge` commit, notice how `C'` is reachable from `master` since there's a "down-and-left" link to commit `C'`
  * Commit `C'` is called the "second parent" of merge commit `M`

```text
--0--0--0------------M
         \          /
          A'--B'--C'
```
