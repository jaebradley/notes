# [How big is a Git commit?](https://ratfactor.com/cards/git-commit-size)
* Git stores git objects as zlib-compressed data
* Git eventually turns object files into even more efficiently-compressed packfiles

| Change | Size (KB) |
|--------|-----------|
| `git init` | `~65kb` |
| Committing `250` tiny files | `~48kb` |
| Committing a `3 byte` change to a file in a directory of `50 ` files | `~17kb` |
| Committing a `3 byte` change to a single file repo | `~9kb` |
| Committing a `~5k4kb` binary file | `~298kb` |
| Committing a `~16mb` source file | `~1.6mb` |

* Kilobytes of overhead for "tiny" commits, whereas larger commits of smaller files are more efficient

## Where is the overhead coming from
* Author has an example of a `3 byte` change that leads to `~17kb` of overhead
* Author audited the updated `.git/*` files and found the following changes
  * Tree file for the updated directory, listing all `50` of the files in the directory
  * Tree file for the root directory of the repo
