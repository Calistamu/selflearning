# linux学习笔记
[老师bilibili教学视频学习笔记](https://space.bilibili.com/388851616/channel/detail?cid=103824)-八章内容，八周学完，没有考试，八次作业，至少做四次作业
https://github.com/CUCCS/linux
前七次公开仓库，第八次作业私有仓库
## 第一章 linux基础
desktop不适用于命令行使用，Live不适用于无人值守安装镜像  

下载了文件要验证校验和，排除低级的技术性问题
```
windows-cmd计算校验和
CertUtil -hashfile ubuntu-18.04.4-server-amd64.iso MD5
```

kickstart红帽子的无人值守方案

安装ubuntu，有一个自动更新，如果选择，每次一打开系统就自动更新了，做作业不选择，生产环境很错误

man手册  
使用h获取man帮助系统中的内置帮助（快捷键映射定义），学习如何使用man  
使用q退出man帮助系统或子页面  
使用上下键滚动页面，使用空格键向下翻页  
使用/键查找关键词（不区分大小写），使用n查看下一个匹配结果  
man的常用命令行参数
```
# 安装开发相关的manual
sudo apt-get install manpages-dev

# 在所有section中查找主题为printf的手册页
man -a printf

# 在所有manual的正文中查找printf关键词
man -k printf

# 直接查看系统调用类帮助文档中主题名为printf的手册页
man 3 printf
```