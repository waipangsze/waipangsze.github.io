---
layout: post
title: "Tmux terminal multiplexer"
categories: [Linux]
tags: [HPC,terminal,tmux]
author: wpsze
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

## Edit .tmux.conf

```sh
# prefix setting (screen-like)
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Use the mouse
set -g mouse on

bind | split-window -h
bind - split-window -v

# History-limit
set -g history-limit 30000

bind -r ^Up select-pane -U
bind -r ^Down select-pane -D
bind -r ^Left select-pane -L
bind -r ^Right select-pane -R

set -g default-terminal "xterm-256color"
```

## Usage

```sh
# Add new seession
tmux new -s <session-name>

# Detach
tmux detach

# List all sessions
tmux ls

# Attach a session
tmux a -t <session-name>

# rename session, 
tmux rename-session -t <old-session-name> <new-session-name>

# Reload the current Tmux configuration
tmux source-file ~/.tmux.conf
```

Actions
```sh
# Move bewteen panes
C trl + A (up,down,lef,right)

# the window into two panes horizontally.
Ctrl + A —

# Split the window into two panes vertically.
Ctrl + A |

# Ctrl+B X — Close pane.
Ctrl + A X
```

