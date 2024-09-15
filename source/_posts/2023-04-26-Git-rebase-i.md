---
layout: post
title: "Git rebase -i "
categories: [Git]
tags: [git rebase]
author: wpsze
---

# Use rebase -i to merge commits

> Ref: https://backlog.com/git-tutorial/tw/stepup/stepup7_5.html

To merge past commits, use the rebase -i command

> $ git rebase -i HEAD~~

The default text editor will open automatically, and you will see commits from HEAD to HEAD~~

```sh
pick 9a54fd4 add commit instructions
pick 0d4a808 Add instructions for pull

# Rebase 326fc9f..0d4a808 onto d286baa
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```

Change the "pick" in the **second line to "squash", save and exit**. Since you need to submit after merging, the editor will remind you to edit the latest submission message, please save and exit after editing the message.
In this way, the two commits are merged into one commit. Please check the history with the log command.

# Amend commits with rebase -i
> Ref: https://backlog.com/git-tutorial/tw/stepup/stepup7_6.html

Use the rebase -i command to select the commits to amend

> $ git rebase -i HEAD~~
> 
The default text editor will open commits from HEAD to HEAD~~

```sh
pick 9a54fd4 add commit instructions
pick 0d4a808 Add instructions for pull

# Rebase 326fc9f..0d4a808 onto d286baa
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```

**Change the text of "pick" in the first line to "edit"**, save and exit. Then, the following content will be displayed, and the commit to be modified will be checked out.

```sh
Stopped at d286baa... add commit
You can amend the commit now, with

        git commit --amend

Once you are satisfied with your changes, run

        git rebase --continue
```

open sample.txt ，make changes.

```sh
$ git add sample.txt
$ git commit --amend
```

You now need to execute "git rebase --continue" to complete the rebase.

> $ git rebase --continue
