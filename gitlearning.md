# Git

# 一
## 基本概念

### 三个工作区域
git仓库 工作目录 暂存区域  

### 三个状态

已提交  已修改  已暂存  

--- 

## [安装下载](https://git-scm.com/downloads)
---

## 配置

1.git config 设置配置变量，低级别覆盖高级别的配置。

2.配置用户信息
```shell
git config  --global user.name "用户名"
git config --global user.email 邮件地址
```
3.配置文本编辑器，windows自行搜索,自己设置了notepad

4.git config --list检查配置

## 帮助
git help

---

# 二

## 获取仓库
现有项目初始化：进入目录 git init  
克隆：git clone [url] [newname]

##记录更新
1.克隆以后，进入文件夹，查看状态git status  
* git status -s/--short简洁查看  
  ??---新添加，未跟踪  
  A ---新暂存  
  M ---修改并暂存  
   M---修改未暂存  
  MM---修改已暂存后又修改

2。新建和修改内容都需要自己放到暂存区，git add 文件名/文件夹名  

3.忽略文件设置：.gitignore文件要在一开始就设置好。  

4.查看未暂存的修改 git diff  
  查看已暂存将要添加到下次提交里的内容 git diff --staged  

5.git commit已缓存提交  
  git commit -a已跟踪提交  

6.git rm删除   
  git rm -f 删除修改并已暂存的内容  
  git rm --cached从仓库删除，但保留在当前工作目录中  

7.git mv [usedname] [newname]

## 查看提交

git log -p显示每次提交的内容差异  
        -2显示最近两次提交   
        --stat查看每次提交的简略的统计信息  
        --pretty指定展示格式，其中，format指定要显示的记录格式

## 撤销操作

git commit --amend撤销提交  
git reset HEAD 撤销暂存  
git checkout撤销修改

## 远程仓库

git remote查看已经配置的远程仓库服务器 -v显示远程仓库使用的git保存的简写与对应的url  
git remote add <shortname> <url> 添加一个新的远程 Git 仓库，同时指定一个你可以轻松引用的简写   
git fetch [remote-name] 从远程仓库中获得数据，需要手动合并  
git pull自动抓取合并远程分支到当前分支

* 默认情况下，git clone 命令会自动设置本地 master 分支跟踪克隆的远程仓库的 master 分支（或不管是什么名字的默认分支）。 运行 git pull 通常会从最初克隆的服务器上抓取数据并自动尝试合并到当前所在的分支。  

git push [remote-name] [branch-name]  
git remote shoe [remote-name]查看某一个远程仓库的更多信息
git remote rename
git remote rm

## 打标签
git tag 列出已有的标签  
轻量标签和附注标签（git tag -av1.4 -m '存储在标签中的信息'）   
后期打标签

* Git sha-1算法前六位为校验和，后期打标签需要提交

共享标签  
检出标签 
删除标签

## 取别名

$ git config --global alias.ci commit

---

# git分支

## 分支简介
  git的分支实质上是包含所指对象校验和的文件。
  
## 分支创建与合并
  git branch 分支名 在当前所在的提交对象上创建一个指针。

  git log  --decorate 查看各个分支当前所指的对象

  git checkout 分支名 从当前分支切换到已存在的分支

  * head指针指向当前所在的本地分支

  git log --oneline --decorate --graph --all输出提交历史、各个分支的指向以及项目的分支分叉情况

  git checkout -b 分支名 新建一个分支并同时切换到那个分支上=git branch + git checkout

  git checkout master --- git merge hotfix  
  hotfix合并到master分支上  
  合并A到B，检出B然后mergeA  
  fast-forward合并当前提交的直接上游，即从一个分支走下去能够到达另一个分支。  
  git branch -d hotfix删除分支
  * 如果合并时出现冲突，git status查看状态然后手动修改

  图形化工具 git mergetool/opendiff可解决冲突，解决冲突以后要git add暂存下来

## 分支管理

  git branch 当前所在分支的一个列表，带*表示HEAD指针指向的分支    
  -v 查看每一个分支的最后一次提交  
  --merged 与 --no-merged过滤这个列表中已经合并或尚未合并到当前分支的分支
  * 已合并没有*的分支可以通过 git branch -d 进行删除，没有合并的分支-D强制删除-d会报错

## 分支开发工作流

   当你新建和合并分支的时候，所有的一切都只发生在你本地的git版本库中，没有与服务器发生交互。

## 远程分支

  git ls-remote/remote 显式获得远程引用的完整列表  

  git remote show/remote 获得远程分支的更多信息

  git fetch origin 同步，抓取本地没有的数据，并且更新本地数据库  

  git remote add 添加一个新的远程仓库引用到当前的项目  

  git push (remote)(branch)推送分支
  * git config --global credential helper cache简单保存几分钟就不必每一次推送都输入用户名和密码

  git pull 跟踪分支,自动查找当前分支所跟踪的服务器与分支，从服务器上抓取数据然后尝试合并到远程分支。相当于 git fetch紧接着git merge

  git checkout -b [branch] [remotename]/[branch]设置其他的跟踪分支，或在一个其他远程仓库上的跟踪分支，或不跟踪master分支

  git branch -vv列出所有本地分支且包含更多的信息
  * ahead表示有n次提交没有推送 behind表示n次提交没有合并。需要统计最新的领先和落后数字，需要在运行此命令前抓取所有的远程仓库，git fetch --all 或者git branch -vv

  git push origin --delete 分支名 删除远程分支（容易恢复）

##  变基

  ### merge：把两个分支的最新快照以及二者最近的共同祖先进行三方合并，结果生成一个新的快照（并提交）。

  ### rebase:将提交到某一分支上的所有修改都移至另一分支上。找到两个分支的最近共同祖先，对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，将当前分支指向目标基底，最后以此将之前另存为临时文件的修改依序应用。
  
  git checkout experiment  找到当前分支  
  git rebase master 找到变基操作的目标基底分支
  git checkout master  
  git merge experiment 最后快进合并  
  * 变基是将一系列提交按照原有次序依次应用到另一分支上，而合并是把最终结果合在一起
