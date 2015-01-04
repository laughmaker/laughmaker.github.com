---
layout: post
title: "mongodb常用操作"
date: 2015-01-04 15:56:30 +0800
comments: true
categories: web
---


---

PHP对MongoDB的扩展文档地址：http://us3.php.net/manual/en/book.mongo.php

	在PHP的Mongo扩展中，有4类接口对象：  
	1.针对MongoDB的连接的操作：MongoClient
	http://us3.php.net/manual/en/class.mongoclient.php
	2.针对MongoDB中数据库的操作：MongoDB
	http://us3.php.net/manual/en/book.mongo.php
	3.针对MongoDB中的Collection的操作：MongoCollection
	http://us3.php.net/manual/en/class.mongocollection.php
	4.针对查询结果集的操作：MongoCursor和MongoCommandCursor
	http://us3.php.net/manual/en/class.mongocursor.php
	http://us3.php.net/manual/en/class.mongocommandcursor.php
	
---

#### 常用管理命令
	./mongo	#启动Mongo Shell
	use foo	#进入数据库foo
	db.createUser({user:'hzdRW', pwd:'hezhendong', roles:[{role:'dbOwner', db:'hzdDB'}]} #添加用户或修改用户密码
	db.system.users.find()	#查看用户列表
	db.auth('name', 'pwd')	#用户认证
	db.removeUser('name')	#删除用户
	show users	#查看所有用户
	show dbs	#查看所有数据库
	show collectios  #查看所有的collection
	db.printCollectionStats()	#查看各collection的状态
	db.printReplicationInfo()	#查看主从复制状态
	db.repariDatabase()	 #修复数据库
	db.copyDatabase(fromDB, toDB, fromHost[, username, password])  #拷贝数据库
	db.collection_name.drop()  #删除集合
	db.dropDatabase()  #删除当前数据库
	db.shutdownServer() #关闭当前服务程序
	exit #退出当前shel

---

#### 数据库连接
1.默认连接本机的27017端口 
 
	$mongo = new MongoClient();

2.自定义格式连接，一般ip为本机可写为localhost,port可省略  

	格式：$mongo = new MongoClient('mongodb://username:password@ip:port');  

---

#### 查询
1.find findOne findAndModify

* 查询user集合中全部内容：

		db.user.find();
		
* 查询user集合中age的值为27的文档：

		db.user.find({"age":27});
		
* 查询user集合中age为27,username为joe的内容：

		db.user.find({"age":27, "username":27});
		
* 查询user集合中的所有内容，但只返回username和email键的值：

		db.user.find({}, {"username":1, "email":1});
		
* 查询user集合中的所有内容，但剔除age键：

		db.user.find({}, {"age": 0});
		
* 查询user集合中的age为18的第一条记录：

		db.user.findOne({"age":18});
		
* 根据查询条件找到相应的记录并对一条结果做更新操作，其中query为查询条件，sort指定排序，remove表示删除（remove和update不能同时指定），new表示返回更新之前还是之后的结果，这条命令只能返回一条结果

		db.user.findAndModify({"query":{"username":"joe"}, "sort":{"age":1}, "remove":true, "new":true});

2.查询操作符

	$lt(<) $lte(<=) $gt(>=) $gte(>=) 
	$in(匹配一个键的多个值) $nin(与列出来的所有值都不匹配)
	$all:匹配数组中所有元素，模糊匹配，不关注顺序
	$not:反向匹配
	$size:匹配数组长度

* 查询age在18～30(含)岁之间的用户：

		db.user.find({"age":{"$gte":18, "$lte":30}});
		
* 查询age为18、20、22岁的用户：

		db.user.find({"age":{"$in":[18, 20, 22]}});
		
* 查询age不为18、20、22岁的所有用户：

		db.user.find({"age":{"$nin":[18, 20, 22]}});

* 查询username不为joe的所有用户：

		db.user.find({"username":{"$not":"joe"}});

3.$exists  
判断字段是否存在

* 查询所有存在username字段的记录：

		db.user.find({"username":{"$exsits":true}});
* 查询所有不存在username字段的记录：

		db.user.find({"username":{"$exsits":false}});

4.$slice  
返回数组的一个子集合

* 查询user集合中username为joe的前10个用户：

		db.user.find({"username":"joe"}, {"$slice": 10});
		
* 查询user集合中从第21条记录开始的10条记录：

		db.user.find({"username":"joe"}, {"$slice":[20, 10]});

5.对结果排序  

* 对年龄升序：db.user.find().sort({"age":1});
* 对年龄降序：db.user.find().sort({"age":-1});

6.限制返回数据条数  

* 返回5条记录:db.user.find().limit(5)
* 从第3条记录开始，返回5条记录:db.user.find().skip(3).limit(5)
* 返回记录条数:db.user.find().count()
* 返回限制条件后的记录数量:db.user.find().skip(10).limit(5).count(1)

---

#### 插入、更新、删除
`insert save update remove`  
若存在_id主键，insert()不做操作，且提示错误，save()改变原来的内容为新内容。

**修改器**:
 
	$set:修改字段
	$unset:删除字段，不论对目标键使用1、0、-1或者具体的字符串等都是可以删除该目标键。
	$rename:修改字段字
	$push、pushAll:向数组末尾追加数据，字段不存在则创建
	$addToSet:向数组类型乐加数据时避免重复
	$each:遍历数组
	$pop:从数组中删除数据，{"$pop":{key:1}}从末尾删除，-1从头部删除
	$pull和$pullAll:从数组中删除指定数据

* 向user集合中插入单个文档：

		db.user.insert({"username":"joe", "age":18});
		
* 清空user集合：

		db.user.remove();
		
* 清除user集合中所有age为18的数据：

		db.user.remove({"age":18});
		
* 根据query条件修改，如果不存在则插入，物许修改多条记录：

		db.foo.update({"y":1}, {"$set":{"x":2}}, upsert=true, multi=true);
		
* 找到username为joe的记录，并删除email字段：

		db.user.update({"username":"joe"}, {"$unset":{"email":1}});
		
* 修改username为nickname:

		db.user.update({}, {"$rename":{"username":"nickname"}});

---

#### 修改器详解示例

	对于文档的更新除替换外，针对某个或多个文档只需要部分更新可使用原子的更新修改器，能够高效的进行文档更新。更新修改器是中特殊的键，
用来指定复杂的操作，比如增加、删除或者调整键，还可能是操作数组或者内嵌文档。

1.$inc

$inc可以对文档的某个值为数字型（只能为满足要求的数字）的键进行增减的操作。

	示例文档：{"uid":"201203","type":"1",size:10}
	> db.b.insert({"uid":"201203","type":"1",size:10})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 10 }
	> db.b.update({"uid" : "201203"},{"$inc":{"size" : 1}})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 11 }
	> db.b.update({"uid" : "201203"},{"$inc":{"size" : 2}})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 13 }
	> db.b.update({"uid" : "201203"},{"$inc":{"size" : -1}})
	> db.b.find()
	{ "_id" : ObjectId("5003b6135af21ff428dafbe6"), "uid" : "201203", "type" : "1",
	"size" : 12 }

