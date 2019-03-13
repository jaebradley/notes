# Commit Ranges

## Double dots

* In format `start..end` which is the set of commits that include `end` but exclude `start`
  * "Give me all commits that lead up to `end` but exclude all commits leading up to `start` (including `start`)"
  * If a `start` or  an `end` is not defined, `HEAD` is assumed

```text
    -A-B-C-D  topic
      /
-T-U-V-X-Y-Z  master
```

* `topic...master` - is commits in `master` that are not in `topic`
  * Since `topic` merged in `master` at `V`, it includes `T`, `U`, `V`, etc.
  * So only commits in `master` not in `topic` are `X`, `Y`, and `Z`

```text
-A-B-C-D  topic
        \
-T-U-V-X-Y-Z  master
```

* `topic` merges into `master`
* `topic..master` is `X`, and prior commits

```text
  A-B-C-D  topic
 /       \
T-U-V-X-Y-Z  master
```

* In the case where `topic` branches off `master` and then is merged back in, `topic..master` is `U` to `Y`
  * Another way to think about it is all _unreachable_ commits from `topic`
  * "Reachability" being the to the left or back over the graph

### Triple dots

* `A...B` represents symmetric difference
* The commits reachable from `A` or `B` but not from both

```text
  A-B-C-D-E-F-G  topic
 /       \
T-U-V-X-Y-Z-A1-A2-A3  master
```

* `master...topic`
  * `A3`, `A2`, `A1`, `Y`, `X`, `V`, `U`, `G`, `F`, `E`
