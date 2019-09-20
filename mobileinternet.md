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

# 2019.9.19

军火村  
手机镜像arm kali也有，可以在手机上提前编辑好成一个app用于后门入口

## 下载了文件应该校验是否正确
鼠标操作或者命令行
         
        查看文件的md5校验码
        certutil -hashfile filename MD5
        查看文件的sha1校验码
        certutil -hashfile filename SHA1
        查看文件的sha256校验码
        certutil -hashfile filename SHA256

## 虚拟机应该开启dhcp服务+虚拟机拓展包的安装下载+全局中可以设置多个网络
        
        列出dhcp参数
        vboxmanage list dhcpservers
        修改dhcp参数
        vboxmanage dhcpserver modify

虚拟机的设置相当于影响的是链路层

        lsusb列出Usb信息

USB设备过滤器
新系统应该先做纯净系统的备份



