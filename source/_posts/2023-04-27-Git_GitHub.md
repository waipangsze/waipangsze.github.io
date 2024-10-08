---
layout: post
title: "Git/Github"
categories: [Git]
tags: [Github,git init, git add, git commit, git conflicts]
author: wpsze
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Git_session.svg/1920px-Git_session.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Git_session.svg/1920px-Git_session.svg.png
---

# Create an empty git repo
Git settings will be recorded in the .gitconfig file under the home page of the user's directory. We need to use the config command to set it.

```sh
$ git config --global user.name "<username>"
$ git config --global user.email "<email>"
```

Follow this command, you can set the color of Git's output (output) results.
```sh
$ git config --global color.ui auto
```

change editor,
```sh
$ git config --global core.editor "vim"
```

Follow the steps below to set the newly created tutorial directory as a Git database
```sh
$ mkdir tutorial
$ cd tutorial
$ git init

## Initialized empty Git repository in /Users/yourname/Desktop/tutorial/.git/
$ git status
$ git add .
$ git commit -m "first commit"
$ git status
$ git log
$ git log --all --graph --oneline
```

# Create branch and swich

```sh
$ git checkout -b iss53
Switched to a new branch "iss53"
This is shorthand for:

$ git branch iss53
$ git checkout iss53
```

## Push to the remote database

```sh
$ git remote add <name> <url>
$ git remote add origin https://xxx/tutorial.git
```
Execute the following command to submit the push to the remote database origin, specify the parameter -u item when executing the submission, and then omit the specified branch name. But when pushing to a blank remote database, you must specify the name of the remote database and branch, which must not be omitted.
During the process, the system will/may ask for user name and password
```sh
$ git push -u origin master
$ git clone <repository> <directory>
```

## Conflicts
Between your last push and the next push, if someone else pushes to update the remote database but you do not update your local database, your push will be rejected. At this time, you need to perform a merge operation to import other people's modification history,otherwise the push will be rejected.
### Resolve conflict
> The upper part separated by == is the editing content of the local database, and the lower part is the editing content of the remote database. After modifying all conflicting places, performing a commit will submit the commit content of the conflicting merge message.

# git clone a given github repo
When the remote github repo is known, You can git clone the remote address first
Then copy the project file to be submitted to the clone folder
```sh
# Add all files, such as submitting only some files requires a single git add file name
git add. 
git commit -m "remarks for submission"

# Force push can overwrite master
git push -u -f origin master
```

If you git **commit --amend** here, then error "Your branch and 'origin/master' have diverged" will reject your git push. The process here is,
1. git clone github repo (latest one with commit ID A)
2. you make something changes
3. $ git commit --amend -m "remarks for submission"
4. The above commit with amend will create a New commit ID and rewrite the original commit ID A. Therefore, it can not reconignate two commit ID's and tell your branch and remote 'origin/master' have diverged. 

The solution is,
```sh
git rebase origin/master # git rebase origin main
# or
git merge origin/master # git merge origin main
```
[WHAT TO DO WHEN GIT BRANCH HAS DIVERGED?](https://poanchen.github.io/blog/2020/09/19/what-to-do-when-git-branch-has-diverged)

# Common Error
## [rejected]        master -> master (non-fast-forward)
> ! [rejected]        master -> master (non-fast-forward)
> error: failed to push some refs to ’’
> To prevent you from losing history, non-fast-forward updates were rejected
> Merge the remote changes (e.g. 'git pull') before pushing again.  See the
> 'Note about fast-forwards' section of 'git push --help' for details.

```sh
$ git pull origin master

# * branch master -> FETCH_HEAD
# CONFLICT (content): Merge conflict in file
# Automatic merge failed; fix conflicts and then commit the result.
# A warning message will appear warning about conflicts during the merge.
```
The conficts like,
> \<<<<<<< HEAD
> commit records the state of the index
> \=======
> pull Get the content of the remote database
> \>>>>>>> 4c0182374230cd6eaa93b30049ef2386264fe12a

Modify the conflicting places, import the modifications of both parties, and delete the redundant marked lines.

> commit records the state of the index
> pull Get the content of the remote database

When the content is modified and the file conflict is resolved, a commit is required.
```sh
$ git add sample.txt
$ git commit -m "merge"
```

# .gitignore file itself is not being ignored

This means that .gitignore is a tracked file.... so you changed it, so it shows up as changed. This would be the expected result... and it makes sense. Do you want to ignore .gitignore? This is not quite a good idea because by tracking it, everyone gets to ignore the same things in the project. Now, if you would like to be the only person in the project to be ignoring the files that you started the question from, you might have to consider adding that to **.git/info/exclude** so that it's only on you and no one else.

```sh
 $ cat .git/info/exclude
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
```