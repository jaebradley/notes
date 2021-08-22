# [2.15 Low-Level Character Handling](https://bob.cs.sonoma.edu/IntroCompOrg-RPi/sec-cwrtrd.html)

```c
/* oneChar.c
 * Writes a single character on the screen.
 * 2017-09-29: Bob Plantz
 */

#include <unistd.h>

int main(void)
{
  char aLetter = 'A';
  write(STDOUT_FILENO, &aLetter, 1); // STDOUT_FILENO is
                                     // defined in unistd.h
  return 0;
}
```

* This program allocates one byte of memory as a `char` variable
* `write` function takes a file descriptor
  * The program uses the `STDOUT_FILENO` variable which is defined in the system header file `unistd.h`
  * If is the GNU/Linux file descriptor for standard out (usually the screen)
  * GNU/Linux sees all devices as files
  * file descriptor `0` is standard input (reading from keyboard)
  * file descriptor `2` is standard error (used for writing error messages to the screen)
* There is another program that reads bytes from standard input
  * However, when reading from standard input, the return character is still in the standard input buffer
  * So when the program exits, the shell program reads the input buffer
  * The result is the same as if the user had pressed the return key in response to the shell prompt (after the program exited)
  * Another execution of this program left the characters `bc` in the standard input buffer
  * So the shell program read `bc` which was a program installed on the author's computer, and the shell program executed the program `bc` as a result of what was in the input buffer
* The main lesson is that you need to design programs that correctly will interpret each byte that is written to the screen or read from the keyboard
