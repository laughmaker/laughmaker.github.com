---
layout: post
title: "MKNetworkEngine的一层小封装"
date: 2014-02-14 13:33:09 +0800
comments: true
categories: iOS
---

    之前使用ASI做网络请求的时候，也会对ASI做一层薄薄的封装，以提高代码实际使用时的利用率，后来切换成MKNetworkEngine的时候，也做了一层小封装，现共享如下，[示例代码](https://github.com/makeLaugh/TWAPP)在这个目录里：AppGeneral/AppEngine/TWModel/TWNetworkRequest.h里。

    在**TWNetworkRequest.h**头文件里:

    首先声明成功和失败的两个Block：
	
	//成功失败Blocks
	typedef void (^TWFinishedBlock) (NSDictionary *data);
	typedef void (^TWFailedBlock)   (NSString *error);

    其次设置网络请求方式，默认为POST

	@interface TWNetworkRequest : NSObject
	//网络请求方式，默认为POST
	@property (copy, nonatomic) NSString *httpMethod;
	
    下面是几个具体的网络请求接口

    最基础的一个接口，用得最多的情况

	//请求数据接口
	- (void)requestDataWithParams:(NSDictionary *)params
	                      forPath:(NSString *)path
	                     finished:(TWFinishedBlock)finished
	                       failed:(TWFailedBlock)failed;
	
    可以上传Image的一个接口，相对来说用得没有那么多

	- (void)requestDataWithParams:(NSDictionary *)params
	                    withImage:(UIImage *)image
	                     imageKey:(NSString *)imageKey
	                    imageName:(NSString *)imageName
	                      forPath:(NSString *)path
	                     finished:(TWFinishedBlock)finished
	                       failed:(TWFailedBlock)failed;
	
    上传和下载文件
	
	//下载文件,hostName为主站点名。
	- (void)downloadFileWithFilePath:(NSString *)filePath
	                        hostName:(NSString *)hostName
	                      toSavePath:(NSString *)savePath
	                        finished:(TWFinishedBlock)finished
	                          failed:(TWFailedBlock)failed;
	
	//上传文件，path为要上传的接口，fileKey为后台设定的关键字，fileName为自行设定的文件名。
	- (void)upLoadFile:(NSData *)fileData
	          hostName:(NSString *)hostName
	              path:(NSString *)path
	           fileKey:(NSString *)fileKey
	          fileName:(NSString *)fileName
	          finished:(TWFinishedBlock)finished
	            failed:(TWFailedBlock)failed;

    这里的部分是需要注意的，接口参数没什么问题，主要是我定义的几个变量如`TWSucceed`、`code`等这些，可能各人的不一样，这些一般是由后台定义的东西，相应的替换掉即可。

	- (void)requestDataWithParams:(NSDictionary *)params
	                    withImage:(UIImage *)image
	                     imageKey:(NSString *)imageKey
	                    imageName:(NSString *)imageName
	                      forPath:(NSString *)path
	                     finished:(TWFinishedBlock)finished
	                       failed:(TWFailedBlock)failed
	{
	    MKNetworkEngine *networkEngine = appDelegate().networkEngine;
	    MKNetworkOperation *operation = [networkEngine operationWithPath:path
	                                                              params:params
	                                                          httpMethod:self.httpMethod];
	    if (image)
	    {
	        NSData *imageData = UIImageJPEGRepresentation(image, 1);
	        NSString *mimeType = [NSData contentTypeForImageData:imageData];
	        [operation addData:imageData forKey:imageKey mimeType:mimeType fileName:imageName];
	    }
	    [operation addCompletionHandler:^(MKNetworkOperation *completedOperation)
	     {
	         NSData *data = [completedOperation.responseString dataUsingEncoding:NSUTF8StringEncoding];
	         NSDictionary *resultDict = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];         
	         if (completedOperation.HTTPStatusCode == 200)
	         {
	             if ([resultDict[@"code"] integerValue] == TWSucceed)
	             {
	                 finished(resultDict);
	             }
	             else if ([resultDict[@"code"] integerValue] == TWNeedLogin)
	             {
	                 failed(resultDict[@"msg"]);
	                 [mNotificationCenter postNotificationName:kReLoginNotification object:nil];
	             }
	             else if ([resultDict[@"code"] integerValue] == TWNoData)
	             {
	                 failed(resultDict[@"msg"]);
	             }
	             else if ([resultDict[@"code"] integerValue] == TWFailed)
	             {
	                 failed(resultDict[@"msg"]);
	             }
	             else
	             {
	                 failed(resultDict[@"msg"]);
	             }
	         }
	         else
	         {
	             failed(resultDict[@"msg"]);
	         }
	     }
	                       errorHandler:^(MKNetworkOperation *completedOperation, NSError *error)
	     {
	         mAlertView(@"网络错误", error.localizedDescription);
	     }];
	    [networkEngine enqueueOperation:operation];
	}


    具体实现就不贴了，看我给的链接里的示例吧，这只是我针对自己的项目的一点简单封装，以我的要求来实现的，仅作参考吧！