---
layout: post
title: "Git/Github"
categories: [Git]
tags: [Github, git]
author: wpsze
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Git_session.svg/1920px-Git_session.svg.png
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/f/f4/Git_session.svg/1920px-Git_session.svg.png
---

# Create an empty git repo
Git settings will be recorded in the  `~/.gitconfig` 、`~/.config/git/config` file under the home page of the user's directory. We need to use the config command to set it.

Git 附帶一個名為 `git config` 的工具，讓你能夠取得和設定組態參數。任何倉儲中 Git 資料夾的 config 檔案（位於 `.git/config`）：這個倉儲的專用設定

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

若你想檢查設定值，可使用 `git config --list` 命令列出所有 Git 在目前位置能找到的設定值,
```sh
$ git config --list
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
$ git commit --amend
$ git status
$ git log
$ git log --all --graph --oneline
```

![](https://i.imgur.com/AnlwuNk.png){width=600}

# Create branch and swich

```sh
$ git checkout -b iss53 # 同時建立且切換分支
Switched to a new branch "iss53"
This is shorthand for:

$ git branch iss53
$ git checkout iss53
```

# git push

```sh
$ git remote add <name> <url>
$ git remote add origin https://xxx/tutorial.git
```

Execute the following command to submit the push to the remote database origin, specify the parameter -u item when executing the submission, and then omit the specified branch name. But when pushing to a blank remote database, you must specify the name of the remote database and branch, which must not be omitted.
During the process, the system will/may ask for user name and password

```sh
$ git push -u origin master
$ git push origin HEAD:refs/for/master
$ git clone <repository> <directory>
```

1. git push <遠端主機名稱> <本機分支名稱> : <遠端分支名稱>
   1. 例如 `git push origin master：refs/for/master`
      1. 是將本地的master分支推送到遠端主機origin上的對應master分支
      2. origin 是遠端主機名，
      3. 第一個master是本地分支名，
      4. 第二個master是遠端分支名。
   2. `git push origin master`
      1. 如果遠端分支被省略，如上則表示將本地分支推送到與之存在追蹤關係的遠端分支（通常兩者同名），如果該遠端分支不存在，則會被新建
      2. （例如，如果只是git push origin HEAD，也就是沒有指定遠端分支，則會推送到與目前分支同名的遠端分支，如果遠端沒有這個同名分支，則在遠端程式庫建立一個和目前分支名稱一樣的遠端分支)
    3. ` git push origin HEAD:refs/for/master`
       1. （注意這裡的orgin和HEAD沒什麼關係，比如說：git branch --set-upstream-to=origin/dev dev_local 這裡是origin/dev，中間有個/，意思就是遠端函式庫的dev分支）
       2. git push 肯定是推送
       3. origin : 是遠端的函式庫的名字
       4. HEAD: 是一個特別的指針，它是一個指向你正在工作的本地分支的指針，可以把它當作本地分支的別名，git這樣就可以知道你工作在哪個分支
       5. refs/for :意義在於我們提交程式碼到伺服器之後是需要經過 code review 之後才能進行merge的
       6. refs/heads： 不需要

# git pull

git pull - Fetch from and integrate with another repository or a local branch

```sh
$ git pull --rebase
You are not currently on a branch.
Please specify which branch you want to rebase against.
See git-pull(1) for details.

    git pull <remote> <branch>

$ git branch
master
....

$ git checkout master
Updating files: 100% (43/43), done.
Previous HEAD position was c1d75b7 Merge "CPAS Benchmark System - added GFS support to scorecard"
Switched to branch 'master'
Your branch is up to date with 'origin/master'.

$ git log
$ git pull --rebase
```

# 撤銷修改場景

撤銷修改場景
工作區文件修改了，但還沒進入版本管理，現在不想要了，如何回退、撤銷已有的修改

```
# 建議使用restore指令
git restore file
# 也可以使用checkout
git checkout -- file
```

# stash 暫存修改內容

