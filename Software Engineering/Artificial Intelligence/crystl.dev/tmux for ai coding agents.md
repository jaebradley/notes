# [tmux for ai coding agents](https://crystl.dev/guides/tmux-for-ai-agents/)

## Why tmux and agents go together
* `tmux` survives disconnection
* `tmux` can hold many sessions within a `tmux` window
  * `n` agents can become `n` named `tmux` sessions
* Claude Code has a `--tmux` flag (combined with the `--worktree` flag)

## A session per project, named by project
* Organization rule: one `tmux` session per repo, where the session is named after the repo

```
# attach to it (creates it if it doesn't exist)
tmux new-session -A -s api -c ~/code/api
```

* Can also format `tmux ls / list-sessions` like `tmux list-sessions -F '#{session_name}  windows=#{session_windows}  attached=#{session_attached}'`

 ## A layout for watching several agents
* Inside a session, give each agent its own window
  * Each window is named after a task
  * Author considers windows superior to panes for agents that one is not actively monitoring because windows do not cost any screen space
  * In contrast, panes should be for the two/three agents that one wants to monitor side-by-side
* Author has two different layouts
  * A `tiled` layout for an even grid of panes
  * A `main-vertical` layout with a larger pane for the primary pane and then smaller panes for secondary work

 ## Example `tmux.conf`
 ```tmux
# agents produce a lot of output, and you will want to scroll back through it
set -g history-limit 100000

# 24-bit colour, so agent TUIs render the way they were designed to
set -g default-terminal "tmux-256color"
set -ga terminal-overrides ",*256col*:Tc"

# scroll and click between panes with the mouse
set -g mouse on

# don't let a running process rename the window out from under you
set -g allow-rename off
set-window-option -g automatic-rename off

# 1-indexed windows match the number keys on your keyboard
set -g base-index 1
set-window-option -g pane-base-index 1
set -g renumber-windows on

# flag windows whose agent produced output while you were elsewhere
set-window-option -g monitor-activity on
set -g visual-activity off

# status line refreshes often enough to be a monitor
set -g status-interval 5

# no delay when an agent's TUI reads an escape sequence
set -sg escape-time 10

# jump panes without the prefix key
bind -n M-h select-pane -L
bind -n M-j select-pane -D
bind -n M-k select-pane -U
bind -n M-l select-pane -R

# reload without restarting the server
bind r source-file ~/.tmux.conf \; display-message "config reloaded"
```

## Driving an agent with send-keys
* `send-keys` types into a pane, and is used to script an agent from another source (like a cron, `git` hook, another agent, etc)
* `# target syntax: session:window.pane`
 * `tmux send-keys -t api:auth "run the test suite and fix what fails" Enter`
 * `Enter`, `Escape`, `Space`, etc are special "keys" and not text
* If a prompt contains one of these words, it will get sent as a keypress
* Instead, use the `-l` argument to send a literal string
* A trailing `;` read as a command separator and should be escaped like `\;`
* Many Agentic `TUI`s debounce or re-render on paste, and an `Enter` arriving in the same frame as the text gets swallowed
* Adding a delay via `sleep` is often a reliable pattern
* ```bash
  tmux send-keys -t api:auth -l "$PROMPT"
  sleep 0.3
  tmux send-keys -t api:auth Enter
  ```

## Reading an agent back with capture-pane
* `capture-pane` dumps a pane's contents to `stdout`
* ```bash
  # the visible screen
  tmux capture-pane -p -t api:auth
  
  # the last 200 lines, with wrapped lines joined back together
  tmux capture-pane -p -J -S -200 -t api:auth
  
  # the entire scrollback
  tmux capture-pane -p -J -S - -t api:auth
  
  # keep the colour escape sequences
  tmux capture-pane -p -e -t api:auth
  ```
* The `-J` is important - without it, a line that wrapped the pane's width comes back as two lines
* `pipe-pane` streams everything a pane produces to a command like
* `tmux pipe-pane -o -t api:auth 'cat >> ~/agent-logs/api-auth.log'`
* `-o` toggles, so running the `pipe-pane` command again, turns off the stream
* Output is raw text, including escape sequences

## The status-line monitor

```tmux
set -g status-interval 5
set -g status-right '#(~/bin/tmux-agent-status) | %H:%M'
set -g status-right-length 100
```

```bash
#!/usr/bin/env bash
# ~/bin/tmux-agent-status
out=""
while read -r target; do
  screen="$(tmux capture-pane -p -J -S -8 -t "$target" 2>/dev/null)"
  name="${target%%:*}"
  case "$screen" in
    *"Do you want"*|*"❯ 1."*) out+="⏸ $name " ;;   # parked on a prompt
    *"esc to interrupt"*)      out+="● $name " ;;   # working
    *)                         out+="○ $name " ;;   # idle
  esac
done < <(tmux list-panes -a -F '#{session_name}:#{window_index}.#{pane_index}')
printf '%s' "$out"
```

* Lighter-weight version is using `monitor-silence` to alert when a pane produces no output for `N` seconds
* ```tmux
  set-window-option -g monitor-silence 30
  set -g visual-silence on
  set-hook -g alert-silence 'run-shell "osascript -e \"display notification \\\"agent idle\\\"\""'

  ```
