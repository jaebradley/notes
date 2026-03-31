# [**Conditionally setting your gitconfig**](https://utf9k.net/blog/conditional-gitconfig/)
* Example is that work code lives in `$HOME/work` and personal code lives in `$HOME/projects`
```
[user]
  email = user@example.com
  name = User
  signingkey = ABC123
[commit]
  gpgSign = true
[includeIf "gitdir:~/work/"]
  path = ~/.work.gitconfig
```
* There are other [options,](https://git-scm.com/docs/git-config#\_conditional_includes) like enabling certain settings with certain branch names
