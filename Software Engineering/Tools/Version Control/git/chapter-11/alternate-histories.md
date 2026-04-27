# Alternate Histories

```bash
# Origin

A - B (master)

# Yours (you make a couple commits) in your master branch that tracks origin/master

A - B (origin/master) - X - Y (master)

# Teammate pushes two commits to origin/master

# Origin

A - B - C - D (master)

# When you try to push up your changes, the push fails since history has changed from your version of origin/master history
# Need to fetch origin/master locally and then merge origin/master into master

# After fetch

A - B - X - Y (master)
     \
      C ----- D (origin/master)

# After merging origin/master into master

A - B - X - Y - M (master)
     \         /
      C ----- D (origin/master)


# Need to push up local master to origin/master to change it's history
# Before push, origin/master is still A - B - C - D
```