2.$set

用来指定一个键并更新键值，若键不存在并创建，对于内嵌文档在使用$set更新时，使用"."连接的方式。

	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "sname" : "jk", "type" : "3", "uid" : "20120002" }
	
--size键不存在的场合

	> db.a.update({"uid" : "20120002","type" : "3"},{"$set":{"size":10}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "size" : 10, "sname" : "jk", "type" : "3", "uid" : "20120002" }
	
--sname键存在的场合

	> db.a.update({"uid" : "20120002","type" : "3"},{"$set":{"sname":"ssk"}})
	> db.a.find()
	{ "_id" : ObjectId("500216de81b954b6161a7d8f"), "desc" : "hello world2!", "num"
	: 40, "size" : 10, "sname" : "ssk", "type" : "3", "uid" : "20120002" }
	{ "_id" : ObjectId("50026affdeb4fa8d154f8572"), "desc" : "hello world1!", "num"
	: 50, "sname" : "jk", "type" : "1", "uid" : "20120002" }
	
--可改变键的值类型

	> db.a.update({"uid" : "20120002","type" : "3"},{"$set":{"sname":["java",".net","c++"]}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{
	        "_id" : ObjectId("500216de81b954b6161a7d8f"),
	        "desc" : "hello world2!",
	        "num" : 40,
	        "size" : 10,
	        "sname" : [
	                "java",
	                ".net",
	                "c++"
	        ],
	        "type" : "3",
	        "uid" : "20120002"
	}
	
对于内嵌的文档，$set又是如何进行更新的内嵌的文档的呢，请看下面的示例：

	示例文档：{"name":"toyota","type":"suv","size":{"height":10,"width":5,"length":15}}
	> db.c.findOne({"name":"toyota"})
	{
	        "_id" : ObjectId("5003be465af21ff428dafbe7"),
	        "name" : "toyota",
	        "type" : "suv",
	        "size" : {
	                "height" : 10,
	                "width" : 5,
	                "length" : 15
	        }
	}
	> db.c.update({"name":"toyota"},{"$set":{"size.height":8}})
	> db.c.findOne({"name":"toyota"})
	{
	        "_id" : ObjectId("5003be465af21ff428dafbe7"),
	        "name" : "toyota",
	        "type" : "suv",
	        "size" : {
	                "height" : 8,
	                "width" : 5,
	                "length" : 15
	        }
	}
	> db.c.update({"name":"toyota"},{"$set":{"size.width":7}})
	> db.c.findOne({"name":"toyota"})
	{
	        "_id" : ObjectId("5003be465af21ff428dafbe7"),
	        "name" : "toyota",
	        "type" : "suv",
	        "size" : {
	                "height" : 8,
	                "width" : 7,
	                "length" : 15
	        }
	}

3.$unset

从字面就可以看出其意义，主要是用来删除键。
使用修改器$unset时，不论对目标键使用1、0、-1或者具体的字符串等都是可以删除该目标键。

	示例操作效果如下：
	> db.a.update({"uid" : "20120002","type" : "3"},{"$unset":{"sname":1}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{
	        "_id" : ObjectId("500216de81b954b6161a7d8f"),
	        "desc" : "hello world2!",
	        "num" : 40,
	        "size" : 10,
	        "type" : "3",
	        "uid" : "20120002"
	}
	> db.a.update({"uid" : "20120002","type" : "3"},{"$unset":{"num":0}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{
	        "_id" : ObjectId("500216de81b954b6161a7d8f"),
	        "desc" : "hello world2!",
	        "size" : 10,
	        "type" : "3",
	        "uid" : "20120002"
	}
	> db.a.update({"uid" : "20120002","type" : "3"},{"$unset":{"size":-1}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{
	        "_id" : ObjectId("500216de81b954b6161a7d8f"),
	        "desc" : "hello world2!",
	        "type" : "3",
	        "uid" : "20120002"
	}
	> db.a.update({"uid" : "20120002","type" : "3"},{"$unset":{"desc":"sssssss"}})
	> db.a.findOne({"uid" : "20120002","type" : "3"})
	{
	        "_id" : ObjectId("500216de81b954b6161a7d8f"),
	        "type" : "3",
	        "uid" : "20120002"
	}

4.数组修改器--$push

$push--向文档的某个数组类型的键添加一个数组元素，不过滤重复的数据。添加时键存在，要求键值类型必须是数组；键不存在，则创建数组类型的键。

示例操作效果如下：

	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "type" : "suv",
	"size" : { "height" : 8, "width" : 7, "length" : 15 } }
	
先push一个当前文档中不存在的键title

	> db.c.update({"name" : "toyota"},{$push:{"title":"t1"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1" ], "type" : "suv" }
 
再向title中push一个值

	> db.c.update({"name" : "toyota"},{$push:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2" ], "type" : "suv" }

再向title中push一个值

	> db.c.update({"name" : "toyota"},{$push:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2" ], "type" : "suv" }

再向一个已经存在的键值非数组类型的键push一个值

	> db.c.update({"name" : "toyota"},{$push:{"size.height":10}})
	Cannot apply $push/$pushAll modifier to non-array
	> db.c.update({"name" : "toyota"},{$push:{"name":"ddddddd"}})
	Cannot apply $push/$pushAll modifier to non-array

5.数组修改器--$ne/$addToSet

主要给数组类型键值添加一个元素时，避免在数组中产生重复数据，$ne在有些情况是不通行的。

	> db.c.update({"title" : {$ne:"t2"}},{$push:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2" ], "type" : "suv" }
	> db.c.update({"name" : "toyota"},{$addToSet:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2" ], "type" : "suv" }

6.数组修改器--$pop、$pull

$pop从数组的头或者尾删除数组中的元素，

示例如下：

	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t3", "t4" ],"type" : "suv" }

从数组的尾部删除 1

	> db.c.update({"name" : "toyota"},{$pop:{"title":1}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t3" ], "type" : "suv" }

从数组的头部 -1

	> db.c.update({"name" : "toyota"},{$pop:{"title":-1}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t2", "t3" ], "type" : "suv" }

从数组的尾部删除 0

	> db.c.update({"name" : "toyota"},{$pop:{"title":0}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t2" ], "type" : "suv" }
 
$pull从数组中删除满足条件的元素，示例如下：

	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t2", "t2", "t3" ],"type" : "suv" }
	 
	> db.c.update({"name" : "toyota"},{$pull:{"title":"t2"}})
	> db.c.find()
	{ "_id" : ObjectId("5003be465af21ff428dafbe7"), "name" : "toyota", "size" : { "height" : 8,
	 "width" : 7, "length" : 15 }, "title" : [ "t1", "t3" ], "type" : "suv" }
 
7.数组的定位修改器

在需要对数组中的值进行操作的时候，可通过位置或者定位操作符（"$")，数组是0开始的，可以直接将下标作为键来选择元素。  
使用$定位操作符时，若为多个文档满足条件，则只更新第一个文档。

示例如下：

	{"uid":"001",comments:[{"name":"t1","size":10},{"name":"t2","size":12}]}
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 10 }, { "name" : "t2", "size" : 12 } ] }
	> db.c.update({"uid":"001"},{$inc:{"comments.0.size":1}})
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 11 }, { "name" : "t2", "size" : 12 } ] }
	> db.c.update({"comments.name":"t1"},{$set:{"comments.$.size":1}})
	> db.c.find({"uid":"001"})
	{ "_id" : ObjectId("5003da405af21ff428dafbe8"), "uid" : "001", "comments" : [ {
	"name" : "t1", "size" : 1 }, { "name" : "t2", "size" : 12 } ] }

8.upsert

upsert是一种特殊的更新。当没有符合条件的文档，就以这个条件和更新文档为基础创建一个新的文档，如果找到匹配的文档就正常的更新。
使用upsert，既可以避免竞态问题，也可以减少代码量（update的第三个参数就表示这个upsert，参数为true时）

	> db.c.remove()
	> db.c.update({"size":11},{$inc:{"size":3}})
	> db.c.find()
	> db.c.update({"size":11},{$inc:{"size":3}},false)
	> db.c.find()
	> db.c.update({"size":11},{$inc:{"size":3}},true)
	> db.c.find()
	{ "_id" : ObjectId("5003ded6c28f67507a6df1de"), "size" : 14 }

9.save函数

1.可以在文档不存在的时候插入，存在的时候更新，只有一个参数文档。  
2.要是文档含有"_id"，会调用upsert。否则，会调用插入。

	> db.a.find()
	{ "_id" : ObjectId("50026affdeb4fa8d154f8572"), "desc" : "hello world1!", "num": 50,
	 "sname" : "jk", "type" : "1", "uid" : "20120002" }
	> var o = db.a.findOne()
	> o.num = 55
	55
	> db.a.save(o)
	> db.a.find()
	{ "_id" : ObjectId("50026affdeb4fa8d154f8572"), "desc" : "hello world1!", "num": 55,
	 "sname" : "jk", "type" : "1", "uid" : "20120002" }

---

#### 索引管理

* 在字段firstname升序和lastname降序上创建一个名为idx_name的联合索引

		db.user.ensureIndex({"firstname":1, "lastname":-1}, {"name":"idx_name"})
		
* 在username上创建一个唯一索引

		db.user.ensureIndex({"username":1}, {"unique":true});

