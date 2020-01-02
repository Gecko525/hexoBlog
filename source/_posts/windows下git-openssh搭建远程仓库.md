---
title: windows7下git+openssh搭建远程仓库
date: 2017-12-22 19:53:47
tags: git
---
## 1.安装git和openssh ##
## 2.配置openssh ##

<!--more-->

> http://blog.51cto.com/leomars/1211702

开启服务：net start opensshd

关闭服务：net stop opensshd

连接测试：ssh Administrator@192.168.28.188

此处会跳转到默认的home>Administrator目录

尝试修改默认home目录失败
## 3.新建远程仓库 ##

> http://blog.csdn.net/ccf0703/article/details/7578276

这个博客讲的挺详细的

用ssh连接后创建或直接在远程仓库所在的机器上创建

此处用ssh

	mkdir test.git
	cd test.git
	git init --bare

退出服务器，远程仓库已建好

## 4. 克隆远程仓库并测试push和pull##

在测试机上克隆远程仓库

	git clone Administrator@192.168.28.188:test.git

克隆完毕

进入test,创建，提交，推送，默认有一个远程地址叫origin
	
	cd test
	touch a b c
	git add .
	git commit -m "创建a b c三个文件"
	git push origin master

推送完毕，经测试pull也没有问题

有关远程的命令：

	git remote add origin Administartor@192.168.28.188:test.git
	git remote set-url origin Administrator@192.168.28.188:test.git
	git remote rm origin
## 5.使用密钥连接 ##

参照上边第一个博客地址，修改openssh配置文件,然后把要验证的机器的公钥拷贝到.ssh/authorized_keys中，重启服务即可
生成密钥指令：

	ssh-keygen -t rsa -b 2048

## 6.待解决问题 ##

1）home目录未改变，所以远程仓库只能放到home>administrator这个文件夹中
