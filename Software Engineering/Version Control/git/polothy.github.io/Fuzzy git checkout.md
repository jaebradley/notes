# [Fuzzy git checkout](https://polothy.github.io/post/2019-08-19-fzf-git-checkout/)
```zsh
fzf-git-branch() {
    git rev-parse HEAD > /dev/null 2>&1 || return

    git branch --color=always --all --sort=-committerdate |
        grep -v HEAD |
        fzf --height 50% --ansi --no-multi --preview-window right:65% \
            --preview 'git log -n 50 --color=always --date=short --pretty="format:%C(auto)%cd %h%d %s" $(sed "s/.* //" <<< {})' |
        sed "s/.* //"
}
```
* `git rev-parse HEAD > /dev/null 2>&1 || return` check to see if the current directory is in a git repository
* `git branch --color=always --all --sort=-committerdate`: list all branches, and sort them by commit date where the most recently committed branches are at the bottom
* `grep -v HEAD`: filter out the `HEAD` branch
* `fzf` reads the branch names from standard input
  * `fzf` preview is running `git log` on the selected branch with formatting
* After selecting a branch from `fzf`, the branch name is filtered through `sed` to remove the asterisk and leading whitespace
* Above function prints the branch, but really, you want to checkout the specified branch
```zsh
fzf-git-checkout() {
    git rev-parse HEAD > /dev/null 2>&1 || return

    local branch

    branch=$(fzf-git-branch)
    if [[ "$branch" = "" ]]; then
        echo "No branch selected."
        return
    fi

    # If branch name starts with 'remotes/' then it is a remote branch. By
    # using --track and a remote branch name, it is the same as:
    # git checkout -b branchName --track origin/branchName
    if [[ "$branch" = 'remotes/'* ]]; then
        git checkout --track $branch
    else
        git checkout $branch;
    fi
}
```
* Note that selecting a remote branch attempts to checkout the branch with the `--track` option
  * The `--track` option will exit with a non-zero exit code if the branch already exists locally - most likely want to checkout the local branch and update it from the remote branch
