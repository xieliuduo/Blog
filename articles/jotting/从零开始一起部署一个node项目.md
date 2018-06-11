通过这篇文章你想了解到如下5个方面知识：

1、linux的一些基本命令操作

2、基于Linux centos7.4下的node、mariaDB、nginx、firewall的安装及配置

3、数据库管理工具，文件上传工具，远程连接工具的基本应用

4、pm2进程守护

5、自动化部署简单介绍


首先登录你的linux

uname -a 可以查看linix内核版本全部信息，其中-a 代表all

检查一下有没有wget，yum

wget 是一个从网络上自动下载文件的自由工具，支持通过 HTTP、HTTPS、FTP 三个最常见的 TCP/IP协议 下载，并可以使用 HTTP 代理。"wget" 这个名称来源于 “World Wide Web” 与 “get” 的结合,前面的比较权威的说法，简单的理解就是web--get 从网上下东西的

yum 是linux系统下的包库及管理工具 自己类比一下npm

分别输入wget -v （yum -v）查看一下装了没