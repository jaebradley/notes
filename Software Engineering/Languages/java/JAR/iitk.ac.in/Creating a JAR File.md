# [Creating a JAR File](https://www.iitk.ac.in/esc101/05Aug/tutorial/jar/basics/build.html)
* Basic format for creating a JAR file looks like `jar cf jar-file input-file(s)`
  * `c` option indicates the intent to create a JAR file
  * `f` option indicates redirecting output to the specified file vs. stdout
* Example is a `TicTacToe` applet from the JDK demos
* Demo contains bytecode class files, audio files, and images that are stored in a `TicTacToe` directory
* `audio` and `images` subdirectories contain sound files and GIF images
* To package this demo applet into a single JAR file named `TicTacToe.jar` execute the following command within the `TicTacToe` directory: `jar cvf TicTacToe.jar TicTacToe.class audio images`
* Since `audio` and `images` are directories, the `jar` program will recursively place them and their contents in the JAR file
```bash
adding: TicTacToe.class (in=3825) (out=2222) (deflated 41%)
adding: audio/ (in=0) (out=0) (stored 0%)
adding: audio/beep.au (in=4032) (out=3572) (deflated 11%)
adding: audio/ding.au (in=2566) (out=2055) (deflated 19%)
adding: audio/return.au (in=6558) (out=4401) (deflated 32%)
adding: audio/yahoo1.au (in=7834) (out=6985) (deflated 10%)
adding: audio/yahoo2.au (in=7463) (out=4607) (deflated 38%)
adding: images/ (in=0) (out=0) (stored 0%)
adding: images/cross.gif (in=157) (out=160) (deflated -1%)
adding: images/not.gif (in=158) (out=161) (deflated -1%)
```
* The `jar` program compresses by default
  * To turn off compression, specify the `0` option like `jar cvf0`
  * Might want to avoid compression to increase loading a JAR file - this is balanced by an increase in the time it might take to download an uncompressed (larger) JAR file
* The `jar` program automatically adds a manifest file to the JAR archive with the pathname `META-INF/MANIFEST.MF`
* The `-C` option allows the creation of a JAR file where the relative paths are not preserved
  * So for example, if it was desired that the `audio` and `images` files are no longer nested and instead located at the top level, this could be accomplished via `jar cf ImageAudio.jar -C images . -C audio .`
```bash
META-INF/MANIFEST.MF
cross.gif
not.gif
beep.au
ding.au
return.au
yahoo1.au
yahoo2.au
```