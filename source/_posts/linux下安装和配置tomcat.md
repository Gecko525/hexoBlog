---
title: linux下安装和配置tomcat
date: 2017-12-29 16:31:54
tags: Linux
---
说明：linux系统为centOs 6.4,tomcat版本为 7.0.81，jdk版本为 7

**第一次安装时我用了最新的jdk 9，搞了很长时间都启动不了，最后发现是jdk版本太高。**

<!--more-->

## 安装JDK ##

1. 下载jdk安装包，以jdk-7u80-linux-x64.rpm为例，下载后执行命令：

    `rpm -ivh jdk-7u80-linux-x64.rpm`
2. 安装完成后，配置环境变量：
   
	`vi /etc/profile`

    在底部添加如下内容：
   
     export JAVA_HOME=/usr/java/jdk1.7.0_80
     export PATH=$PATH:$JAVA_HOME/bin 
     export JAVA_HOME CLASSPATH PATH
   
    wq保存退出

3. 使用以下命令使配置文件生效

    `source /etc/profile`

4. 分别输入命令 java -version 和 javac -version 检测是否安装成功！

## 安装tomcat ##

1. 下载软件包，此处以 apache-tomcat-7.0.81.tar.gz 为例，下载后先建一个文件夹

	`cd /opt`
	`mkdir tomcat`

2. 将下载的软件包移动到tomcat文件夹下，并解压

	`cp /软件包所在路径 /opt/tomcat`
	`tar -zxvf apache-tomcat-7.0.81.tar.gz`

3. 此时即可启动Tomcat

	`cd /opt/tomcat/apache-tomcat-7.0.81/bin`
	`./startup.sh`

4. 在本地浏览器中输入localhost:8080,即可看到默认页面
![](/images/tomcat.png)

5. 如果**连接失败**，首先检查是否8080端口被占用

	`netstate -anp|grep 8080`

	如果被占用，则可以杀死占用进程或修改tomcat端口。此处选择后者
	
	`cd /opt/tomcat/apache-tomcat-7.0.81/conf`
	`vi server.xml`

	修改以下代码中的8080为8081或其他未被占用的端口,然后重新执行./startup.sh即可

	`<Connector port="8080" protocol="HTTP/1.1"`  
 	`connectionTimeout="20000"`
	`redirectPort="8443" \>`
	`<!-- A "Connector" using the shared thread pool-->`

6. 此时在远程访问一般是不可访问的，还需配置防火墙，可关闭防火墙或开启8080端口，如需访问mysql还可在此处加入3306端口

	`vi /etc/sysconfig/iptables`
在-A INPUT -m state –state NEW -m tcp -p tcp –dport 22 -j ACCEPT这条命令下添加以下命令：

     `-A INPUT -m state –state NEW -m tcp -p tcp –dport 8080 -j ACCEPT`
	 `-A INPUT -m state –state NEW -m tcp -p tcp –dport 3306 -j ACCEPT`
7. 重启防火墙使配置生效
	`/etc/init.d/iptables restart`
## 停止tomcat ##
进入tomcat的bin目录执行shutdown.sh：
	`cd /opt/tomcat/apache-tomcat-7.0.81/bin`
	`./shutdown.sh`
	