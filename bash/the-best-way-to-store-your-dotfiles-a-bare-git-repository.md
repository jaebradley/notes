# [The Best Way To Store Your Dotfiles: A Bare Git Repository](https://developer.atlassian.com/blog/2016/02/best-way-to-store-dotfiles-git-bare-repo/)

* Strategy from [@StreakyCobra from HackerNews thread](https://news.ycombinator.com/item?id=11071754)
* Uses a bare git repository (which just tracks changes to source files vs. container working index - GitHub ".git" that people clone from is bare repository)
* Dotfiles are tracked in this bare git repo that's stored in config folder like `$HOME/.config`
* Aliases are added to access this config folder
  * `alias config='/usr/bin/git --git-dir=$HOME/.config/ --work-tree=$HOME'`
    * Note that the path to the repo (`git-dir`) is set to the config location but the working tree path (`work-tree`) is set to the home directory
    * This is probably because the bare repo will be cloned into the home directory
  * `config config --local status.showUntrackedFiles no`
    * The `--local` flag is to hide files that are not being explicitly tracked yet
  * Thus, you can now do `config add some-file.txt` => `config commit -m "I added some file"` => `config push`
* This config folder is then cloned locally, to use as a working copy

## Migration

* Add aforementioned `alias` to `.bashrc` / `.zsh`
* Avoid any recursion issues by git ignoring config folder in home repo by adding `/.config` to global `.gitignore`
* Clone repo into bare repo (`git clone --bare <git-repo-url> $HOME/.config`)
* Checkout bare repo to home directory (`config checkout`)
* Probably going to see a lot of errors that files are going to be overridden
  * Back 'em up or just override that shit
* Hide files using `showUntrackedFiles no` config option
