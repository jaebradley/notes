# `spawn` `npm install` and Display Output

You can [`spawn`](https://nodejs.org/api/child_process.html#child_process_child_process_spawn_command_args_options) a command, like `npm install --save-dev eslint` by doing something like

```bash
spawn('npm', [ 'install', '--save-dev', 'eslint' ]);
```

However, this doesn't have "live" output (i.e. `stdout`) - `stdout` will print after the command executes.

Turns out you can do something like

```bash
spawn('npm', [ 'install', '--save-dev', 'eslint' ], { stdio: 'inherit' });
```

Which will use the `inherit` option of [the `node` `stdio` values](https://nodejs.org/api/child_process.html#child_process_options_stdio), which is also equivalent to `[process.stdin, process.stdout, process.stderr]`.

