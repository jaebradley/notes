# [How To Use Top, Netstat, Du, & Other Tools to Monitor Server Resources](https://www.digitalocean.com/community/tutorials/how-to-use-top-netstat-du-other-tools-to-monitor-server-resources#process)

# `netstat`

* `netstat` prints list of open sockets
* `netstat -a` lists all ports (listening and non-listening)
* `-t` flag will filter on `TCP` connections
* `-u` flag will filter on `UDP` connections
* `-s` flag displays statistics

# `df`

* `-h` for human-readable

# `du`

* Breakdown by directory (for the given directory)
* `-h` for human-readable
* `-a` to see size + directory
* `-c` to see total at bottom of output
* ` -s` to see total and omits specific items
