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

#
