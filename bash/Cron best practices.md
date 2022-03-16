# [Cron best practices](https://blog.sanctum.geek.nz/cron-best-practices/)

## Apply the principle of least privilege

* Run the task as a user with only the privileges it needs to run
* Create a dedicated system user purely for running scheduled tasks relevant to your application
* For example, for database systems like MySQL, don't use the administrative `root` user, but instead create a dedicated user with only the necessary permissions

## Test the tasks

* test the task on the command line as the user configured to run the task (`sudo -i -u cronuser`)
* Once the task works on the command line place it in the `crontab` file with the timing settings modified to run the task a few minutes later to check if the task ran without errors and completed properly
* Tail the `/var/log/syslog` to check for errors
* Also tests if `crontab` is well-formed

## Don't throw away errors or useful output

* Something like `curl X >/dev/null 2>&1` will ignore the output completely
* If there is a `500` or redirection there would be no indication as the output would contain that information
* `curl -fLsS -o /dev/null X` will emit an error if the HTTP response code is an error, follow redirections, will not show progress meter, and send the standard output (the actual page) to `/dev/null`

## Put the tasks in their own shell script file

* If you're running cron as a non-system user and can't add scripts into a system bindir one workaround is to create a bindir at something like ~/.local/bin`
* Make sure the scripts in the bindir are `chmod +x` and include the directory in the `PATH` environment variable in the `crontab`

## Send the output somewhere useful

* Set a useful `MAILTO` (if necessary)
* Or use the `logger` tool
* Author sends `stderr` output via `MAILTO`
* If you don't want to use `syslog` set up a log file like `/var/log/cron-task.log` but don't forget to `logrotate` it

## Include a timeout

* `cron` will allow tasks to run indefinitely, which is not ideal
* Can use the `timeout` command wrapper

## Include file locking to prevent overruns

* `cron` will start a new process regardless of if previous runs have completed
* Can use `flock` wrapper to set an exclusive lockfile in order to prevent the task from running more than one instance in parallel
