---
layout: post
title: "mac下配置web开发环境"
date: 2015-01-04 16:01:56 +0800
comments: true
categories:web
---


### sublime配置PHP和JS调试环境
* 点击tools -> build system -> new build system
* 在其中输入如下代码，保存为PHP.sublime-build 

		{
			"cmd": ["php", "$file"],
			"file_regex": "^(...*?):([0-9]*):?([0-9]*)",
			"selector": "source.php"
		}

* 此后，即可用command+B进行编译调试了

另外，添加一个new build system，命名为JSC.sublime-build，添加以下内容，即可调试js代码：

	{
		"cmd": ["/System/Library/Frameworks/JavaScriptCore.framework/Versions/A/Resources/jsc", "$file"],
		"selector": "source.js"
	}



### NGINX常用的指令有 
nginx -V 查看版本，以及配置文件地址  
nginx -v 查看版本  
nginx -c filename 指定配置文件  
nginx -h 帮助  
nginx -s [reload\reopen\stop\quit]  

配置文件地址：/usr/local/etc/nginx/nginx.conf  
默认访问目录：/usr/local/Cellar/nginx/1.4.2/html

### brewhome 常用的指令  
brew search mysql : 搜索具体的程序包    
brew install mysql : 安装具体的程序包  
brew info mysql : 查看具体程序的信息  
brew uninstall mysql : 卸载具体的应用（这里只是用mysql  作个例子）

### PHP
homebrew安装的PHP路径：/usr/local/Cellar/php54/5.4.19/  
配置文件目录：/usr/local/etc/php/5.6