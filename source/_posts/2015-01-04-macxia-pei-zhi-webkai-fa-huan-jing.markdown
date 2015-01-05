---
layout: post
title: "mac下配置sublime调试环境"
date: 2015-01-04 16:01:56 +0800
comments: true
categories:web
---


### 配置PHP和JS调试环境
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

---

### 配置Xdebug


#### 安装xdebug

选择一个xdebug版本

* brew search xdebug

安装xdebug

* brew install php55-xdebug

将以下代码片断加入到php.ini文件最后

	zend_extension= "/usr/lib/php/extensions/no-debug-non-zts-20121212/xdebug.so"
	xdebug.default_enable = On
	xdebug.show_exception_trace = On
	xdebug.show_local_vars = 1
	xdebug.max_nesting_level = 50
	xdebug.var_display_max_depth = 6
	
	xdebug.dump_once = On
	xdebug.dump_globals = On
	xdebug.dump_undefined = On
	xdebug.dump.REQUEST = *
	xdebug.cli_color = 2

**以下为配置sublime**

将项目保存为一个project

* sublime->project->save project as...

* 利用package control 安装xdebug client

配置项目

* sublime->project->edit project

在其中添加类似以下内容

	{
	    "folders":
	    [
	        {
	            "follow_symlinks": true,
	            "path": "."
	        }
	    ],
	    "settings": {
	        "xdebug": {
	             "url": "http://my.local.website/",
	        }
	    }
	}

开启调试，在项目文件中添加断点

* xdebug->Add/Remove breakpoint

进行调试

	tools->xdebug->start debugging(launch browser)




