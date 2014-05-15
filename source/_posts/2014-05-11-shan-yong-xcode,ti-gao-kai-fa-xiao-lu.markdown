---
layout: post
title: "善用Xcode，提高开发效率"
date: 2014-05-11 21:53:08 +0800
comments: true
<!--published: false-->

categories: 能工巧匠
---

　　默认的xcode其实已经很强大的了，用起来已经很贴心舒服了，但它还可以更加强大的，以下介绍下我自己的一些增强技巧。

　　**一、配色**  
　　要提高效率，首先得看着舒服，是吧。默认的白色，平常使用其实也挺不错的了，但时间用长了，还是感觉有一些不舒服，它最大的问题是色彩对比不明显，不容易定位到关键词上去。我差不多是使用默认配色一年多后，才打算改换主题的，后来在网上下了很多别人的主题，都发觉要么是对比度太强烈了，看着刺眼，或者太花哨，或者太死板，都有一些不喜欢的地方。后来找到了一个相对比较喜欢的主题，这个主题是仿sublime的一个主题[zenburn](https://github.com/colinta/zenburn) ，但还是有一些不太喜欢的地方，后来花了一整个下午，根据它调了一个主题，包括字体、背景色、关键词颜色等等，算是比较满意了，[下载链接](https://github.com/makeLaugh/xcodeTheme)在这里，喜欢的拿走。  
　　安装方式：下载后，将主题文件放入`/Users/HZD/Library/Developer/Xcode/UserData/FontAndColorThemes `目录中，重启`xcode即可`。我的环境是xcode5.1，其它的未测试。

    **二、CocoaPods**  
    对CocoaPods的介绍和安装教程网太多了，随便百度谷歌一下都满大街了，我想说，如果还没有使用它来管理毓三方库的，那得切换了，因为它实在是太简单太高效了。下面简单罗列一下使用步骤。 [详细参考教程](http://code4app.com/article/cocoapods-install-usage)

* 更换ruby源，以加快安装速度，如果已经更换过的跳过：

		1. gem sources --remove https://rubygems.org/
		2. gem sources -a http://ruby.taobao.org/
		3. gem sources -l //查看新的镜像源

* 安装ruby，一般mac都自带了ruby，若未安装，可用以下方式安装或更新到最新版  

		1. 安装RVM：curl -L https://get.rvm.io | bash -s stable --ruby
		2. 列出已知的ruby版本：rvm list known
		3. 安装最新的版本：rvm install version号
		4. 切换到安装的版本上：rvm use version号
		5. rvm rubygems latest
		
* 安装和使用CocoaPods

		1. 安装CocoaPods：sudo gem install cocoapods
		2. 搜索要使用的库：pod search AFNetworking，会得到如何使用、源地址等提示
		3. 编辑配置文件：vim Podfile
		4. 按上术提示，添加相关配置，并保存  
		   platform :ios, '7.0'  
		   pod "AFNetworking", "~> 2.0"
		5. 安装第三方库：pod install 
		6. 以后打开项目都打开`.xcworkspace`文件，而不是源来的项目文件。

    就这样，安装完成了，以后就可以使用AFNetworking了。
    一个技巧：有时候，第三方库会有警告，为了忽略警告，只需要在Podfile文件中添加一个命令即可，`inhibit_all_warnings!`。
    以下我的Podfile配置文件作为一个参考：  

	platform :ios, '7.0'
	inhibit_all_warnings!
	pod 'MKNetworkKit'
	pod 'FMDB', '~> 2.2'
	pod 'REFrostedViewController', '~> 2.4.5'
	pod 'BlocksKit'
	pod 'Overline-BlocksKit'
	pod 'Masonry'
	pod 'AFNetworking', '~> 2.2.3'
	pod 'UIColor+CSSColors', '~> 0.0.1'
	pod 'BlurryModalSegue', '~> 1.0.0'
	pod 'SBAPNSPusher', '~> 2.2.1'!
	
    伏笔，CocoaPod其实有更便捷的管理方式，见以下插件部分。

    **三、插件**  
    Xcode已经是一个非常强大的IDE了，但是，它还可以更强大，这就需要外挂了。  
    首先，安装插件管理器，当然你也可以手动来管理，但是强列推荐使用它[**Alcatraz**](https://github.com/supermarin/Alcatraz)，有了它，你的xcode将无所不能，且极其的方便管理插件，安装卸载，一键搞定。详细教程，点开链接后仔细阅读吧，以下简单说明安装和卸载：  
    **安装**：curl -fsSL https://raw.github.com/supermarin/Alcatraz/master/Scripts/install.sh | sh  
    或者下载源码编译即可  
    **重启xcode**，即可安装好  
    **卸载**：我还没有卸载过，你要不喜欢，运行下面的命令吧  

	卸载Alcatraz: rm -rf ~/Library/Application\ Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin
	卸载通过它安装的插件: rm -rf ~/Library/Application\ Support/Alcatraz/

    启动Alcatraz：`command+shift+9`或者window->Package Manager  
    它有四个菜单，分别为**All 插件  主题  模板**，点击单个插件前面的小图标时，会自动安装，安装成功后，注意看图标，变成了淡蓝色(注意睁大眼睛看)，卸载再点一次即可，更新再点一次就是更新了。

    网上已经有了各种排名的xcode的十大插件、十五大插件的，试用了一下，下面简单介绍几个个人使用后，觉得挺不错的插件：  

    1.**CocoaPods**  
    不喜欢使用命令行？没关系，装上这个插件，在Product选项里，就集成了CocoaPods选项了，直接点击就可以在Xcode里管理CocoaPods里的第三方库了。  
    ![image](https://github.com/kattrali/cocoadocs-xcode-plugin/raw/master/menu.png)

    2.**ColorSense**  
    是不是每次设置颜色的时候，不知道效果？用它吧，直接在代码里修改颜色，如下图：    
	![image](images/blog/2014/Xcode效率/ColorSense.png)

    3.**VVDocumenter**  
    注释对于代码很重要，特别是对代码规范要求很严的公司，那更是必须写的，话说，自己写，有一个好的规范不也挺好的么，这个配合`appledoc`一起工作，简直是神器，下面有介绍。这货可以让你在写注释的时候，大幅的降低繁杂重复的工作量。见下图：  
    ![image](http://www.cocoachina.com/cms/uploads/allimg/130918/4196_130918173834_1.gif)

    4.**FuzzyAutocomplete**  
    xcode自带的自动代码匹配功能不是很完善，当你只大概记得一个方法时，这时候，xcode就无能为力了，它登场了，效果见下图：  
    ![image](http://www.cocoachina.com/cms/uploads/allimg/131031/4196_131031114351_1.gif)

    5.**XAlign**  
    你有没有一个空格一个空格的敲，就为了对齐那该死的`@、=、*`等符号？好吧，如果你没有，那说明你没有代码洁癖，我应该恭喜你呢，还是替后来维护的人难受？  
    ![image](http://www.cocoachina.com/cms/uploads/allimg/131211/4196_131211113618_1.gif)

    6.**BBUDebuggerTuckAway**  
    xcode很强大，但有的时候，它却不太讨人喜欢，我明明在编写代码了，调试窗口却死活不肯退场，死占着那个位置，让我多难受啊。好吧，我开始写代码了，你去睡一会儿吧。  
    ![image](http://cms.csdnimg.cn/article/201405/05/53674c667516f.jpg)

    7.**XcodeBoost**  
    它的使用有一点小复杂，你需要花一点时间去熟悉它，了解它😃，别恐惧，其实也没有那么复杂了，它真的很强大，真的非常的强大，我还是先上图吧  
    ![image](https://github.com/fortinmike/XcodeBoost/raw/master/Images/highlighting.gif)  
    ![image](https://github.com/fortinmike/XcodeBoost/raw/master/Images/copy-method-declarations.gif)  
    ![image](https://github.com/fortinmike/XcodeBoost/raw/master/Images/paste-without-reindent.gif)  

    8.**KSImagedNamed**
    当我第一次看到它的时候，我内牛满面，我为什么不早点认识它呢，以前在代码里设置图片，不知道图片是什么样子，也不知道图片是什么名字的时候，在UI给我的图片包里，一个一个去找的时候，我有想疯了的感觉。这货不但可以自动匹配识别图片名字，还能预览图片，你还有什么理由不使用它呢？
    ![image](http://cms.csdnimg.cn/article/201405/05/53673f77a65a3.jpg)
    
    以上是我使用过后，感觉非常不错，能够极大提高效率的几个插件，以后有新的体验了，还会更新，这些插件都可以直接通过`Alcatraz`来管理，所以不上链接了。


    **四、文档生成工具Appledoc**  
    前面介绍了`VVDocumenter`，终于轮到它登场了，使用`VVDocumenter`写上标准的注释，然后使用appledoc，一键生成苹果官方式的文档，如下图所示：  
	![image](images/blog/2014/Xcode效率/appledoc1.png)  
    首先安装，可以选择正常安装，也可以选择homebrew安装。  
    **正常安装**：  

	1. git clone git://github.com/tomaz/appledoc.git
	2. cd ./appledoc
	3. sudo sh install-appledoc.sh

    **homeBrew安装**：  

	brew install appledoc

    **使用：**  

	1. cd 项目目录
	2.appledoc -o ./doc --project-name MyProject --project-company MyCompany --company-id companyId .
	
    首先进入项目目录，然后运行上面的命令，`MyProject`是你项目的名字，`MyCompany`是你公司的名字，最后`./doc`是指在当前目录下，生成一个doc目录，里面有一个`docset-installed.txt`文件，里面记录你安装的记录和文档生成的路径，`companyId`一般写成`com.company`格式。  
    appledoc支持markdown、html等语言来写注释，详细的介绍推荐看[这篇文章](http://blog.ibireme.com/2013/08/26/appledoc-guide/)

    **总结：**  
    Xcode虽然是苹果一家开发维护，封闭的，但是在配合一些其它的工具，还是可以极大的提高我们的开发效率，工欲善其事，必先利其器，好好的挖掘它的潜力，为我们服务！