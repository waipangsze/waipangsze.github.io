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

{% note primary %}
Use the default configuration (Ctrl+B) and apply some useful skills
{% endnote %}

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

# 在 tmux 中使用滑鼠進行文字反白與點擊操作

要在 tmux 中使用滑鼠進行文字反白與點擊操作，首先需要啟用滑鼠模式。

## 1. 啟用滑鼠模式

### 暫時啟用（當前工作階段）

在 tmux 中按下 `Ctrl + b`，然後放開，再輸入 `:` 進入命令列，輸入以下指令並按 Enter：

```text
set -g mouse on
```

### 永久啟用（寫入設定檔）

將設定加到 `~/.tmux.conf` 檔案中：

```text
set -g mouse on
```

然後在 tmux 中重新載入設定，或重啟 tmux：

```bash
tmux source-file ~/.tmux.conf
```

## 2. 滑鼠常用操作與反白複製

啟用滑鼠模式後，你可以直接透過滑鼠進行以下操作：

- **點擊選擇窗格 (Pane)**：用滑鼠左鍵點擊任一分割畫面，即可切換為當前活動窗格。
- **點擊切換視窗 (Window)**：點擊最下方狀態列上的視窗名稱，即可切換視窗。
- **調整窗格大小**：按住並拖曳窗格之間的邊界線。
- **使用滾輪捲動**：向上滾動會自動進入「複製模式 (Copy Mode)」瀏覽歷史輸出；滾動回最底部則會自動離開複製模式。
- **滑鼠反白與複製文字**：
  1. 按住滑鼠左鍵並拖曳來反白選取文字。
  2. 放開滑鼠按鍵：tmux 會自動將選取的文字複製到其內建的剪貼簿中，並退出複製模式。
  3. 貼上文字：按下 `Ctrl + b`，然後按 `]` 即可貼上剛剛複製的內容。

{% note primary %}
- **滑鼠反白與複製文字**：
  1. 按住滑鼠左鍵並拖曳來反白選取文字。
  2. 放開滑鼠按鍵：tmux 會自動將選取的文字複製到其內建的剪貼簿中，並退出複製模式。
  3. 貼上文字：按下 `Ctrl + b`，然後按 `]` 即可貼上剛剛複製的內容。
{% endnote %}

## 3. 如何繞過 tmux 選取系統原生文字？

當 `mouse on` 開啟時，滑鼠事件會由 tmux 攔截，導致你無法直接用終端機本身的右鍵選單或原生反白功能。

- **解法**：在用滑鼠拖曳反白文字時，按住鍵盤的 `Shift` 鍵（在某些終端機如 iTerm2 中可能是 `Option` 鍵），即可暫時繞過 tmux，使用終端機原本的文字選取與右鍵複製/貼上功能。

# To copy mouse selections directly to your Linux system clipboard in tmux 

In `.bashrc`,

- `alias cpwd="pwd | tr -d '\n' | xclip -selection clipboard && echo 'pwd copied to clipboard'"`
