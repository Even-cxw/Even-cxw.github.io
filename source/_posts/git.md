---
title: git操作
date: 2022-09-27 16:15:24
categories: git
tags: [git]
comments: false
---

# 代码回归
- 指针回滚
```js
git reset --soft HEAD~1  # 回退到前一个提交，保留更改
git reset --mixed HEAD~1  # 回退到前一个提交，保留更改但不在暂存区
git reset --hard HEAD~1  # 回退到前一个提交，丢弃所有更改
```

- 代码回滚并创建提交记录
```js
git revert <commit-SHA>  # 撤销特定提交的更改并创建新提交
```

## 场景
- 想要会退到包含`测试git3`代码如何实践.
```
7000543 HEAD@{11}: commit: 测试git5
b43209f HEAD@{12}: commit: 测试git4
38072e1 HEAD@{13}: commit: 测试git3
0089eaa HEAD@{14}: commit: 测试git2
bde05f9 HEAD@{15}: commit: 测试git1
```
- `git revert b43209f` 


# git基本命令
1. 删除分支 `git branch -d ""`
2. 拉去远程删除分支： `git remote prune origin`


# 查看源地址

- npm config get registry
- cnpm config get registry

# nvm常用命令
---

- 切换成默认版本 `nvm alias default v12.7.0`
- 下载指定版本node `nvm install v12.7.0`

- nvm install报错 `could not retrieve`
```javascript
// 解决办法
// 找到nvm配置目录 、settings.text文件，添加如下配置
node_mirror:npm.taobao.org/mirrors/node/
npm_mirror:npm.taobao.org/mirrors/npm/
```


# cnpm安装
```javascript
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

- 替换淘宝镜像
```javascript
// 命令
npm config set registry https://registry.npm.taobao.org
npm config set disturl https://npm.taobao.org/dist
// 查看是否成功
npm config get registry
```

# yarn安装
```js
// 下载yarn
npm isntall yarn -g
// 查看版本
yarn --version
// yarn 设置淘宝镜像
yarn config set registry https://registry.npm.taobao.org/
yarn config set disturl https://npm.taobao.org/dist
// 切换为yarn自带的镜像源
yarn config set registry https://registry.yarnpkg.com
```
- yarn命令
```js
// 下载依赖
yarn add 包名
// 移除依赖
yarn remove 包名
```




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