有時候我們會遇到需臨時修改它分支的狀況，這邊的臨時代表正在開發的項目並不是它分支的內容，比如說 master 分支需做緊急修改，但我們目前正在跑 feature 分支的進度，這時你可能會將 feature 尚未提交成 commit 的修改內容複製到一個空白文件上，以確保修改內容不會不見，最後當 master 分支修改完成時，再將修改內容貼回到 feature 上，這確實也是個辦法，但其實不用這麼麻煩，Git 本身就有專門處理此情況的指令，名為 stash，可將當下分支的修改內容暫存起來，日後有需要再將它取出即可。

我們可使用以下指令將當前分支的修改給暫存下來：

```sh
暫存當前分支修改內容：
git stash
git status #check: working tree clean

暫存當前分支修改內容 (包含未追蹤)：
git stash -u
git status #check: working tree clean
```

我們可使用以下指令查看所有暫存內容：
```sh
git stash list
```

假設你已經完成臨時的任務，此時可使用以下指令**取出暫存內容**：

```sh
將最新暫存套用至當前分支，成功後銷毀暫存 (即 stash@{0})：
git stash pop

將指定暫存套用至當前分支，成功後銷毀暫存：
git stash pop stash@{1}
```

使用 pop 可以把某個 stash 拿出來並套用在目前的分支上，套用完成即銷毀這個暫存內容，如果你**不想銷毀暫存內容**，可改用以下指令：

```sh
將最新暫存套用至當前分支，成功後保留暫存：
git stash apply

將指定暫存套用至當前分支，成功後保留暫存：
git stash apply stash@{1}
```

apply 只會將暫存做套用的動作，並不會銷毀暫存，如果你不想要某個暫存了，可使用以下指令：

```sh
刪除最新暫存 (即 stash@{0})
git stash drop

刪除指定暫存：
git stash drop stash@{1}

刪除全部暫存：
git stash clear
```

# 合併本地多次提交記錄場景

場景：有時開發某一個功能時會提交很多次commit，這樣會顯得版本日誌很亂，尤其是在多人協作的開發場景中。這時可以將多個緊密相關的commit合併成一個。

簡單情況：只是對最新一次的commit進行修正的話，可以使用

```sh
git commit -v --amend
```

更複雜情況：
```sh
git rebase -i HEAD~4 # 合併最近4次提交記錄
# 進入vi編輯模式，依照提示對應commit選擇指令，常用的包括pick,edit,squash,fixup
s cacc52da add: qrcode
s f072ef48 update: indexeddb hack
s 4e84901a feat: add indexedDB floder
p 8f33126c feat: add test2.js
```
合併成功後，git log會發現四個版本資訊變成了一個。

如果異常退出vi編輯窗口，可以執行
```sh
git rebase --edit-todo
#進入編輯視窗繼續編輯儲存
git rebase --continue
```

不要合併已經提交遠端分支的記錄，否則會報錯
```console
error: cannot 'squash' without a previous commit
```

# 拉取主分支最新版本到個人開發分支場景

這在團隊開發中很常見，主分支頻繁更新，個人開發特性分支相比主分支會落後，需要及時更新master主分支最新變動

- 方法一：使用git merge master合併主分支最新變動，但git日誌裡會出現merge資訊。
- 方法二：如果希望保持乾淨的commit，使用git rebase。

```sh
git rebase master
# 如果出現衝突，需要解決衝突，然後git add
git add something
git rebase --continue

# 後悔選項，終止rebase操作
git rebase --abort
```

# 版本回滾場景

理解GIT三種模式：工作區、暫存區、版本庫

- 場景1：工作目錄程式碼檔案修改完，但還未 `git add` 納入了暫存區
  - 使用 `git checkout .` 使用暫存區文件覆蓋工作區文件
- 場景2：已經執行 `git add` 納入暫存區，但還沒提交commit到版本庫。
  - `git reset` #先用HEAD指標覆蓋目前的暫存區內容
  - `git checkout .` #再用暫存區內容覆蓋工作區內容
  - `git reset --hard` #直接使用HEAD覆蓋目前暫存區和工作區
- 場景3：已經 commit 提交到本地版本記錄中，但還沒有推送到 git push 遠端倉庫.
  - `git reset --hard <last_commit_id>` #覆蓋本機版本庫、暫存區和工作區
- 場景4：已經 `git push` 推送到遠端倉庫 
  - `git reset --hard <commit_id>`
  - `git push origin <remote_branch> --forc`e

