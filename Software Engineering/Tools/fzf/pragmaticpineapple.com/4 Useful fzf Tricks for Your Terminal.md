# [4 Useful fzf Tricks for Your Terminal](https://pragmaticpineapple.com/four-useful-fzf-tricks-for-your-terminal/)

* Fuzzily look up directories using `Option` + `C`
* Fuzzily look through command history using `Control` + `R` 
* Fuzzy autocompletion using `<command>` + `**` + `Tab`
  * The `**` can be changed by setting the `FZF_COMPLETION_TRIGGER` variable
* `fzf` has a preview feature, which allows previewing files before opening them
  * Pass the `--preview` argument to `fzf` 
    * Basic `--preview` would be something like `--preview 'cat {}'`
  * Author creates a directory preview using the `tree` program

```zsh
_fzf_comprun() {
  local command=$1
  shift

  case "$command" in
    cd)           fzf "$@" --preview 'tree -C {} | head -200' ;;
    *)            fzf "$@" ;;
  esac
}
```
