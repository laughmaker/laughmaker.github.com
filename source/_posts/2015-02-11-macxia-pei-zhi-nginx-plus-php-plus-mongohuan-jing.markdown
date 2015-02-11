---
layout: post
title: "mac下配置nginx+php+mongo环境"
date: 2015-02-11 17:30:09 +0800
comments: true
categories: web
---

#### 安装brew

安装：

	curl -LsSf http://github.com/mxcl/homebrew/tarball/master | sudo tar xvz -C/usr/local --strip 1

brew安装的软件目录：

	/usr/local/Cellar

常用命令：

* 更新brew：brew update
* 更新所有brew安装的软件：brew upgrade
* 查看所有brew安装的软件：brew list
* 搜索具体的程序包：brew search mysql
* 安装具体的程序包：brew install mysql
* 查看具体程序的信息：brew info mysql
* 卸载具体的应用：brew uninstall mysql

---

#### 安装pecl

	cd /usr/lib/php 
	sudo php install-pear-nozlib.phar 
	sudo pear channel-update pear.php.net 
	sudo pecl channel-update pecl.php.net 
	sudo pear upgrade-all

---

#### 安装mongo

安装

	brew install mongodb
	
创建/data/db文件夹，启动数据库需要

	sudo mkdir -p /data/db
	
启动数据库

	sudo mongod
	
命令行启动mongo操作终端(非必须)

	sudo mongo
	
停止数据库

	sudo pkill mongo

---

#### 配置php

由于mac自带有php，故直接使用系统自带的php

启动,若提示无相关文件，则创建相关文件再启动

	sudo php-fpm
	
配置文件路径

	/etc/php.ini
	
若无该配置文件，则创建该文件

	sudo mv /etc/php.ini.default /etc/php.ini

php扩展路径

	/usr/lib/php/extensions/

---

#### 配置php的mongo扩展

安装

	sudo pecl install mongo

在/etc/php.ini文档中最后添加

	extension=mongo.so

详细文档

[PHP MongoDB Driver](http://docs.mongodb.org/ecosystem/drivers/php/)

---

#### 安装nginx

安装
	
	brew install nginx

启动

	sudo nginx

配置文件路径

	/usr/local/etc/nginx/nginx.conf

页面访问目录

	/usr/local/var/www

nginx常用命令

	nginx -V 查看版本，以及配置文件地址  
	nginx -v 查看版本  
	nginx -c filename 指定配置文件  
	nginx -h 帮助  
	nginx -s [reload\reopen\stop\quit]
	
详细配置教程

[nginx配置从零开始](http://oilbeater.com/nginx/2014/12/29/nginx-conf-from-zero.html)


---

#### 配置nginx支持php文件

打开配置文档
 
 sudo vim /usr/local/etc/nginx/nginx.conf

将监听端口更改80

	server {
	        listen       80;
	        server_name  localhost;

添加php解析支持，取消注释，并更改文件目录为/usr/local/var/www

	location ~ \.php$ {
	            root           /usr/local/var/www;
	            fastcgi_pass   127.0.0.1:9000;
	            fastcgi_index  index.php;
	            fastcgi_param  SCRIPT_FILENAME  /usr/local/var/www$fastcgi_script_name;
	            include        fastcgi_params;
	        }

[如何正确的配置nginx+php](http://huoding.com/2013/10/23/290)
