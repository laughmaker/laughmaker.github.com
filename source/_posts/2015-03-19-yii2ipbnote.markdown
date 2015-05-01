---
layout: post
title: "Yii2学习笔记"
date: 2015-03-19 15:02:54 +0800
comments: true
categories: 
---

一、安装

1.安装composer

	brew install composer
	
详细教程见：[composer官网](http://www.phpcomposer.com)

2.安装Yii

安装Yii2高级版，应用名称为：www.3water3.com

	composer  create-project --prefer-dist yiisoft/yii2-app-advanced www.3water3.com
	
二、配置框架

默认安装完成后，有两个应用：`frontend`和`backend`，还需要添加一个`api`应用，提供app应用接口。

1.复制backend目录到当前目录，修改名字为api
	
	cp -r backend api
	
2.修改配置，支持api应用，打开environments目录下的index.php文件，添加api部分修改后如下所示：

	return [
	    'Development' => [
	        'path' => 'dev',
	        'setWritable' => [
	            'backend/runtime',
	            'backend/web/assets',
	            'backend/models',
	            'backend/views',
	            'backend/controllers',
	            'frontend/runtime',
	            'frontend/web/assets',
		         'frontend/models',
	            'frontend/views',
	            'frontend/controllers',
	            'api/runtime',
	            'api/web/assets',
	            'api/models',
	            'api/views',
	            'api/controllers',
	        ],
	        'setExecutable' => [
	            'yii',
	        ],
	        'setCookieValidationKey' => [
	            'backend/config/main-local.php',
	            'frontend/config/main-local.php',
	            'api/config/main-local.php',
	        ],
	    ],
	    'Production' => [
	        'path' => 'prod',
	        'setWritable' => [
	            'backend/runtime',
	            'backend/web/assets',
	            'frontend/runtime',
	            'frontend/web/assets',
	            'api/runtime',
	            'api/web/assets',
	        ],
	        'setExecutable' => [
	            'yii',
	        ],
	        'setCookieValidationKey' => [
	            'backend/config/main-local.php',
	            'frontend/config/main-local.php',
	            'api/config/main-local.php',
	        ],
	    ],
	];

分别进入www.3water3.com/environments/dev和prod目录，复制backend为api

	cp -r backend api
	
3.在根目录下，运行初始化命令

	./init

根据需求，选择开发环境和产品环境。至此，项目框架搭建完成。

三、配置与使用Mysql数据库

1.创建一个测试数据库，进入mysql命令行终端：

	create database dev;
	
2.创建`hzd`用户，密码为`hZd`，权限为dev数据库下所所有操作

	grant all privileges on dev.* to hzd@localhost identified by 'hZd';

3.修改common/config/main-local.php文件，添加数据库连接信息，分别修改dbname、username、password为以上创建的对应内容。

4.创建数据库中相应的表，使用migrate来创建。以user表为例：

进入console/migrations目录，修改init.php文件，将其中的up、down分别修改为safeUp、safeDown。

运行数据库迁移命令：

	./yii migrate/up
	
以上则可创建数据表，./yii migrate/down则可删除user表。

四、设置局域网域名

五、RUL美化

1.打开common/config/bootstrap.php文件，在文件下面添加一行

	Yii::setAlias('api', dirname(dirname(__DIR__)) . '/api');
	
2.修改api/config/main.php文件中内容为如下：

	return [
	    'id' => 'app-api',
	    'basePath' => dirname(__DIR__),
	    'controllerNamespace' => 'api\controllers',
	    'bootstrap' => ['log'],
	    'modules' => [],
	    'components' => [
	        'user' => [
	            'identityClass' => 'common\models\User',
	            'enableAutoLogin' => true,
	        ],
	        'log' => [
	            'traceLevel' => YII_DEBUG ? 3 : 0,
	            'targets' => [
	                [
	                    'class' => 'yii\log\FileTarget',
	                    'levels' => ['error', 'warning'],
	                ],
	            ],
	        ],
	        'errorHandler' => [
	            'errorAction' => 'site/error',
	        ],
	        'urlManager' => [
	            'enablePrettyUrl' => true,
	            'enableStrictParsing' => true,
	            'showScriptName' => false,
	            'rules' => [
	                ['class' => 'yii\rest\urlRule', 'controller' => ['section', 'comment', 'user']],
	            ],
	        ],
	    ],
	    'params' => $params,
	];



六、添加Mongo扩展
	
**附录**：  
1.[Mongo扩展资料](http://www.yiiframework.com/doc-2.0/ext-mongodb-index.html)  
2.[Yii2官方API文档](http://www.yiiframework.com/doc-2.0/index.html)  
3.[官方中文网](http://www.yiichina.com)  
4.[深入理解Yii2.0](http://www.digpage.com)
