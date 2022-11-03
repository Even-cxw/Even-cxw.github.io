---
title: git操作
date: 2022-09-27 16:15:24
categories: git
tags: [git]
comments: false
---


# git基本命令
1. 删除分支 `git branch -d ""`
2. 拉去远程删除分支： `git remote prune origin`




## 如何关联俩个仓库
- 借鉴地址[https://juejin.cn/post/6844903970637365255#heading-1]
```js
#拉取Fork出来的分支
git clone Fork的分支url

#注意：进入项目根目录，执行下面操作

#查看所有远程库(remote repo)的远程url
git remote -v

#添加源分支url
git remote add upstream 替换成源项目url

#查看所有远程库(remote repo)的远程url
git remote -v

#从源分支获取最新的代码
git fetch upstream

#切换到主分支
git checkout master

#合并本地分支和源分支,本地库和远程的github原仓库同步
git merge upstream/master

#push到fork分支,本地的仓库提交到github
git push origin master
```

- 代码案列
```js
Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git remote -v
origin  https://github.com/zhengjiaao/YOUR_FORK.git (fetch)
origin  https://github.com/zhengjiaao/YOUR_FORK.git (push)

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git remote add upstream https://github.com/zhengjiaao/ORIGINAL_REPOSITORY.git

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git remote -v
origin  https://github.com/zhengjiaao/YOUR_FORK.git (fetch)
origin  https://github.com/zhengjiaao/YOUR_FORK.git (push)
upstream        https://github.com/zhengjiaao/ORIGINAL_REPOSITORY.git (fetch)
upstream        https://github.com/zhengjiaao/ORIGINAL_REPOSITORY.git (push)

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git fetch upstream
From https://github.com/zhengjiaao/ORIGINAL_REPOSITORY
 * [new branch]      master     -> upstream/master

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git merge upstream/master
Already up to date.

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git push origin master
Everything up-to-date

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git push
Everything up-to-date

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git fetch upstream
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/zhengjiaao/ORIGINAL_REPOSITORY
   eb3614a..92539d4  master     -> upstream/master

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git merge upstream/master
Updating eb3614a..92539d4
Fast-forward
 fork.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 fork.txt

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$ git push
Enumerating objects: 4, done.
Counting objects: 100% (4/4), done.
Delta compression using up to 4 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 331 bytes | 331.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/zhengjiaao/YOUR_FORK.git
   eb3614a..92539d4  master -> master

Administrator@ZhengJiaAo MINGW64 /j/IdeaTestProject/GitHub/YOUR_FORK (master)
$

```



## 回滚代码
```js
git reset --hard '版本号'

```

## 回滚后强制提交
```js
git push -f origin ’分支名‘
```