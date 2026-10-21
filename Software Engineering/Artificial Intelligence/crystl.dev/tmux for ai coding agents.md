# [tmux for ai coding agents](https://crystl.dev/guides/tmux-for-ai-agents/)
* `tmux` survives disconnection
* `tmux` can hold many sessions within a `tmux` window
  * `n` agents can become `n` named `tmux` sessions
* Claude Code has a `--tmux` flag (combined with the `--worktree` flag)
* Organization rule: one `tmux` session per repo, where the session is named after the repo

```
# attach to it (creates it if it doesn't exist)
tmux new-session -A -s api -c ~/code/api
```

* Can also format `tmux ls / list-sessions` like `tmux list-sessions -F '#{session_name}  windows=#{session_windows}  attached=#{session_attached}'`
* Inside a session, give each agent its own window
  * Each window is named after a task
  * Author considers windows superior to panes for agents that one is not actively monitoring because windows do not cost any screen space
  * In contrast, panes should be for the two/three agents that one wants to monitor side-by-side
