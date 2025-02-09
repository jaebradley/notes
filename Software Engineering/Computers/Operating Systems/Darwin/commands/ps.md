# ps
* Returned data is the process ID, terminal associated with the process, CPU time used by the process, command that started the process
* `ps aux` displays all running processes across all users
* `px aux | less` to page through the output of `ps`

## [Options](https://ss64.com/mac/ps.html)
* `-c` `Command` column just contains executable name vs. full command line instruction
* `-a` displays information about other users’ processes
* `-f` “full” output like user ID
* `-r` sort by current CPU usage
* `-p` displays information associated with the specified process IDs
* `-o` and specify comma/space-separated keywords (commonly-used ones below)

## [Keywords](https://ss64.com/mac/ps_keywords.html)
* `%cpu` / `pcpu`
* `%mem` / `pmem`
* `rss` - real memory (resident set) size of the process in `1024` byte units
* `state` - first letter returned indicates the run state of the process
  * `D` - disk or other uninterruptible wait (legacy)
  * `I` - idle process (sleeping for > 20 seconds)
  * `R` - runnable process
  * `S` - process sleeping less than 20 seconds
  * `T` - stopped process
  * `U` - process in uninterruptible wait
  * `Z` - zombie dead process
* `inblock` - total blocks read
* `majflt` - total page faults
* `minflt` - total page reclaims
* `nswap` - total swaps in/out
* `nivcsw` - total involuntary context switches
* `nvcsw` - total voluntary context switches
* `wq` - total number of workqueue threads
* `wqb` - blocked workqueue threads
