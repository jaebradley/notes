# Rebase

* `git rebase` alters where a sequence of commits is based
  * For example, keeping a feature branch up-to-date with `master`
  * `rebase` relocates commits one at a time from original commit location to new commit base
* `git rebase` creates completely new commits for the previous sequence of commits
  * They have new SHA1 commit IDs
  * Based on new initial state
  * Represent different diffs
  * All this to achieve the same state as a merge

```bash
# feature branch that has branched off of master, but is ultimately behind

A - B - C - D - E (master)
     \
      F - G - H (feature)

# After rebasing feature onto master

A - B - C - D - E (master)
                 \
                  F' - G' - H' (feature)
```

## Transplanting commits

```bash
A - B - C - D - E (master)
     \
      F - G - H (feature1)
           \
            I - J (feature2)

# git rebase --onto master feature1^feature2
# This will transplant only commits I and J (i.e. commits in feature2 not in feature1) 
# to be based off of master

                  I' - J' (feature2)
                 /
A - B - C - D - E
     \ 
      F - G - H (feature1)          
```

## Rebasing first level of nested branching

```bash
A - B - C - D - E (master)
     \
      F - G - H (feature1)
           \
            I - J (feature2)

# git rebase master feature1
# This will move feature1's commits "on top of" master
# New commits will be created for work in F, G, and H
# But F and G will still exist since feature2 depends on it
# Commit H will no longer exist as it's no longer reachable

                  F' - G' - H' (feature1)
                 /
A - B - C - D - E
     \
      F - G - I - J (feature2)

# You'll probably want to rebase feature2 on feature1
```

## Rebasing with a branch that has merge commits

```bash
# dev has a merge commit (M)

            P - M - N (dev)
           /   /
      X - Y - Z
     / 
A - B - C - D (master)

# What you might expect it to look like

                    P - M - N (dev)
                   /   /
              X - Y - Z
             / 
A - B - C - D (master)

# What it actually looks like (without --preserves-merges)

              X - Y - Z - P - M - N (dev) 
             /
A - B - C - D (master)
```

* Git needs to find merge base from tip of `dev` back to `B` on `master` so it uses `master..dev`
* Git performs a topological sort on these commits to produce linearized sequence of commits in that range
* Git will then apply these commits one by one
* If you want to keep merging structure there is a `--preserves-merges` option
