# tmux

Config file is located in `~/tmux.conf`

```
set -g history-limit 10000
set -g allow-rename off
set-window-option -g mode-keys vi
```

Basic actions:

```
tmux new -s <session_name>
[ctrl+B] = prefix
[prefix] + c = new window
```

Copy and pasting:

```
[prefix] + [ = enter copy mode
? = search
[space] = start copying
[enter] = copy
[prefix] + ] = paste
```
