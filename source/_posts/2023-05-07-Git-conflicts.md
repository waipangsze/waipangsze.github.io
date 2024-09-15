---
layout: post
title: "Git: solve conflicted files"
categories: [Git]
tags: [Github, git commit, git conflicts]
author: wpsze
---

# Git: solve conflicted files

## What's the simplest way to list conflicted files in Git?
```sh
# # Search for all conflicting files.
git diff --name-only --diff-filter=U --relative

# Search for all conflicting files.
grep -lr "<<<<<<< HEAD" ./*

```
## Resolve easy/obvious conflicts
```sh
#At this point you may review each files. If solution is to accept local/our version, run:
git checkout --ours PATH/FILE

#If solution is to accept remote/other-branch version, run:
git checkout --theirs PATH/FILE

#If you have multiple files and you want to accept local/our version, run:
grep -lr '<<<<<<<' . | xargs git checkout --ours

#If you have multiple files and you want to accept remote/other-branch version, run:
grep -lr '<<<<<<<' . | xargs git checkout --theirs
```

```sh
$ git add sample.txt
$ git commit
```
