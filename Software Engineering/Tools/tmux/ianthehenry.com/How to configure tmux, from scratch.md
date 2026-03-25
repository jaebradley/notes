# [How to configure tmux, from scratch](https://ianthehenry.com/posts/how-to-configure-tmux/)
* Author unbinds all keys at the top of the tmux config, and then bind exactly the ones the author wants
  * `unbind-key -a`
* Author changes the prefix key from `control` + `b` to `alt` + `w` as its possible to press with only the left hand
* Need to also "unbind" other key mappings
  * Previously unbound the "normal" key mapping, but need to unbind the `prefix`, `root`, `copy-mode` and `copy-mode-vi` key tables
* `tmux list-keys` shows keybindings
  * `tmux -f /dev/null list-keys` displays all the default keybindings
  * `-f /dev/null` tells tmux to ignore the `.tmux.conf`
* Reload the tmux configuration by defining `bind M-r source-file ~/.tmux-conf` 
* To decide between the `copy-mode` key table and the `copy-mode-vi` key table, tmux checks the `$EDITOR` environment variable
  * If this environment variable contains the string `vi` then it uses `copy-mode-vi`
  * Explicitly set `vi mode` by defining `set -g mode-keys vi`
* `bind Space copy-mode` is short hand for `bind -T prefix Space copy-mode`
  * This means to enter copy mode, press `<prefix>` + `Space`
  * Could also create a root keybinding to trigger copy mode by doing something like `bind -T root M-Space copy-mode`
  * Once in copy mode, there's one thing to do - `Escape` leaves copy mode
    * `bind -T copy-mode-vi Escape send-keys -X cancel`
    * tmux waits 500ms before it passes escape to the program
    * To avoid this wait, add `set -g escape-time 1`
  * To make space clear the current selection if there is one or start a new selection if there isn't already an active selection: `bind -T copy-mode-vi Space  if -F "#{selection_present}" { send -X clear-selection } { send -X begin-selection }`
* Define cursor up/down/left/right in copy mode to arrow keys / `hjkl`
* Author defines beginning selection in copy mode
  * `v` in `vim` but author defines it using `space` because it is the most common operation
  * Author binds `y` for yanking text, and `enter` for copying and exiting
* In macOS copying might look something like `bind -T copy-mode-vi y send -X copy-pipe 'pbcopy'`
* Don't show tmux status bar: `set -g status off`
* Increase tmux history: `set -g history-limit 50000`

## Mouse Events

* `set -g mouse on`
* Because tmux is a fullscreen application, users can't scroll *through* the output of the shell running inside tmux except by entering copy mode
* `bind -n WheelUpPane copy-mode -e `
  * `copy-mode -e` means "enter copy mode, but automatically exit if the user scrolls past the bottom"

```tmux
bind -T copy-mode-vi WheelUpPane   send -X -N 5 scroll-up
bind -T copy-mode-vi WheelDownPane send -X -N 5 scroll-down
bind -n MouseDrag1Pane copy-mode -M # this means enter copy mode and start mouse selection
bind -T copy-mode-vi MouseDrag1Pane    send -X begin-selection # this lets users change their selection even if they are already in copy mode
bind -T copy-mode-vi MouseDragEnd1Pane send -X copy-selection-no-clear # copies after selection stops
```
