# [Git commit vs. squash commit](https://stackoverflow.com/questions/47089913/merge-commit-vs-normal-commit)

```bash
A - B - C - D (master)
     \
      F - G - H (feature)


# Merge commit (E) references both commit D and commit H
# It has more than one parent
 
A - B - C - D - E (master)
     \         /
      F - G - H (feature)


# Squash commit (E) references only commit D (even though it contains changes in feature

A - B - C - D - E (master)
     \
      F - G - H (feature)
```
