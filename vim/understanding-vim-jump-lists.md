# [Understanding Vim's Jump List](https://medium.com/breathe-publication/understanding-vims-jump-list-7e1bfc72cdf0)

* "jumps" are remembered in a "jump list" via `:jumps` command
* Some things that qualify as "jumps"
  * Moving to a line (e.g. `10g`)
  * Moving to next blank line (`}`)
  * Moving to previous blank line (`{`)
  * Moving to beginning of block (`[{`)
  * Moving to beginning of sentence (`(`)
  * Moving to end of sentence (`)`)
  * Moving to beginning of section (`[[`)
  * Moving to end of section (`]]`)
  * Top of window (`H`)
  * Middle of window (`M`)
  * Bottom of window (`L`)
* `CTRL + i` will toggle forwards through jump list
* `CTRL + o` will toggle backwards through jump list
* Move to specific previous jump (e.g. `3g;` is three jumps ago)
  * Execute command in standard mode
* Move to specific forward jump (e.g. `3g,` is three jumps forward)
  * Note the semi-colon on the first case and the comma in the second
* If want `j` and `k` to be added to jump list can update `.vimrc`

```bash
noremap <expr> k (v:count > 1 ? "m'" . v:count : '') . 'gk'
noremap <expr> j (v:count > 1 ? "m'" . v:count : '') . 'gj'
```

