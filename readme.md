[toc]

# LearnMongoDB

## 下载链接

1.下载安装 mongodb https://www.mongodb.com/try/download/community

2.配置环境变量 C:\Program Files\MongoDB\Server\5.0\bin 到 Path

3.在 c 盘根目录创建 data/db

4.开启数据库

```
mongod
```

如果不使用默认设置
则:

```
mongod --dbpath C:\Users\DELL\Desktop\data\db --port 10086
```

5.连接数据库

```
mongodb
```

```
momgo --host=127.0.0.1 --port=12707
```

## mongodb 的三个默认的库

admin：从权限角度看这是一个 root 库。要是将用户添加到这个数据库，这个用户自动继承所有数据库权限。一些特定的服务端命令也只能从这个数据库运行，比如关闭服务。

local：这个数据库的数据永远不会被复制，可以用来储存本地单机的集合

config：当 mongo 用于分片设置时，config 数据库在内部使用，用于保存分片的相关信息。

## 数据库常用命令

查看数据库

```
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
```

创建数据库

```bash
> use zjtest
switched to db zjtest
> show dbs #看不到zjtest是因为创建在内存里面，并不存在于磁盘，但是成功了
admin   0.000GB
config  0.000GB
local   0.000GB
> db #使用db查看当前数据库
zjtest
```

删除持久化的数据库

```
> db.dropDatabase()
{ "ok" : 1 }
```

集合

```bash
> db.createCollection("my") #创建集合
{ "ok" : 1 }
> show collections
my
> db.my.drop() #删除集合
true
> show collections
>
```

## 文档的增删改查

```bash
> db.my.insert({"zj":"test",name:"zj",age:26,others:{}})  #my集合自动创建
WriteResult({ "nInserted" : 1 })
> db.my.find() #查看集合数据
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "zj" : "test", "name" : "zj", "age" : 26, "others" : {  } }

#批量插入数据
> db.my.insertMany([{name:'zj1'},{name:'zj2'},{name:'zj3'}])
{
        "acknowledged" : true,
        "insertedIds" : [
                ObjectId("637ee87a559f1d9a1b08a2e8"),
                ObjectId("637ee87a559f1d9a1b08a2e9"),
                ObjectId("637ee87a559f1d9a1b08a2ea")
        ]
}
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "zj" : "test", "name" : "zj", "age" : 26, "others" : {  } }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }

#按条件查询
> db.my.find({name:"zj2"}) #只查一条数据用：findOne()
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
#查询显示部分字段
> db.my.find({name:"zj"})
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "zj" : "test", "name" : "zj", "age" : 26, "others" : {  } }
> db.my.find({name:"zj"},{age:1})
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "age" : 26 }
> db.my.find({name:"zj"},{age:1,_id:0})
{ "age" : 26 }
```

修改

```bash
db.my.update({name:"zj"},{age:27})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "age" : 27 } #被全部修改了
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }

> db.my.update({age:27},{name:"zj",age:26})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "name" : "zj", "age" : 26 }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }

#局部更新
> db.my.update({name:"zj"},{$set:{age:15}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "name" : "zj", "age" : 15 }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }
```

update 默认只修改一条数据，想要同时修改多条数据需加上第三个参数

```bash
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "name" : "zj", "age" : 15 }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }
{ "_id" : ObjectId("637f00a0559f1d9a1b08a2eb"), "name" : "zj", "age" : 46 }

> db.my.update({name:"zj"},{$set:{age:18}},{multi:true})
WriteResult({ "nMatched" : 2, "nUpserted" : 0, "nModified" : 2 })

> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "name" : "zj", "age" : 18 }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }
{ "_id" : ObjectId("637f00a0559f1d9a1b08a2eb"), "name" : "zj", "age" : 18 }

```

删除

```bash
> db.my.find()
{ "_id" : ObjectId("637ee7bf559f1d9a1b08a2e7"), "name" : "zj", "age" : 18 }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }
{ "_id" : ObjectId("637f00a0559f1d9a1b08a2eb"), "name" : "zj", "age" : 18 }
> db.my.remove({name:"zj"}) #删除所有符合条件的数据
WriteResult({ "nRemoved" : 2 })
> db.my.find()
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e8"), "name" : "zj1" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2e9"), "name" : "zj2" }
{ "_id" : ObjectId("637ee87a559f1d9a1b08a2ea"), "name" : "zj3" }

#删除文档所有数据
> db.my.remove({})
WriteResult({ "nRemoved" : 3 })
> db.my.find()
>
```

## 分页查询和排序

分页

```bash
> db.my.find()
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }
> db.my.count()
3
> db.my.count({name:"zj1"})
1

#显示查询的前两条数据
> db.my.find().limit(2)
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }

#跳过第一条数据
> db.my.find().skip(1)
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }

#跳过第一数据，并且显示查询的前一条数据
> db.my.find().skip(1).limit(1)
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
```

排序

```bash
> db.my.find().sort({age:1}) #升序
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }

> db.my.find().sort({age:-1}) #降序
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
```

正则查询

```bash
> db.my.find({name:/1/}) # 查询name包含1的的所有数据
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
```

比较查询

```bash
> db.my.find({age:{$gt:18}})) # >
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }

> db.my.find({age:{$lt:18}}) # <
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }

> db.my.find({age:{$gte:18}}) # >=
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }

> db.my.find({age:{$lte:18}}) # <=
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }

> db.my.find({age:{$ne:18}}) # !=
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ee"), "name" : "zj3", "age" : 19 }
```

包含查询

```bash
> db.my.find({name:{$in:["zj1","zj2"]}})
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
```

条件查询

```bash
> db.my.find({$and:[{name:"zj1"},{age:{$gt:11}}]}) # 名字zj，age大于11
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
```

## 索引

查看索引

```bash
> db.my.getIndexes()
[ { "v" : 2, "key" : { "_id" : 1 }, "name" : "_id_" } ]
```

创建索引

```bash
> db.my.createIndex({name:1}) #创建索引 1是升序，-1降序
{
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "createdCollectionAutomatically" : false,
        "ok" : 1
}
> db.my.getIndexes()
[
        {
                "v" : 2,
                "key" : {
                        "_id" : 1
                },
                "name" : "_id_"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1
                },
                "name" : "name_1"
        }
]

# 复合索引
> db.my.createIndex({name:1,age:1})
{
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 3,
        "createdCollectionAutomatically" : false,
        "ok" : 1
}
> db.my.getIndexes()
[
        {
                "v" : 2,
                "key" : {
                        "_id" : 1
                },
                "name" : "_id_"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1
                },
                "name" : "name_1"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1,
                        "age" : 1
                },
                "name" : "name_1_age_1"
        }
]
```

删除索引

```bash
> db.my.dropIndex({name:1})
{ "nIndexesWas" : 3, "ok" : 1 }
> db.my.getIndexes()
[
        {
                "v" : 2,
                "key" : {
                        "_id" : 1
                },
                "name" : "_id_"
        },
        {
                "v" : 2,
                "key" : {
                        "name" : 1,
                        "age" : 1
                },
                "name" : "name_1_age_1"
        }
]

#根据名称删
> db.my.dropIndex("name_1_age_1")
{ "nIndexesWas" : 2, "ok" : 1 }
> db.my.getIndexes()
[ { "v" : 2, "key" : { "_id" : 1 }, "name" : "_id_" } ]

#删除所有索引
> db.my.dropIndexes()
{
        "nIndexesWas" : 1,
        "msg" : "non-_id indexes dropped for collection",
        "ok" : 1
}
```

## 用例(文章评论)

![](./img/0.png)
