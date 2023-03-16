# [Handy Bash feature: Process Substitution](https://medium.com/@joewalnes/handy-bash-feature-process-substitution-8eb6dce68133)

* Example is `curl`ing two URLs and piping the content into two different files, and then `diff`ing the two files

```bash
curl http://somesite/file1 > file1
curl http://somesite/file2 > file2
diff file1 file2
```

* In this scenario, we need to pipe output from multiple commands into a single "input" command (which is what process substitution does)
* The previous set of three commands can be combined like `diff <(curl http://somesite/file1) <(curl http://somesite/file2)`
  * The structure is `some-command <(another-command)` where `some-command` accepts a file / files and `another-command` writes output to standard output
  * When Bash sees process substitution it'll create temporary file descriptor which it uses as the filename and pipe output from the other process into it
* Process substitution is useful for when you need to feed multiple outputs into a single program, or need to feed ouput from a program into a program that expects input files, but cannot read from stdin, or to replace temporary files
* Bash will automatically parellelize the tasks represented by the process substitution
