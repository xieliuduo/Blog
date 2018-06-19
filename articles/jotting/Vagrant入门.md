# Vagrant入门

> 后端的同学用 Vagrant 快速的、可重复的创建各种不同环境的虚拟机，来测试部署各后端程序。对于前端同学来说,想向全栈发展，服务器相关的一些操作是绕不开的一道槛。这里先前端为视角，以 Vagrant 来引导大家逐步进入服务器技术相关的领域。


快速入门
安装
安装 Vagrant 非常简单，可以在Downloads 页面选择最新的版本安装。Vagrant 支持 Windows、Linux、Mac 等平台。

同时电脑中还需要再安装[virtualbox](https://www.virtualbox.org/)。

创建一个目录用于存放 Vagrantfile 以及 Vagrant 在工作中的数据

	mkdir myvagrant
	cd myvagrant
 
接下来前往 vagrant 官网寻找一个合适的 Box，这里我选择了 Virtualbox 版本的Centos7

以 Centos7 初始化 Vagrant 工程
	
	 vagrant init centos/7

运行这个命令后会在当前目录下新建一个` Vagrantfile` 的配置文件。

如果本地没有 Centos7 这个镜像，那么接下来将会是一个漫长的下载过程。在这如果实在等不下去，可以先通过别的各种途径将 Box 下载到本地。然后再命令行中输入如下代码：

	» vagrant box add --name centos/7 /local_download_path/virtualbox.box

通过 vagrant box list来检查 box 是否添加成功。出现如下提示代表安装成功。

	centos/7 (virtualbox, 0)

启动虚拟机
再次执行初始化命令，并通过up命令来启动

	» vagrant init centos/7
	» vagrant up

出现如下提示代表启动成功

	Bringing machine 'default' up with 'virtualbox' provider...
	==> default: Importing base box 'centos/7'...
	==> default: Matching MAC address for NAT networking...
	==> default: Setting the name of the VM: myvagrant_default_1528726843301_65709
	==> default: Clearing any previously set network interfaces...
	==> default: Preparing network interfaces based on configuration...
	    default: Adapter 1: nat
	==> default: Forwarding ports...
	    default: 22 (guest) => 2222 (host) (adapter 1)
	==> default: Booting VM...
	==> default: Waiting for machine to boot. This may take a few minutes...
	    default: SSH address: 127.0.0.1:2222
	    default: SSH username: vagrant
	    default: SSH auth method: private key
	    default:
	    default: Vagrant insecure key detected. Vagrant will automatically replace
	    default: this with a newly generated keypair for better security.
	    default:
	    default: Inserting generated public key within guest...
	    default: Removing insecure key from the guest if it's present...
	    default: Key inserted! Disconnecting and reconnecting using new SSH key...
	==> default: Machine booted and ready!
	==> default: Checking for guest additions in VM...
	    default: No guest additions were detected on the base box for this VM! Guest
	    default: additions are required for forwarded ports, shared folders, host only
	    default: networking, and more. If SSH fails on this machine, please install
	    default: the guest additions and repackage the box to continue.
	    default:
	    default: This is not an error message; everything may continue to work properly,
	    default: in which case you may ignore this message.
	==> default: Rsyncing folder: /work/training/vagrant/myvagrant/ => /vagrant
登录到虚拟机
输入 vagrant ssh 登录

此时 vagrant 将使用默认的用户 vagrant 以及预设的 SSH 公钥密钥键值对直接登录虚拟机。

	[vagrant@localhost ~]$
	
切换用户到 root，默认密码是 vagrant

密码在 Linux 中是看不见的，但确实已经输入了

	[vagrant@localhost ~]$ su
	Password:
	[root@localhost vagrant]#
	退出虚拟机
	[root@localhost vagrant]# exit
	exit
	[vagrant@localhost ~]$ exit
	logout
	Connection to 127.0.0.1 closed.
	
关闭虚拟机

Vagrant 提供了好几种方法来关闭虚拟机，你可以根据不同的情况选择不同的方式。

vagrant suspend将虚拟机置于休眠状态。这时候主机会保存虚拟机的当前状态。再用vagrant up启动虚拟机时能够返回之前工作的状态。这种方式优点是休眠和启动速度都很快，只有几秒钟。缺点是需要额外的磁盘空间来存储当前状态。

vagrant halt则是关机。如果想再次启动还是使用vagrant up命令，不过需要多花些时间。

vagrant destroy则会将虚拟机从磁盘中删除。如果想重新创建还是使用vagrant up命令。

进阶操作

安装 nginx

Nginx 是一个十分轻量级的 HTTP 服务器
接上篇登录到虚拟机,并切换到 root 用户。


	[root@localhost vagrant]# yum install -y epel-release
	[root@localhost vagrant]# yum install -y nginx

启动 nginx

	[root@localhost vagrant]# systemctl start nginx

检查 nginx 状态

	[root@localhost vagrant]# systemctl status nginx
	● nginx.service - The nginx HTTP and reverse proxy server
	   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
	   Active: active (running) since Mon 2018-06-11 15:02:45 UTC; 57s ago
	  Process: 2910 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
	  Process: 2908 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
	  Process: 2907 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
	 Main PID: 2912 (nginx)
	   CGroup: /system.slice/nginx.service
	           ├─2912 nginx: master process /usr/sbin/nginx
	           └─2913 nginx: worker process
	
	Jun 11 15:02:45 localhost.localdomain systemd[1]: Starting The nginx HTTP and reverse proxy server...
	Jun 11 15:02:45 localhost.localdomain nginx[2908]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
	Jun 11 15:02:45 localhost.localdomain nginx[2908]: nginx: configuration file /etc/nginx/nginx.conf test is successful
	Jun 11 15:02:45 localhost.localdomain systemd[1]: Failed to read PID from file /run/nginx.pid: Invalid argument
	Jun 11 15:02:45 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse proxy server.

出现如上所示代表 nginx 启动成功。

外部访问 & 端口转发

现在 nginx 是启动到了 vagrant 中，如何在我们的电脑中直接访问呢 ？ 这里需要对 vagrant 做一下端口转发的配置。

首先退出虚拟机。编辑上一篇初始化后生成的配置文件 Vagrantfile,找到

	# config.vm.network "forwarded_port", guest: 80, host: 8080
将最前边的#号删除并保存。

运行` vagrant reload`来应用修改后的配置文件。

登录虚拟机，切换到 root 用户，启动 nginx

这时打开电脑浏览器输入` http://localhost:8080/ `就能看到 nginx 的欢迎页面了。 
