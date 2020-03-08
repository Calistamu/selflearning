# 网络安全
[教材](https://c4pr1c3.github.io/cuc-ns/)

# 第一章
```tcpdump -i enp0s9 -n icmp ```#debian开启网络抓包  
tmux---？？
```find -name "*.exe" | xargs rm -rfv #删去相同类型文件``` 
## 网站
[识别来源地址ip网站](www.myip.cn)
[在线检查你当前所使用的http代理类型](http://www.adamek.biz/php.php)
## 书籍
[如何成为一名黑客中文版](https://translations.readthedocs.io/en/latest/hacker_howto.html)  
[如何成为一名黑客英文版](http://catb.org/~esr/faqs/hacker-howto.html)

## 第一章
# 第三章
tcpdump -1 eth0 -n -s 65535 -w saved.pcap
* -n避免反向解析，就能看懂包
* -s定长
* 抓包命令行，分析图形化工具
```
ip link set eth0 promisc on #网卡开启混杂模式
```
```
cat /etc/resolv.conf #更改域名解析服务器
```