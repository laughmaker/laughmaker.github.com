---
layout: post
title: "mac下安装配置mysql"
date: 2015-02-11 17:00:22 +0800
comments: true
categories: web
---

## mac下安装配置mysql

1.安装：
	
	brew install mysql
	
2.添加修改mysql配置  
运行：

	mysqld --help --verbose | more  
	
查看这一行：

	  Default options are read from the following files in the given order:
	/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf

创建连接和配置文件：

	ls $(brew --prefix mysql)/support-files/my-* (用这个可以找到样例.cnf)
	cp /usr/local/opt/mysql/support-files/my-default.cnf /etc/my.cnf (拷贝到第一个默认读取目录)

3.解决mysql_connect():No such file or director错误

启动mysql后,如果/tmp/mysql.sock文件存在，/var/mysql/mysql.sock不存在，则运行：

	cd /var
	sudo mkdir mysql
	cd mysql
	sudo ln -s /tmp/mysql.sock mysql.sock
	
3.启动与停止

	mysql.server start
	mysql.server stop