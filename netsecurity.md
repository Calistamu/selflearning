# 2019.9.20

[教材](https://c4pr1c3.github.io/cuc-ns/)
一五七十一章必做  
[查看往年实验报告](https://github.com/CUCCS)


## 获得

 CWE总结了许多软件安全缺陷  
 CVE如果有一个CVE的编号，那么说明你具备挖掘真实软件的能力，指导安全人员如何在系统开发过程中规避已知弱点   
 密码学算法都有一个期限，在期限内不可能通过算法和暴力的方法破解出来，没有绝对的安全  
 不接受office二进制文件，markdown纯文本文件  
 没有正确调用安全算法，也会带来一些问题  
 《通信4.0》软件定义一切，硬件不需要重新采用的情况下，更新软件就可以    
 不同基础设施，不同物理层面的IP网络，导致了纯IP网络问题的扩散  
苹果是第一个，将安全软件放在了硬件上，不仅仅是操作系统当中，由于硬件无法通过应用层保障，并不是不可破解  
技术不断发展，专业的人研究专业的问题，没有终点、持续不断地迭代学习  

第一章iptable配置nat  
图片特殊标记  
用实践证明自己的结论  
先理解问题再回答  
脑洞待开发
注重代码的命令
培养好习惯
## 关于实验
NAT网络和NAT不一样  
ssh客户端可以连接到虚拟机  
昨日的实验问题要看  
linux 防火墙配置  
tail /etx/ssh/sshd_config服务开启  
管道操作符让滚动可控  
本地回环网卡
定向抓包tcpdump  -i enpose -n icmp,先开启抓包器，完全包含应用程序  
ping 地址 -n 1只发一个包，两个信息，一个请求一个响应  
更好的呈现形式  
用户体验的角度  
默认还是普通，修改成多重加载

# 2019.9.29

CAM表动态学习，根据表中的查询有无命中来决定是否更新这个表  
底层安全机制是上层安全机制的基础，

## ARP欺骗的三种模式

终端ARP缓存投毒
  主动嗅探/中间人攻击
交换机Dos 
  强制交换机进入Hub模式：广播,集线器用的人越多，越慢
  * 如果失败了，重启就可以了，不必捕获异常
交换机投毒
     
     arp -a
     #查看地址表

第一个数据包是ipv6，因为ipv6有自己的邻居发现协议  
抓得到广播包，抓不到相应包是与通信方式有关的   
arp表可以动态分配，也可以静态配置  
hostonly方便ssh  
tupdump -i enp0s9 -n  网关
apt update && apt install apache2安装apache attacker
systemctl start apache2 attacker,如果apache没有监听在我们想访问的地址上，那么就还是不可以的
apt update && apt install curl#网关安装curl
apt update && apt install tiniproxy
ps aux | grep apache
