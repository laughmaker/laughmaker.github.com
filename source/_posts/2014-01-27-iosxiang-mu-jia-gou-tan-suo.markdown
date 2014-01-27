---
layout: post
title: "iOS项目架构探索"
date: 2014-01-27 10:28:48 +0800
comments: true
<!--published: false-->
categories: iOS
---
***做了iOS项目两年多，对怎么来做好项目架构，做过一些思考，也参考了一些别人的写法，这里写下自己的一些想法与思考，[示例代码](https://github.com/makeLaugh/TWAPP)。***

<!--more-->

**首先来看项目在硬盘上的实际目录结构**
	
<!--![实体目录结构](/images/blog/2014/iOS项目架构探索/1.png)-->
{% img /images/blog/2014/iOS项目架构探索/1.png %}

在这里，我给第个功能块，都建立实际的目录文件夹来管理，每个功能块里，都建立三个文件夹：Controller、Model、View，这样做，主要是让整个目录结构很清晰，易于管理。	

**再来看看在Xcode中的目录结构**
	
![整个项目目录](/images/blog/2014/iOS项目架构探索/2.png)	
在Xcode里，整体目录结构和实体文件夹结构保持一致。
	

**项目里的Lib目录**

![Lib目录](/images/blog/2014/iOS项目架构探索/3.png)

主要用于管理第三方类库，比如我这里添加了FMDB、MKNetworkKit、SDWebImage等几个常用的类库，还有Category里主要是用来管理自己写的一些常用分类; Views管理一些常用的第三方类库。
	

**这里是APP真正的结构了，写代码最多的地方**
	
![APP代码目录](/images/blog/2014/iOS项目架构探索/4.png)

* Controller：放各种ViewController，实现逻辑控制，为了缩短类名，我ViewController都缩写为VC
* View：管理ViewController的各个子View
* Model：数据模型文件夹，各种数据模型都放在里面，如果简单的Controller，可能不需要

	
**这里是很多APP都可以通用的一个目录，需要做的修改很少，很多甚至直接拖进去都可以直接使用的**
	
![AppGeneral目录](/images/blog/2014/iOS项目架构探索/5.png)

AppGeneral分为两个目录：AppDefine和AppEngine

* AppDefine：这里管理一些常用的定义
	* Appconfig.h：定义了项目里一些常用的常量，比如常用的字体、颜色、常量等等
	* MacroDefine.h：定义一通用的一些宏定义，所有项目通用，以m开头
	* NetworkAPI.h：定义项目里的网络接口
* AppEngine:这里定义了一些通用的数据模型和UI
	* TWModel：定义了一些通用的数据模型
		* TWModel.h：模型的引入头文件，方便引用
		* TWTableDataSource.h：定义TableView的通用DataSource，以减轻使用UITableView的VC复杂度
		* TWTableDelegate.h：定义TableView的通用Delegate，以减轻使用UITableView的VC复杂度
		* TWTableModel.h：TableView的通用数据模型，对TableView的数据做简单的处理
		* TWNetworkRequest.h：对MKNetworkEngine的一层封装，简化网络请求
	* TWUIKit:定制项目里一些通用的UI样式，这样在具体使用的时候，就不需要再单独进行设置，这里的设置可能每个项目都需要自行根据需求设定
		* TWKit.h：UI的引入头文件，方便引用
		* TWButton.h：定制Button的样式，如形状，字体大小等等
		* TWCell.h：定制Cell的几个通用方法，配合TWTableDataSorce和TWTableDelegate使用
		* TWLable：定义Label，如背景为透明，字体大小等
		* TWTextField：定制TextField，如圆角，字体大小，背景色等
		* TWTextView：同上
		* TWViewController.h：定制ViewController，如显示加载页面等
		* TWBarButtonItem.h：定制BarButtonItem按钮，如导航栏按钮，在iOS7和之前显示是不一样的，这里把它设置为一样的，使样式统一，体验一致
		
**最后来看.pch文件引用**
![PCH文件](/images/blog/2014/iOS项目架构探索/6.png)
这里我把一些常用的基本上不会改变的、可能会在很多地方都使用到的头文件，一次在这个文件里引用，这样就不会每次使用的时候，都去引入一次了。

**总结一下**：

    我觉得，项目架构，最主要的是要使项目结构清晰，易于管理，然后是简化开发。所以我首先把项目文件夹规范起来，定义各个文件夹的作用，这是为了文件管理，使结构清晰。然后，定义一些通用的模板和全局样式，这样就不至于写很多重复的代码，简化开发。

    另外，这里面，其实有一些东西，是可以单独拿出来写一下，比如TWTableDataSource、TWTableDelegate和Views里的一些东西等等，后续再写。
