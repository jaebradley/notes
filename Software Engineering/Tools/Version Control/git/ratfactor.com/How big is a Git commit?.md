# [How big is a Git commit?](https://ratfactor.com/cards/git-commit-size)
* Git stores git objects as zlib-compressed data
* Git eventually turns object files into even more efficiently-compressed packfiles

| Change | Size (KB) |
|--------|-----------|
| `git init` | `~65kb` |
| Committing `250` tiny files | `~48kb` |
| Committing a `3 byte` change to a file in a directory of `50 ` files | `~17kb`
