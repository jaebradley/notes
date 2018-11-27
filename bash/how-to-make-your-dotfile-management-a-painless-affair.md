# [How To Make Your Dotfile Management A Painless Affair](https://medium.freecodecamp.org/dive-into-dotfiles-part-2-6321b4a73608)

* Use version control
  * Pushing dotfiles to remote repo allows people to share dotfiles - also provides a good way to back up dotfiles
  * Allows ability to see dotfiles evolve over time
* Symlink dotfiles to home directory
  * The symlinks are essentially aliases to the original files in the dotfiles directory
  * `ln -sv ~/Projects/dotfiles/.bash_profile ~`
  * If changes are made to `~/Projects/dotfiles/.bash_profile` they should be reflected in the home directory
  * There should be a script in dotfiles repo that does this syncing
  * Don't forget to exclude certain files like sync script itself, `.git` directory, `README.md` etc.
* Bootstrap script should
  * Make any relevant directories that you use (like the `~/Projects` directory)
  * Call sync script to sync dotfiles with home directory
  * Install all tools, utilities, languages, etc. that you use
* Author allows for customization by adding this line to end of all main dotfiles
  * `[ -f ".functions.local" ] && source ".functions.local"`
  * It checks to see if a while with the same name (but obviously with a `.local` extension) exists and if it does, to `source` it
  * Allows people to experiment and customize them without having to modify actual VC'd code
