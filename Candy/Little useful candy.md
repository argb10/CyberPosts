## Tmux conf

``` bash
##Join Windows

set -g history-limit 5000

bind-key j command-prompt -p "join pane from:" "join-pane -s '%%'"

bind-key s command-prompt -p "send pane to:" "join-pane -t '%%'"

#Copy mode for the tmux

set -g mouse on

bind -n WheelUpPane if-shell -F -t = "#{mouse_any_flag}" "send-keys -M" "if -Ft= '#{pane_in_mode}' 'send-keys -M' 'select-pane -t=; copy-mode -e; send-keys -M'"

bind -n WheelDownPane select-pane -t= \; send-keys -M

bind -n C-WheelUpPane select-pane -t= \; copy-mode -e \; send-keys -M

bind -T copy-mode-vi C-WheelUpPane send-keys -X halfpage-up

bind -T copy-mode-vi C-WheelDownPane send-keys -X halfpage-down

bind -T copy-mode-emacs C-WheelUpPane send-keys -X halfpage-up

bind -T copy-mode-emacs C-WheelDownPane send-keys -X halfpage-down

# To copy, left click and drag to highlight text in yellow,

# once you release left click yellow text will disappear and will automatically be available in clibboard

# # Use vim keybindings in copy mode

setw -g mode-keys vi

# Update default binding of `Enter` to also use copy-pipe

unbind -T copy-mode-vi Enter

bind-key -T copy-mode-vi Enter send-keys -X copy-pipe-and-cancel "xclip -selection c"

bind-key -T copy-mode-vi MouseDragEnd1Pane send-keys -X copy-pipe-and-cancel "xclip -in -selection clipboard"
```

## Full update alias:
```bash
alias update='sudo apt update -y && sudo apt dist-upgrade -y && sudo apt autoremove -y && sudo apt clean -y'
```

<<<<<<< HEAD
Upgrade terms
---
```bash
which python
python -c 'import pty;pty.spawn("/bin/bash")'
```

Now, if you also want tab auto completion _Ctrl-Z_ to background your shell and type on your box

$ stty raw -echo
=======
## Upgrade pty
Upgrade to a pty (https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/) :

```bash
python -c 'import pty; pty.spawn("/bin/bash")'

CTRL+Z
$ stty raw -echo
hit enter 2 times
$ fg
ener

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows <num> columns <cols>
```
>>>>>>> a6cd39b04c7053fb245bf24e15a8551aac7fbc66
