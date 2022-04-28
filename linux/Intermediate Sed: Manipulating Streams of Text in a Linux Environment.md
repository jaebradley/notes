# [Intermediate Sed: Manipulating Streams of Text in a Linux Environment](https://www.digitalocean.com/community/tutorials/intermediate-sed-manipulating-streams-of-text-in-a-linux-environment)

## Supplying Multiple Editing Sequences

* Can do something like `sed 's/and/\&/' somefile.txt | sed 's/people/horses/'`
  * But simpler to do something like `sed -e 's/and/\&/' -e 's/people/horses/'`
  * Can also use `;` separator that will perform the same as the `-e` flag like `sed 's/and/\&/;s/people/horses/' somefile.txt`
* The `=` operation inserts a line-number on a new line between each existing line
* The `G` command, by itself, inserts a blank line between each line (`sed 'G' somefile.txt`)
* Can combine the `=` and `G` like `sed '=;G' somefile.txt` which will result in line-number, then line, then blank line because the `=` operator modifies the actual output stream directly and means you cannot use the result for more editing
* To get the effect of line-number, blank line, then line, would need to `sed '=' somefile.txt | sed 'G'`

## Advanced Addressing

* Can use regular expressions to match only lines that contain a certain pattern
* Place the match pattern between two forward slashes before giving the command strings
* `sed '/singing/s/it/& loudly/' somefile.txt` - replace `it` with `loudly` on the first occurrence of `it` on every line that contains the string `singing`
* `sed '/^$/d' somefile.txt` - matches any blank lines and passes them to the delete command

## Using the Hold Buffer

* `h` copies the current pattern buffer / current line you're matched / working on into the holding buffer, erasing the prevbious contents of the hold buffer
* `H` appends the current pattern buffer to the current holding pattern, separated by a newline character
* `g` copies the current holding buffer into the current pattern buffer, erasing the previous pattern buffer
* `G` appends the current holding pattern to the end of the current pattern buffer, separated by the new-line character
* `x` swaps the current pattern and the holding buffer
* `sed -n '1~2h;2-2{H;g;s/\n/ /;p}' somefile.txt`
  * `-n` is used to suppress automatic printing so `sed` will only print when told to
  * `1\~2h` => `1\~2` means to perform the subsequent operation on the first line and every other line afterwards and `h` means to copy the matched line into the holding buffer
  * `2~2{H;g;s/\n/ /;p}`
    * `2~2` refers to the even-numbered lines
    * The rest of the command is enclosed in braces, meaning that the rest of the command will inherit the address that was just specified (i.e. the even-numbered lines)
    * Without the braces, only the `H` command would inherit the address and the rest of the commands would be executed on every line
    * The `H` command copies a newline, followed by current pattern buffer, onto the end of the current holding pattern
    * The holding pattern is then an odd numbered line, followed by a newline, followed by an even numbered line and is copied back into the pattern buffer, replacing the existing pattern buffer via the `g` command
    * Newline is replaced with a space, and the `p` command tells sed to pring the line
    * The `N` command via `sed -n 'N;s/\n/ /p' somefile.txt` would produce the same result
* Can create a `sed` script comprised of `sed` commands (i.e. the part between single-quotes) and execute the script like `sed -f somesedscript somefile.txt`
