# [Bash best practices](https://bertvv.github.io/cheat-sheets/Bash.html)

* Always use long parameter notation when available (`rm --recursive --force` vs. `rm -rf`)
* Navigate directories in a subshell (`(cd "some directory"; more instructions; cd "previous directory")`) and/or use `pushd` / `popd`
* `nohup some_background_command | cat &`
* Always use `$(cmd)` for command substitution
* Print error messages on `stderr`

```bash
error() {
  printf "${red}!!! %s${reset}\\n" "${*}" 1>&2
}
```

* Describe the usage of each function: number of arguments, return value, output
* Always check for syntax errors by running the script with `bash -n myscript.sh`
* Bash has a debug output feature, which prints each statement after applying all forms of substitution
  * Use `-x` flag like `bash -x myscript.sh`
  * Put `set -x` at top of script / `set -x` and `set +x` around sections of script that you want to debug
