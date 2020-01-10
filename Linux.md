# Linux学习

## 关于yum

(Could not resolve host)无法解析主机, 初步确认为DNS配置问题，

 因为服务器原来为DHCP自动获取ip，所以DNS为也自动获取,　后业务需要，将ip改为静态ip,  忽略了DNS配置

解决方法：

 一：在网卡中添加DNS

 二：在/etc/resolv.conf中添加　nameserver 8.8.8.8</br>

 ---
使用 man指令时 ，『DATE(1)』的数字意义：
 代号|意义|
 --|:--:|
1|使用者在shell環境中可以操作的指令或可執行檔|
5|設定檔或者是某些檔案的格式|
8|系統管理員可用的管理指令|

## 磁碟与档案系统管理

Linux的档案系统为 **EXt2**--索引式档案系统

* superblock
* inode
* datablock  