# 誤在主分支開發場景

專案規定develop分支為開發主分支，成員不能直接在該分支上修改提交，必須建立類似feature/xxx特性分支進行開發。但有時候拉取版本後忘了創建特性分支，直接在develop分支修改。此時如何處理？

```sh
# 暫存develop分支修改內容
git stash
# 然後建立特性分支
git checkout -b new_branch # # 同時建立且切換分支
# 將暫存修改放到新分支中
git stash pop
# 此時可以正常使用git add繼續開發
```

# 比對檔案與版本差異

```sh
git diff #在什麼參數都不加的使用情況，比對的是「工作目錄」與「索引」(index)之間的差異
git diff commit1 commit2 #比對兩個版本間的差異
git diff HEAD # 最常用的指令，因為這代表你要拿「工作目錄」與「當前分支的最新版」進行比對
git diff --cached HEAD # 最常用的指令(索引(index)狀態)，這個語法代表的是「當前的索引狀態」與「當前分支的最新版」進行比對。

git diff => 工作目錄 vs 索引(index)
git diff HEAD => 工作目錄 vs HEAD
git diff --cached HEAD => 索引(index) vs HEAD
git diff --cached => 索引 vs HEAD
git diff HEAD^ HEAD => HEAD^ vs HEAD
```

# git merge

```sh
# 先切換到主分支
$ git checkout master
 
# 使用 fast-forward
$ git merge <branch> 

or 
# 使用 no-fast-forward
$ git merge <branch> --no-ff 
```

# git rebase

Rebase 是 “Re-” 與 “Base” 的複合字，這裡的 “Base” 代表「基礎版本」的意思，表示你想要重新修改特定分支的「基礎版本」，把另外一個分支的變更，當成我這個分支的基礎。

功能一：合併版本
```sh
# 切換至 branch1 分支：
git checkout branch1
 
# 然後執行 Rebase 動作，把 master 當成我們的基礎版本： 
git rebase master
```

功能二：修改歷史 commit 紀錄
```sh
除上述功能外，rebase 還能用來修改特定分支線上任何一個版本的版本資訊
git rebase -i HEAD~3
```

- 請非常小心使用，因為 rebase 會改變歷史紀錄

# Updates were rejected

```console
To github.com:waipangsze/waipangsze.github.io.git
 ! [rejected]            hexo -> hexo (non-fast-forward)
error: failed to push some refs to 'git@github.com:waipangsze/waipangsze.github.io.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

這是因為遠端 repository 和我本地的 repository 衝突導致的; 但是卻沒有pull到本地。這樣就產生了版本衝突的問題。

```sh
1. 使用强制push的方法
git push -u origin master -f
这样会使远程修改丢失&#xff0c;一般是不可取的&#xff0c;尤其是多人协作开发的时候。

2. push前先将远程repository修改pull下来
git pull origin master
git push -u origin master
```

# Conflicts
Between your last push and the next push, if someone else pushes to update the remote database but you do not update your local database, your push will be rejected. At this time, you need to perform a merge operation to import other people's modification history,otherwise the push will be rejected.

## Resolve conflict

> The upper part separated by == is the editing content of the local database, and the lower part is the editing content of the remote database. After modifying all conflicting places, performing a commit will submit the commit content of the conflicting merge message.

# git add/commit/push

When the remote github repo is known, You can git clone the remote address first
Then copy the project file to be submitted to the clone folder

```sh
# Add all files, such as submitting only some files requires a single git add file name
git add. 
git commit -m "remarks for submission"
# Make some changes
git commit --amend

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

# 持續整合（Continuous integration CI）系統

當開發人員將本機Git倉庫中的程式碼更新後，執行commit和push操作；該動作會產生一個事件，並觸發Jenkins進行建置。如果開發人員在程式碼中加入和Junit或testng測試案例，也會在建置過程中執行；建置完成後，jenkins會將建置的結果以Gerrit投票的方式傳到Gerrit伺服器上。專案Owner登入Gerrit Web UI，進行Code Review時會看到Jenkins建置的結果並進行code review。只有當建置成功，且code review通過時，專案owner才會將程式碼合到主分支上，如果說jenkins上建置失敗，則表示程式碼肯定有問題，Owner不用Code Review，直接通知開發檢查程式碼並重新提交。

