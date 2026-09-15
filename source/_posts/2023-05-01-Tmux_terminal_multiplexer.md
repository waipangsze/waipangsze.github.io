---
layout: post
title: "Tmux terminal multiplexer"
categories: [Linux]
tags: [HPC,terminal,tmux]
author: wpsze
index_img: 
banner_img: 
---

# Tmux terminal multiplexer

**tmux** is a terminal multiplexer: it enables a number of terminals to be created, accessed, and controlled from a single screen. tmux may be detached from a screen and continue running in the background, then later reattached.

This release runs on OpenBSD, FreeBSD, NetBSD, Linux, macOS and Solaris.

[Git tmux](https://github.com/tmux/tmux)

## Dependencies
- tmux depends on **libevent 2.x**, available from [this page](https://github.com/libevent/libevent/releases/latest).
- It also depends on **ncurses**, available from [this page](https://invisible-mirror.net/archives/ncurses/).
To build tmux, a C compiler (for example gcc or clang), make, pkg-config and a suitable yacc (yacc or bison) are needed.

## Installation

```sh
git clone https://github.com/tmux/tmux.git
cd tmux
sh autogen.sh
./configure && make
```

or 

```sh
micromamba install conda-forge::tmux
```

## Edit .tmux.conf

```sh
# wpsze: apply Default
# # prefix setting (screen-like)
# set -g prefix C-a
# unbind C-b
# bind C-a send-prefix

set -g mouse on
set -g set-clipboard on

bind | split-window -h
bind - split-window -v

# History-limit
set -g history-limit 30000

bind -r ^Up select-pane -U
bind -r ^Down select-pane -D
bind -r ^Left select-pane -L
bind -r ^Right select-pane -R

set -g default-terminal "xterm-256color"

setw -g mode-keys         vi    # 进入复制模式的时候使用 vi 键位（默认是 EMACS）

# Copy mouse selections to Linux system clipboard
bind-key -T copy-mode-vi MouseScreenDragEnd1Pane send-keys -X copy-pipe-and-cancel "xclip -selection clipboard -i"
```

## Usage

```sh
# Add new seession
tmux new -s <session-name>

# Detach
tmux detach # or Ctrl + B then d

# List all sessions
tmux ls

# Attach a session
tmux a -t <session-name>

# rename session, 
tmux rename-session -t <old-session-name> <new-session-name>

# Reload the current Tmux configuration
tmux source-file ~/.tmux.conf
```

## Actions

```sh
# Move bewteen panes
C trl + B (up,down,lef,right)

# the window into two panes horizontally.
Ctrl + B —

# Split the window into two panes vertically.
Ctrl + B |

# Ctrl+B X — Close pane.
Ctrl + B X
```

## copy text

If you prefer using your mouse to highlight and copy text just like a normal terminal, you can turn on mouse mode.

1. Open or create your tmux configuration file: vim ~/.tmux.conf
2. Add the following line:
   1. `set -g mouse on`
3. Save the file, and reload your tmux environment inside the terminal:
   1. `tmux source ~/.tmux.conf`
4. Enter Copy Mode: Press `Ctrl + b`, then release and press `[`. (The screen will freeze, and a line counter will appear in the top right).
5. Simply click and drag your mouse over the text.
6. You can still paste it using `Ctrl + b` followed by `]`
