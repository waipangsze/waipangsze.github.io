---
layout: post
title: "Git cherry-pick"
categories: [Git]
tags: [git cherry-pick]
mathjax: true
author: wpsze
---

# Git cherry-pick

After switching to the master, use the cherry-pick command to take out the submission of "adding commit instructions", and then add it to the master.

```sh
$ git checkout master
Switched to branch 'master'
$ git cherry-pick 99daed2
error: could not apply 99daed2... commit
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
```
If a conflict occurs, please open sample.txt, modify the conflicting part and then submit.

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
