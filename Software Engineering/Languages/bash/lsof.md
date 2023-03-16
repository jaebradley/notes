# [`lsof`](https://medium.com/@copyconstruct/lsof-f2b224eee7b5)

* **l**ists **a**ll **o**pen **f**iles

## Options

* `-u` narrows down to a specific user
  * `lsof -u cindy` is files opened by a specific user
  * Can also negate with a `^` like `lsof -u^cindy`
* `-c` lists all files open by processes executing specified command
  * `lsof -cpython` lists all files open by Python processes
* `-p` allows the specification of a process id
  * `lsof -p some-process-id` lists all files open by the specified process
* `-i` allows ability to match specified internet address
  * If no internet address is specified, selects _all_ Internet and network files
  * `lsof -i -a -u $USER | grep Slack` - find all TCP connections Slack has open
  * `lsof -iUDP` - UDP connections
  * `lsof -i 6` - all IPv6 connections
* `-t` suppresses all information except PID
  * Useful when piping to another function (`kill -9`) or file
* Generally `lsof` will `OR` results together - specifying `-a` will give an `AND` of the results