近年來，由於開源專案、社群的活躍熱度大增，進而引來持續整合（CI）系統的誕生，也越發的聽到更多的人在說協同開發、敏捷開發、迭代開發、持續整合和單元測試這些拉風的術語。然而，大都是僅僅聽到在說而已，國內也很少有公司能有完整的 CI 體系流程。反之一些開源專案都有完整的 CI體系，例如openstack。為了實現程式碼託管->程式碼審核->程式碼發佈的一套自動化流程，我特意在IDC伺服器上部署了`Gitlab+Gerrit+Jenkins`對接環境，

![](https://i.imgur.com/Lsz18A6.png){width=600}

1. `Gitlab`上進行程式碼託管
在gitlab上建立的專案設定成Private，一般使用者對這個專案就只有pull權限，不能直接進行push，Git自帶code review功能。
   - 強制Review ：在Gitlab 上建立的項目，指定相關使用者只有Reporter權限，這樣使用者沒有權限使用git push功能，只能git review到Gerrit 系統上，Jenkins在監聽Gerrit上的項目事件會觸發建置任務來測試程式碼， Jenkins 把測試結果透過ssh gerrit 給這個專案打上Verified （資訊校驗）成功或失敗標記，成功通知其它人員Review（程式碼審核） 。
   - Gitlab保護Master 分支：在Gitlab 上創建的專案可以把Master 分支保護起來，普通用戶可以自己創建分支並提交代碼到自己的分支上，沒有權限直接提交到Master分支，用戶最後提交申請把自己的分支Merge到Master ，管理員收到Merge 請求後， Review 後選擇是否合併。
   - 可以將gitlab和gerrit部署在兩台機器上，這樣gitlab既可以託管gerrit程式碼，也可以作為gerrit的備份。
   - 因為gitlab和gerrit做了同步，gerrit上的程式碼會同步到gitlab。這樣即使gerrit部署機故障，它裡面的程式碼也不會遺失，可以去gitlab上拿。
2. `Gerrit`審核代碼
   - Gerrit是一款被Android開源專案廣泛採用的code review(程式碼審核)系統。普通用戶將gitlab裡的專案clone到本地，修改程式碼後，雖不能直接push到程式碼中心 ，但是可以透過git review提交到gerrit上進行審核。 gerrit相關審核員看到review資訊後，判斷是否通過，透過即commit提交。然後，gerrit程式碼會和gitlab完成同步。
   - grrit的精髓在於不允許直接將本地修改同步到遠端倉庫。客戶機必須先push到遠端倉庫的refs/for/*分支上，等待審核。
   - gerrit上也可以比較程式碼審核提交前後的內容狀態。
3. `jenkins`代碼發布
   - 當使用者git review後，程式碼透過jenkins自動測試（verified）、人工review 後，程式碼只是merge到了Gerrit的專案中，並沒有merge到Gitlab的專案中，所以需要當Gerrit 專案倉庫有變更時自動同步到Gitlab的項目倉庫中。 Gerrit 自備一個 Replication 功能，同時我們在安裝 Gerrit 時候預設安裝了這個 Plugin，透過新增replication.config 給 Gerrit

# References

1. [日常开发场景Git使用技巧记录](https://mp.weixin.qq.com/s/YhTDnBKXC1ENbu8i6-1pJw)
2. [Git commit提交信息规范](https://mp.weixin.qq.com/s?__biz=MzkxNTM4ODc4Ng==&mid=2247485667&idx=1&sn=898316a92cbc30b0aa216d1d464d8949&chksm=c15ea8fef62921e8ea69a5fbeceedea1ed43fa87a79b8f2cd2860c475617fe05d8ca99d50804&cur_album_id=3026951976829517826&scene=189#wechat_redirect)
3. [Git 版本控制系統 - stash 暫存修改內容](https://awdr74100.github.io/2020-05-06-git-stash/)
4. [【Git教學】分支合併: merge 與 rebase 差異](https://www.maxlist.xyz/2020/05/02/git-merge-rebase/)