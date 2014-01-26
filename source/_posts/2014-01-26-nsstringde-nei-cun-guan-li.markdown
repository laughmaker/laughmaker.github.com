---
layout: post
title: "NSString的内存管理"
date: 2014-01-26 14:03:07 +0800
comments: true
categories: 
---

>本文参考了这篇文章，主要做了补充说明：[点击查看](http://www.cnblogs.com/hellocby/archive/2012/08/23/2652201.html)


**生成NSString的方式:**

方法1.直接赋值:   
 
	NSString *testStr1 = @"a";

方法2.类函数初始化生成:     

    NSString *testStr2 = [NSString stringWithString:@"b"];
    NSString *testStr3 = [NSString stringWithFormat:@"c"];

 方法3.实例方法初始化生成:　

    NSString *testStr4 = [[NSString alloc] initWithString:@"d"];
    NSString *testStr5 = [[NSString alloc] initWithFormat:@"e"];
    
首先查看它们的地址和引用计数：

	2012-10-11 17:35:25.601 StringDemo[8514:11303] test1Address:0x4698
	2012-10-11 17:35:25.601 StringDemo[8514:11303] test2Address:0x46a8
	2012-10-11 17:35:25.602 StringDemo[8514:11303] test3Address:0x746c820
	2012-10-11 17:35:25.602 StringDemo[8514:11303] test4Address:0x46c8
	2012-10-11 17:35:25.603 StringDemo[8514:11303] test5Address:0x7455990
	2012-10-11 17:35:25.585 StringDemo[8514:11303] test1:4294967295
	2012-10-11 17:35:25.586 StringDemo[8514:11303] test2:4294967295
	2012-10-11 17:35:25.596 StringDemo[8514:11303] test3:1
	2012-10-11 17:35:25.600 StringDemo[8514:11303] test4:4294967295
	2012-10-11 17:35:25.600 StringDemo[8514:11303] test5:1
	
  从上可以看出，`test1,test2,test4`都是在一个内存区域，也就是上文所说的常量内存区。test3,test5在一个内存区，也就是堆区。

  这里就有一个疑问：`[NSString alloc] initWithString:@"d"`这种方式初始化的字符串，也就是test4.应该是位于堆区的，但为什么会跑到常量内存区来呢？据说是因为xcode对这种方式做了处理，还包括`[NSString stringWithString:@"b"]`这种方式，这两种初始化字符串都等同于@"ddd"了。所以说test2,test4都同等于test1了。

  还有，对于`NSString *testStr3 = [NSString stringWithFormat:@"c"]`;这种初始化的字符串，只要一写release语句就会挂掉，但其它的都不会挂掉，test1,test2,test4好理解，因为release本来就不会起作用;但testStr5无论release多少次也不会挂掉，只会在控制台报警告：malloc: *** error for object 0x744d650: double free*** set a breakpoint in malloc_error_break to debug。这个猜测应该是也xcode做了优化吧。

  对现在4.4之后的编译器，`NSString *testStr2 = [NSString stringWithString:@"b"];`这种写法会报警告了：Using 'stringWithString' with a literal is redundant。也就是说这种写法是多余的了，它给的建议是用＝@"b"这种方式来代替了。


**小结下吧：**

  对NSString的初始化方法，`对于test1,test2,test4这三种的话建议用＝@“字符串”来使用`，因为本来就是一样的。test3,test5这两种的话，建议用texst3这种，方便点，不用管内存问题，系统自已管理。



