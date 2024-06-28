# [Vim anti-patterns](https://blog.sanctum.geek.nz/vim-anti-patterns/)

* `*` or `#` to search for word under cursor
* Use `c` to “delete” then enter insert mode `c2w` vs. `d2wi`
* Remap some key combination to Escape (like `ctrl` + `[` or `jj`)
* `I` and `A` to move to start and end of line and insert
* `@:` for searches or `n/N`
* `&` to repeat substitution for th current line. `g&` to repeat substitution for all lines
