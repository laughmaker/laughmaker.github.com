---
layout: post
title: "NSString属性声明中的copy和retain区别"
date: 2014-01-26 14:39:00 +0800
comments: true
categories: iOS
---

    关于NSString在属性声明中的copy和retain，我想存在一些误解，即和其它不一样的地方。


**首先做以下几个变量声明：**

	@property (retain, nonatomic) NSString *retainStr;
	@property (copy, nonatomic)   NSString *copyStr;
	@property (retain, nonatomic) NSMutableString *retainMStr;
	@property (copy, nonatomic)   NSMutableString *copyMStr;

 
**实现代码如下：**

    NSMutableString *mStr = [NSMutableString string];
    [mStr setString:@"我没变"];
    self.retainStr   = mStr;
    self.copyStr     = mStr;
    self.retainMStr = mStr;
    self.copyMStr   = mStr;
    
    NSLog(@"retainStr:%@",  self.retainStr);
    NSLog(@"copyStr:%@",    self.copyStr);
    NSLog(@"retainMStr:%@", self.retainMStr);
    NSLog(@"copyMStr:%@",   self.copyMStr);
    NSLog(@"\n");
    
    [mStr setString:@"我变了"];
    NSLog(@"retainStr:%@",  self.retainStr);
    NSLog(@"copyStr:%@",    self.copyStr);
    NSLog(@"retainMStr:%@", self.retainMStr);
    NSLog(@"copyMStr:%@",   self.copyMStr);
    NSLog(@"\n");
    
	NSString *str = @"我来了";//[[NSString alloc] initWithFormat:@"我来了"];//两种方式都一样。
    self.retainStr  = str;
    self.copyStr    = str;
    self.retainMStr = [str mutableCopy];
    self.copyMStr   = [str mutableCopy];
    NSLog(@"retainStr:%@",  self.retainStr);
    NSLog(@"copyStr:%@",    self.copyStr);
    NSLog(@"retainMStr:%@", self.retainMStr);
    NSLog(@"copyMStr:%@",   self.copyMStr);
    NSLog(@"\n");
    
    str =@"我走了";//[[NSStringalloc] initWithFormat:@"我走了"];//两种方式都一样
    NSLog(@"retainStr:%@",  self.retainStr);
    NSLog(@"copyStr:%@",    self.copyStr);
    NSLog(@"retainMStr:%@", self.retainMStr);
    NSLog(@"copyMStr:%@",   self.copyMStr);
    NSLog(@"\n");

**打印结果如下：**

	2012-10-12 13:53:14.858 StringDemo[1515:11303] retainStr:我没变
	2012-10-12 13:53:14.860 StringDemo[1515:11303] copyStr:我没变
	2012-10-12 13:53:14.861 StringDemo[1515:11303] retainMStr:我没变
	2012-10-12 13:53:14.862 StringDemo[1515:11303] copyMStr:我没变
	2012-10-12 13:53:14.863 StringDemo[1515:11303] 
	2012-10-12 13:53:14.864 StringDemo[1515:11303] retainStr:我变了
	2012-10-12 13:53:14.865 StringDemo[1515:11303] copyStr:我没变
	2012-10-12 13:53:14.866 StringDemo[1515:11303] retainMStr:我变了
	2012-10-12 13:53:14.867 StringDemo[1515:11303] copyMStr:我没变
	2012-10-12 13:53:14.868 StringDemo[1515:11303] 
	2012-10-12 13:53:14.869 StringDemo[1515:11303] retainStr:我来了
	2012-10-12 13:53:14.869 StringDemo[1515:11303] copyStr:我来了
	2012-10-12 13:53:14.870 StringDemo[1515:11303] retainMStr:我来了
	2012-10-12 13:53:14.871 StringDemo[1515:11303] copyMStr:我来了
	2012-10-12 13:53:14.872 StringDemo[1515:11303] 
	2012-10-12 13:53:14.873 StringDemo[1515:11303] retainStr:我来了
	2012-10-12 13:53:14.874 StringDemo[1515:11303] copyStr:我来了
	2012-10-12 13:53:14.875 StringDemo[1515:11303] retainMStr:我来了
	2012-10-12 13:53:14.876 StringDemo[1515:11303] copyMStr:我来了

**由此可以看出：**

* 对源头是NSMutableString的字符串，retain仅仅是指针引用，增加了引用计数器，这样源头改变的时候，用这种retain方式声明的变量（无论被赋值的变量是可变的还是不可变的），它也会跟着改变; 而copy声明的变量，它不会跟着源头改变，它实际上是深拷贝。
* 对源头是NSString的字符串，无论是retain声明的变量还是copy声明的变量，当第二次源头的字符串重新指向其它的地方的时候，它还是指向原来的最初的那个位置，也就是说其实二者都是指针引用，也就是浅拷贝。

   另外说明一下，这两者对内存计数的影响都是一样的，都会增加内存引用计数，都需要在最后的时候做处理。

   其实说白了，对字符串为啥要用这两种方式？我觉得还是一个安全问题，比如声明的一个NSString *str变量，然后把一个NSMutableString *mStr变量的赋值给它了，如果要求str跟着mStr变化，那么就用retain;如果str不能跟着mStr一起变化，那就用copy。而对于要把NSString类型的字符串赋值给str，那两都没啥区别。不会影响安全性，内存管理也一样。

