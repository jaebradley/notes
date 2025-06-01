# [TIL: timeout in Bash scripts](https://heitorpb.github.io/bla/timeout/)
* Can use Bash built-in program `until` to "loop" where the program is `sleep`ed until it succeeds
  * Downside is `sleep`ing forever if the `until` condition will never succeed
* `timeout` built-in program takes a time limit and will terminate the command with a non-zero error code if the command has not exited within that time limit
* `timeout 1s sleep 5` will send a `SIGTERM` signal to `sleep` after 1 second
* `timeout` expects a terminatable command and `until` is not terminatable
* Wrap the `until` in a Bash process and then terminate the process via the `timeout`
  * This can be done using `bash -c "until {command}"` or moving the `until` to a script file
