# mongodb

```
一.mongodb
官网:https://www.mongodb.com
下载:https://www.mongodb.com/download-center?jmp=nav#community

二.安装
数据目录(默认): /data/db
命令启动: ./mongod
指定数据目录: ./mongod --dbpath=/data/db2

三.后台管理
./mongo

计算：
2+2
4

插入数据:
db.runoob.insert({x:10})
查找数据:
db.runoob.find()

四.概念
数据库
(单个实例可以容纳多个独立的数据库[data目录])	//默认数据库test,如果没创建数据库集合将存放在test数据库中
创建数据库语法:use database_name //如果数据库不存在,则创建数据库,否则切换到指定数据库
查看当前数据库:db
删除当前数据库:db.dropDatabase()
显示所有数据库:show dbs	//新创建的数据库必须insert数据后才可以通过此命令查看到
↓
集合
创建集合语法:db.createCollection(name,options) //options:capped,autoIndexId,size,max
查看所有集合:show collections
删除集合:db.name.drop()
自动创建集合:db.lyc.insert({"name":"liyuan_ctest"}) //自动创建lyc集合
↓
文档
使用insert或save
插入文档语法:db.COLLECTION_NAME.insert(document)
删除文档语法:db.collection.remove(<query>,<justOne,writeConcern>)

var doc1=({name:liyuan}) //定义变量
db.lyc.insert(doc1)	//插入操作

插入单条:db.collection.insertOne({"a": 3})
插入多条:db.collection.insertMany([{"b": 3}, {'c': 4}])

insert与save区别?
1.insert: 若新增数据的主键已经存在,则会抛 org.springframework.dao.DuplicateKeyException 异常提示主键重复,不保存当前数据。
2.save: 若新增数据的主键已经存在,则会对当前已经存在的数据进行修改操作。

save与update区别？
都可以更新数据

笔记：
mongod --dbpath=/home/liyuan/soft/mgdata
```

