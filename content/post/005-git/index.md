---
# 基本内容
title: Learning Git # 文章标题
description: note for learning git. # 文章描述
slug: 005-learning-git # 文章slug
date: 2025-12-01 14:30:00+0800 # 发布时间 创建时间
lastmod: 2025-12-01 14:30:00+0800 # 修改时间 会在文章末尾显示
# image: cover.png # 文章封面
# 分类与标签
categories:
    - learning
tags:
    - git
# 其他
draft: true
# weight: 1  
# 全局配置覆盖
# math: true
# readingTime: true
# toc: true
# comment: true    
---

## 参考
[廖雪峰git教程](https://liaoxuefeng.com/books/git/introduction/index.html)
[learning git branching](https://learngitbranching.js.org/)


## 安装git

## 配置git
```bash
# 全局配置
git config --global user.name "user1"
git config --global user.emal "user1@example.com"

# 局部配置
git config user.name "user1"
git config user.emal "user1@example.com"

# 查看配置
git config --list
git config --list --local
```

git默认使用master分支，可以使用git config --global init.defaultBranch main 来设置默认分支为main。
```bash
git config --global init.defaultBranch main
```

## git的基础操作
```bash
# 创建文件夹
mkdir git-playground
cd git-playground
# 初始化git仓库
git init
```

```bash
# 添加文件
touch README.md
# 添加到暂存区
git add README.md
# 提交到仓库
git commit -m "first commit"

# 添加多个文件
git add file1 file2 file3
git commit -m "add files"

# 添加所有文件
git add .
git commit -m "add all files"
```

```bash
# 查看状态
git status
# 查看差异
git diff
git diff HEAD -- README.md # 查看当前文件和HEAD的差异
# 查看提交历史
git log
git log --pretty=oneline
git log --graph --pretty=oneline --abbrev-commit
# 查看提交记录
git reflog
```

## 版本管理
HEAD 表示当前分支的指针，指向当前commit。
HEAD~1 表示当前commit的父commit。
HEAD^ 表示当前commit的父commit。

```bash
git reset --hard HEAD^
git reset --hard HEAD~1
```

--hard 参数表示强制修改，会丢弃工作区和暂存区的内容。
--soft 参数表示不修改工作区和暂存区，只修改HEAD指针。
--mixed 参数表示不修改工作区和暂存区，只修改HEAD指针和暂存区。

## 工作区和暂存区
工作区：就是你在电脑上看到的目录。
暂存区：就是一个临时区域，存放暂存的文件。
版本库：.git 目录，存放所有版本的文件。

## 管理修改
git commit 只会把暂存区中的文件提交到版本库中。

## 撤销修改
```bash
# 撤销工作区的修改
# 让工作区恢复到上一次git commit或git add时的状态
git checkout -- README.md
```
场景1：当你改乱了一个文件，想要丢弃工作区的修改时，使用git checkout -- file 命令。
场景2：当你不但改乱了工作区中的文件，还添加到了暂存区时，想要丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

## 删除文件
```bash
# 删除文件
rm README.md
# 删除git 仓库中的文件
git rm README.md
```

## 添加远程仓库
首先，在github上创建了一个完全空的仓库，没有任何文件，也没有任何分支。  

在命令行中创建一个新的存储库
```bash
echo "# git-playground" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:ztnote/git-playground.git
git push -u origin main
```

或者从命令行推送现有存储库
```bash
git remote add origin git@github.com:ztnote/git-playground.git
git branch -M main
git push -u origin main
```
git remote add 命令用于添加一个新的远程仓库，origin是远程仓库的名称，git@github.com:ztnote/git-playground.git是远程仓库的地址。
origin是远程仓库的默认名称，可以使用git remote -v查看远程仓库的详细信息。
git push -u origin main 将本地分支main推送到远程仓库origin的main分支。
-u 参数将本地分支main和远程分支origin/main关联起来，以后就可以使用git push origin main 来推送本地分支main到远程仓库。

## 删除远程库
```bash
git remote rm origin
```
删除只是解除了本地和远程的关联，远程仓库并没有被删除，可以通过github.com来查看。

## 关联到远程库
```bash
git remote rm origin
git remote add origin git@github.com:ztnote/git-playground.git
```

```bash
# 关联多个远程仓库
git remote add origin1 git@github.com:ztnote/git-playground.git
git remote add origin2 git@github.com:ztnote/git-playground.git

git push origin1 main
git push origin2 main
```

## 从远程库克隆
```bash

git clone git@github.com:ztnote/git-playground.git

```
使用git clone命令克隆远程仓库，git clone <url> 命令会自动创建一个本地仓库，并将远程仓库的内容下载到本地。

## 分支管理

查看分支
```bash
git branch
```

创建分支
```bash
git checkout -b dev

# 等价于
git branch dev
git checkout dev

# 等价于
git switch -c dev
```

切换分支
```bash
# 切换到主分支
git checkout main
# 等价于
git switch main
```

合并分支
```bash
# 假设我们在dev分支上开发了一些功能，需要合并到main分支上
git checkout main
git merge dev
```

删除分支
```bash
# 合并完成后，可以删除dev分支
git branch -d dev
```

回退到某个commit
```bash
git branch -f main HEAD~3 # 回退到HEAD~3 的commit
```

## 解决冲突
分支之间的冲突需要手动解决。
之后再合并分支。

查看分支合并图
```bash
git log --graph --pretty=oneline --abbrev-commit
```

## 分支管理策略
git默认使用fast-forward模式合并分支，如果要强制禁用fast-forward模式，需要使用--no-ff参数。

## bug分支
git stash 命令用于保存工作区和暂存区的内容，等以后恢复工作区时使用。
比如在dev分支上开发了一些功能，但是又想切换到main分支上修复bug，可以使用git stash命令保存工作区和暂存区的内容，等以后恢复工作区时使用。

```bash
git status # 假设现在在dev分支上
git stash # 保存工作区和暂存区的内容
git checkout main # 切换到main分支
git checkout -b bug-101 # 创建bug分支
# 完成了bug分支的开发
git checkout dev # 切换到dev分支
git stash pop # 恢复工作区和暂存区的内容
```

stash
```bash
git stash list # 查看stash列表
# 方法1 使用apply恢复 手动删除
git stash apply stash@{0}  # 恢复stash
git stash drop stash@{0} # 删除stash
# 方法2 使用pop恢复 自动删除
git stash pop stash@{0}
```

cherry-pick
```bash
git cherry-pick <commit>
```

## Feature分支
如果一个Feature分支不需要了，可以删除。
```bash
git branch -D feature-vulcan # 强制删除未合并的分支
```

## 多人协作
查看远程库信息
```bash
git remote -v
```

推送分支
```bash
git push origin main
git push origin dev
# 
```

拉取分支
```bash
git pull

```

设置本地分支与远程分支的链接
```bash
git branch --set-upstream-to=origin/dev dev
```

## Rebase

## 标签管理
```bash
git checkout main # 切换到main分支

git tag v1.0.0 # 创建标签
git tag v0.9.0 a6b4c97 # 给某个commit打标签

git tag -a v0.9.0 -m "version 0.9.0" # 创建带有说明的标签
git tag -a v0.9.0 -m "version 0.9.0" a6b4c97 # 给某个commit打标签

git tag # 查看标签
git show v1.0.0 # 查看标签信息

git tag -d v0.9.0 # 删除标签

git push origin v1.0.0 # 推送标签到远程仓库
git push origin --tags # 推送所有标签到远程仓库

git tag -d v0.9.0 # 删除本地标签
git push origin :refs/tags/v0.9.0 # 删除远程标签
```

