# [Why pipes sometimes get "stuck": buffering](https://jvns.ca/blog/2024/11/29/why-pipes-get-stuck-buffering/)

* Writing all output immediately executes more system calls
* More efficient to buffer the data until 8KB or so of the data exists and then write to the pipe

```
tail -f /some/log/file | grep thing1 | grep thing2
# grep thing 1 saves 8KB of data to write until it pipes to grep thing2
```

* `grep` handles buffering changes depending on whether its writing to a terminal or not
* If stdout is a terminal, uses line buffering (printing every line immediately)
* If stdout is not a terminal, uses block buffering, only print data if 8KB of data to print
* `BUFSIZ` variable in libc that determines buffer size
* `tail`, `cat`, `tee` don’t buffer

## Common Commands and their flags that disable buffering

* `grep --line-buffered`
* `sed -u`
* `awk fflush function`
* `tcpdump -l`
* `jq -u`
* `tr -u`
* `cut` (no way to disable buffering)
