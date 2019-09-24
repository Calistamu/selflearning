# 2019.9.12 

[校内访问](sec.cuc.edu.en)  
[校外访问](https://github.com/c4pr1c3/cuc-wiki)  
[往届仓库示例](https://github.com/CUCCS)  
[教学视频](sec.cuc.edu.cn/ftp/video)  
[教材](https://c4pr1c3.github.io/cuc-mis/)  
[课件](sec.cuc.edu.cn/huangwei/cuc-wiki/courses/2017/misLecture0x01.pdf)  
[公开仓库](https://classroom.github.com/a/nf63lwDF)  
[私有仓库](https://classroom.github.com/a/UP5b348p)  
老师使用OBS进行录频
kalirolling虚拟机,具备很多安全相关的软件
如何通过git术语
如果想搭建无限的网络工作环境需要无线AP，用自己的AP搭建无线网络    

## 获得
了解了电力猫  
热点  
802.11链路层和物理层，与网络层没有关系，黑盒化向上封装  
每一个路由器能够构建一个独立的BSS，都使用同一个SSID  
SS（service stack）sta(station,其实就是终端设备比如手机平板)
mac地址前缀相同说明是同一厂商  
如何发现前缀不同，说明可能是别人搭建的钓鱼网站  
单个网络设备同时建立多个网络，SSID仅仅是区分不同的网络
好的服务器是平滑切换
每一层有每一层的检测方法，Ping不通不一定是网络层有问题  
一个基本的物理网络可以没有路由的功能，仅仅交换就可以  
信道设置  
敏感信息记得遮盖  
kismac是mac上的嗅探工具  
一般笔记本中的无线网卡，厂商自动禁用了监听模式   
STA关联AP，历史关联记录优先，先发现，先关联，信号强度高者优先，无线嗅探，无线网络太危险，有线更安全  
无线网络连接，访问不需要同意就能够读取照相机里面的照片是怎么做到的？  
openwrt可以装在virtualbox中

## 如何写实验报告

### 5W2H

what 实验报告的准确标题、做了哪些实验、实验结论/观测现象是什么  

* 一定要有标题，不要狂贴图 

why 上述结论的依据、观测现象的产生原因是什么  

* 解释结论与观测现象，给出客观依据

when 以无限抓包分析实验为例，展示wireshark的抓包统计信息：抓包持续时间、实际起止时间

* 抓包起止时间，抓包信息

who 本次实验报告得到了哪些人的帮助？参考文献引用标注要清晰和全面，第四章网络安全监听的实验（arp或其他的劫持方式，劫持时间或手段对打击有没有影响） 
    
* 标注出参考链接。数据包抓包的无线网络谁搭建的，是否能连互联网，描述接入环境，描述不同互联网接入条件下的实验情况。 容器或者虚拟环境

where 本次实验的网络拓扑、虚拟机还是容器环境？局域网还是互联网环境？wifi/有限/4G接入条件等等  

* 出现问题的场景要描述清楚

How实验操作步骤描述、代码、配置文件、完整命令行等  

* 命令行统计,越专业越好

how much 报告的篇幅（并非越长越好，原创内容为主，避免复制粘贴参考资料中内容）、图片的后期处理（标注、尺寸裁剪）、操作录像（精简、避免无效操作、剪辑掉长时间的静止画面）、排版（markdown使用规范性、易读性）

*  原创性内容，加工整理

## 安装kali

选对类型就可以，具体版本没关系，主要生成初始硬件的配置类型。  
最好是英文版  
选择NAT为主网络  
装服务器硬盘不要选择向导式，选manual，LVM提供更好的可靠性，由于虚拟机所以选择向导式  
先备份，再使用  
lsb查看设备
iw dev查看网卡基本信息
iw phy查看网卡物理信息

# 2019.9.19

## 下载了文件应该校验完整性

鼠标操作或者命令行
         
        查看文件的md5校验码
        certutil -hashfile filename MD5
        查看文件的sha1校验码
        certutil -hashfile filename SHA1
        查看文件的sha256校验码
        certutil -hashfile filename SHA256

## 验证真实性

（如果我们能攻击kali的服务器，换掉镜像，那么我们也可以更换校验码，那么验证完整性显得不堪一击）  
opengpg工具  
kali网上有例子命令行操作

## 虚拟机应该开启dhcp服务+虚拟机拓展包的安装下载+全局中可以设置多个网络
        
        列出dhcp参数
        vboxmanage list dhcpservers
        修改dhcp参数
        vboxmanage dhcpserver modify

虚拟机的设置相当于影响的是链路层

        lsusb列出Usb信息

USB设备过滤器
新系统应该先做纯净系统的备份
   
        iw dev查看网卡基本信息
        iw phy查看网卡物理信息

## 跟着视频操作后发现 Kali 安装完成后无法上网

完整复制以下代码到「终端」运行
     
     grep "iface eth1 inet dhcp" /etc/network/interfaces || cat << EOF >> /etc/network/interfaces
     auto eth0
     iface eth0 inet dhcp
     auto eth1
     iface eth1 inet dhcp
     EOF

重启网络管理服务

     systemctl restart networking

检查确认两块网卡都已分配到了正确的 IP
      
     ip a

## 无法通过 ssh 访问 Kali

默认安装完 Kali 后，系统未开启 SSH 服务，且默认 SSH 服务配置禁止 root 用户使用口令方式登录，因此需要按照以下方式操作一遍才能正确开启 SSH 服务。   
            
      grep -q 'PermitRootLogin yes' /etc/ssh/sshd_config || echo 'PermitRootLogin yes' >> /etc/ssh/sshd_config  

设置 SSH 服务为开机自启动

      systemctl enable ssh  

启动 SSH 服务
  
      systemctl start ssh  

[chocolatey安装](https://chocolatey.org/courses/installation/installing?method=installing-chocolatey)  
[WSL2安装](https://docs.microsoft.com/zh-cn/windows/wsl/wsl2-install)  
[cmder安装](https://cmder.net/)  
NAT网络无法满足使用宿主机访问  
openssh高级用法  
.ssh保存公私钥  
逆等率  
win7 cmder  
wsl或wsl2  
openwrt开源的无线路由器，无线仿真的路由器  
在路由器上进行漏洞的挖掘和复现，如何对路由器逆向工程，对他的配置文件进行编解码，加解密  
正确的学习方法：看官方文档，一定要注意版本  
建议下载稳定版本
多重加载相当于创建了一个只读的文件系统  
squashfs ex4 openwrt.org/docs/techret  
查错误应该查错误关键字  
chocolatey包管理软件方便安装  
看文章的时候要仔细  
查网卡





