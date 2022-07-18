# [Safe ways to do things in bash](https://github.com/anordal/shellharden/blob/master/how_to_do_things_safely_in_bash.md)

## `Splitting $string on the separator $sep into $array:`

```bash
# Bad
IFS="$sep"
array=($string)

# Good
array=()
while read -rd "$sep" i; do
    array+=("$i")
done < <(printf '%s%s' "$string" "$sep")

# Equivlant in Bash 4
readarray -td "$sep" array < <(printf '%s%s' "$string" "$sep")
```

* Works for any separator byte except `NUL` (hardcode literal `$'\0'` in place of `$sep`

## `Assert that command dependencies are installed`

```bash
require(){ hash "$@" || exit 127; }
require …
require …
require …
```

* [`hash` command is used to detect if a command is available on the path and keeps track of the number of times various commands are called](https://unix.stackexchange.com/questions/86012/what-is-the-purpose-of-the-hash-command)
* Benefits of using `hash` over `command` or `which` is that it gives you an error message in the failure case

## Builtins like `local` and `export` are commands

```bash
# This will not fail since the local command will "succeed"
set -e # Fail if nproc is not installed
local jobs="$(nproc)"
make -j"$jobs"

# Need to separate declaration from assignment
set -e # Fail if nproc is not installed
local jobs
jobs="$(nproc)"
make -j"$jobs"
```

## How to check if a variable exists

* `[[ -v var ]]`

## printf > echo

* GNU version of echo does not support option parsing
* Unlike the POSIX version, the GNU version takes options but offers no way to suppress further option parsing
  * Interprets any number of leading arguments as options until the first argument that is not an option
  * We can not just print anything unpredicatble like a variable or command substituion (which might start with a dash and look like an option), we must first pring some literal character that is not the dash
