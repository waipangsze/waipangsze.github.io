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

## Actions

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

## 選擇文字

在bash中選擇文字本不是一件難事，但是到了tmux中，情況會稍有複雜。如果一個window裡面有多個pane，普通的選擇是會橫跨並排的pane的，這讓複製文字變得困難了起來。那麼如何解決呢？答案是：使用Alt。利用Alt+滑鼠框選，我們可以控制選擇的文字範圍，就可以實現選擇單一pane裡的文字啦。 如果在tmux中啟用滑鼠模式(tmux set mouse on)的話，會發現無法直接透過滑鼠來選擇複製文字，這個時候可以用過Shift+滑鼠選擇來選擇文字。 不過對於有多個pane的窗口，選擇一個pane裡的文字就不能簡單透過Shift+滑鼠選擇來完成了，因為這樣可能會選取多個pane裡的文字。 那麼如何來實現只選擇單一pane裡的文字呢？方法很簡單，只需要多按一個鍵即可： 

- `Shift + Alt + 滑鼠框選`
  - copy: `Ctrl + Shift + C`
  - paste: `Ctrl + Shift + V`