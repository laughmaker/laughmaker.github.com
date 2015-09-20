---
layout: post
title: "使用pods管理项目"
date: 2015-09-20 21:46:15 +0800
comments: true
categories: iOS
---


    Spec Repo是所有的Pods的一个索引，就是一个容器，承载所有公开或私有的的Pods项目源引用，仅保存每个pod库的引用。

    公共的Spec Repo是一个Git仓库remote端，在GitHub上，但是当你使用了Cocoapods后他会被clone到本地的~/.cocoapods/repos目录下，这个master目录的结构是这个样子的

	├── Specs
	    └── [SPEC_NAME]
	        └── [VERSION]
	            └── [SPEC_NAME].podspec
	            
备注：运行 `pod repo` 命令，可以查看所有的pod仓库，然后进入到列出来的目录，可查看相关仓库的目录结构
	            
**一、创建私有的Spec Repo**

1.首先在托管服务器上创建一个仓库。  
2.然后将托管服务器中的仓库与本地pod repo关联：  

	# pod repo add [Private Repo Name] [GitHub HTTPS clone URL]
	pod repo add TWSpecs https://git.coding.net/laughmaker/TWSpecs.git
	
备注：此处TWSpecs名可以随便取，只是取在你本地的一个命名，作为与远端相关联的一个名称。
	
备注：若别人需要使用这个私有项目里的代码，需要有单个项目和这个仓库的权限才能使用

**二、创建私有模块项目，并创建podspec文件**

1.创建一个包含私有代码的项目，并将代码上传到托管服务器  
2.在根目录下，运行以下命令创建podspec文件  

	pod spec create PodTestLibrary git@coding.net:wtlucky/podTestLibrary.git

备注：由于此步骤会创建一个完整的配置文件，里面包含许多用不到的配置信息和注释，故可以拷贝其他项目的配置文件来做相应修改，简化操作。另此配置文件，应保持简洁，不需要的注释和信息，最好删除掉。

**三、配置验证podspec文件**

创建成功后，会在根目录下产生一个.podspec文件，按提示中，填入相应信息后，输入以下命令，进行验证：

	pod lib lint
	
若成功会提示passed validation.否则按提示进行错误改正。
	
**四、本地测试**

创建一个新的项目，在这个项目的Podfile文件中直接指定刚才创建编辑好的podspec文件，看是否可用。 在Podfile中我们可以这样编辑，有两种方式

	platform :ios, '7.0'
	
	pod 'PodTestLibrary', :path => '~/code/Cocoapods/podTest/PodTestLibrary'      #指定路径
	pod 'PodTestLibrary', :podspec => '~/code/Cocoapods/podTest/PodTestLibrary/PodTestLibrary.podspec'  #指定podspec文件
	
然后执行pod install命令安装依赖，在项目工程中，可以看到pod库安装在Development Pods/PodTestLibrary中。

在项目中编写代码，测试库文件无误后就可以开始下一步了，提交podspec到Spec Repo中。

**五、将私有项目提交到本地Spec Repo中**

向私有Spec Repo提交podspec只需要一个命令

	pod repo push TWSpecs PodTestLibrary.podspec  #前面是本地Repo名字 后面是podspec名字
	
成功之后这个组件库就添加到我们的私有Spec Repo中了，可以进入到~/.cocoapods/repos/TWSpecs目录下查看
	
再去看我们的Spec Repo远端仓库，也有了一次提交，这个podspec也已经被Push上去了。

**六、更新维护私有项目**

更新维护将对应的版本一起更新即可，注意，对应tag版本号一定要一致。

**七、使用私有项目**

	source 'https://git.coding.net/laughmaker/TWSpecs.git'  
	source 'https://github.com/CocoaPods/Specs.git'  

	pod 'TWSNS',:git=>"http://xxxxx.git"
	
备注：私有库和公有库的源都需要添加，否则只能搜到其中的一个代码。
	
**八、删除私有库**

	pod repo remove TWSpecs
	
**九、部署到公有库**

代码创建好了，当然也可以提交到公有库了，之前需要审核一两天的时间，而现在有了trunk，提交即可使用了。

1.首先将代码上传到github。

2.注册trunk服务
	
	pod trunk register wskrdfeai@gmail.com 'laughmaker' --description='我爱我的因我觉得欢喜' --verbose

可查看注册信息：

	pod trunk me
	
3.将仓库上传到公有库中心

	pod trunk push TWSNS.podspec
	
4.也可以将其他人添加进来一起进行开发

	pod trunk add-owner ProjectName(项目名) email(要添加进来的人)
	
以上步骤，可能会稍微慢一些，因为这些操作，都会和github打交道。成功之后，其他人更新pod索引库，即可搜索和使用你刚才添加的项目了。


