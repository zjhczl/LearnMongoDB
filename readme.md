- [1. MongoDB安装与配置](#1-mongodb安装与配置)
  - [1.1. windows](#11-windows)
  - [1.2. ubuntu](#12-ubuntu)
  - [1.3. mongodb 的三个默认的库](#13-mongodb-的三个默认的库)
  - [1.4. 权限管理](#14-权限管理)
    - [1.4.1. 切换到admin数据库进行权限管理](#141-切换到admin数据库进行权限管理)
    - [1.4.2. 为数据库添加账号](#142-为数据库添加账号)
    - [1.4.3. 修改配置文件](#143-修改配置文件)
    - [1.4.4. 使用账户登录数据库](#144-使用账户登录数据库)
- [2. 数据库常用命令](#2-数据库常用命令)
  - [2.1. 数据库相关](#21-数据库相关)
    - [2.1.1. 查看数据库](#211-查看数据库)
    - [2.1.2. 创建数据库](#212-创建数据库)
    - [2.1.3. 集合](#213-集合)
  - [2.2. 文档的增删改](#22-文档的增删改)
    - [2.2.1. 插入数据](#221-插入数据)
    - [2.2.2. 修改](#222-修改)
    - [2.2.3. 删除](#223-删除)
  - [2.3. 分页查询和排序](#23-分页查询和排序)
    - [2.3.1. 分页](#231-分页)
    - [2.3.2. 排序](#232-排序)
    - [2.3.3. 正则查询](#233-正则查询)
    - [2.3.4. 比较查询](#234-比较查询)
    - [2.3.5. 包含查询](#235-包含查询)
    - [2.3.6. 条件查询](#236-条件查询)
  - [2.4. 索引](#24-索引)
    - [2.4.1. 查看索引](#241-查看索引)
    - [2.4.2. 创建索引](#242-创建索引)
    - [2.4.3. 删除索引](#243-删除索引)
- [3. 用例(文章评论)](#3-用例文章评论)


# 1. MongoDB安装与配置
## 1.1. windows

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
## 1.2. ubuntu

安装mongodb
```
apt install mongodb
```

开启数据库
```bash
sudo service mongodb start
sudo service mongodb stop
sudo service mongodb restart
```

远程连接数据库
```bash
mongo mongodb://my.zjhczl.xyz
```
## 1.3. mongodb 的三个默认的库

admin：从权限角度看这是一个 root 库。要是将用户添加到这个数据库，这个用户自动继承所有数据库权限。一些特定的服务端命令也只能从这个数据库运行，比如关闭服务。

local：这个数据库的数据永远不会被复制，可以用来储存本地单机的集合

config：当 mongo 用于分片设置时，config 数据库在内部使用，用于保存分片的相关信息。

## 1.4. 权限管理

### 1.4.1. 切换到admin数据库进行权限管理
```bash
use admin
```

### 1.4.2. 为数据库添加账号
例：为test数据库添加一个admin账号
```bash
db.createUser({user: "admin", pwd: "123456",roles: [ { role: "dbOwner", db: "test"}]})
```
role可以有一下选择:

        read: 只能读取指定数据库
        readWrite: 能读写指定数据库
        dbAdmin: 能执行管理函数，如索引创建、删除，查看统计或访问 system.profile
        dbOwner: 对当前数据库有全部权限
        userAdmin: 能创建、删除和管理用户
        clusterAdmin: 只能在 admin 数据库中可用，能赋予用户所有分片和复制集相关函数的管理权限
        readAnyDatabase: 只能在 admin 数据库中可用，能赋予用户所有数据库的读权限
        readWriteAnyDatabase: 只能在 admin 数据库中可用，能赋予用户所有数据库的读写权限
        userAdminAnyDatabase: 只能在 admin 数据库中可用，能赋予用户所有数据库的 userAdmin 权限
        dbAdminAnyDatabase: 只能在 admin 数据库中可用，能赋予用户所有数据库的 dbAdmin 权限
        root: 只能在 admin 数据库中可用。超级权限

### 1.4.3. 修改配置文件

需要开启auth才能只用配置的账户
```bash
auth = true
```
### 1.4.4. 使用账户登录数据库
```bash
db.auth("username","passwd")
```

# 2. 数据库常用命令

## 2.1. 数据库相关

### 2.1.1. 查看数据库

```
> show dbs
admin   0.000GB
config  0.000GB
local   0.000GB
```

### 2.1.2. 创建数据库

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

### 2.1.3. 集合

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

## 2.2. 文档的增删改

### 2.2.1. 插入数据

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

### 2.2.2. 修改

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

### 2.2.3. 删除

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

## 2.3. 分页查询和排序

### 2.3.1. 分页

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

### 2.3.2. 排序

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

### 2.3.3. 正则查询

```bash
> db.my.find({name:/1/}) # 查询name包含1的的所有数据
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
```

### 2.3.4. 比较查询

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

### 2.3.5. 包含查询

```bash
> db.my.find({name:{$in:["zj1","zj2"]}})
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ed"), "name" : "zj2", "age" : 18 }
```

### 2.3.6. 条件查询

```bash
> db.my.find({$and:[{name:"zj1"},{age:{$gt:11}}]}) # 名字zj，age大于11
{ "_id" : ObjectId("637f02da559f1d9a1b08a2ec"), "name" : "zj1", "age" : 17 }
```

## 2.4. 索引

### 2.4.1. 查看索引

```bash
> db.my.getIndexes()
[ { "v" : 2, "key" : { "_id" : 1 }, "name" : "_id_" } ]
```

### 2.4.2. 创建索引

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

### 2.4.3. 删除索引

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

# 3. 用例(文章评论)

![](./img/0.png)
