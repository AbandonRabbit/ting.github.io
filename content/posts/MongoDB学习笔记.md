+++
title = 'MongoDB学习笔记'
date = 2024-10-02T16:26:17+08:00
categories =  ["技术文档","学习笔记"] 
tags = ["技术文档","学习笔记","MongoDB"]

+++

# 概念

非关系型数据库，文档型数据库

文档类似于mysql中的行

数据库由一个个集合组成，集合对应mysql中的表，每个集合包含多个文档，每个文档对应mysql中的一行，文档使用json的数据格式来组织和存储数据，不同的文档中间不需要有相同的结构和字段

MongoDB 不需要事先创建好数据库和集合然后再插入数据，也不需要预先定义好集合中字段的数据类型和长度，同一个集合中的数据也不需要有相同的结构

# 常用命令

显示所有数据库`databases` 或 `show dbs`

切换当前数据库 `use 数据库名称`

只有在插入集合之后, 数据库才会被真正地创建

插入一个文档到集合中 `db.集合名.insertOne({key:"value"})`

查看数据 `db.集合名.find()`

插入多条数据到集合中 `db.集合名.insertMany([{key:"value"},{key:"value"}])`

# 查询操作

limit() —— 返回n条数据

~~~c
db.集合名.find().limit(n)
~~~



sort() —— 排序 

~~~c
-- 1表示按升序排序，-1表示降序
db.集合名.find().sort({level:1}) 

按照等级和名字排序
db.集合名.find().sort({level: 1, name: -1})
~~~

skip() —— 跳过一些查询数据

~~~c
返回第二名开始的数据
db.集合名.find().sort({level:1}).limit(2).skip(1)

一般skip函数和sort函数一起使用实现分页功能
~~~

find({field:value}) —— 条件查询

~~~c
db.集合名.find({level:3})  --查询等级为3的用户

db.集合名.find({level:3},{name:1,email:1})  --只返回name和email字段

db.集合名.find({level:3},{email:0})  --返回除了email之外的所有字段

db.集合名.find({level:{$gt:3}})  --查询等级大于3的用户
还有 lt小于，eq等于

查询某个字段的值是否在一个数组中
db.集合名.find({level:{$in:[1,3]}})  --返回等级为1或者3的用户
Not IN:表示不再这个数组中的值

判断某个字段是否存在，不能查看某个字段的值是否存在，
db.集合名.find({email:{$exists:true}})  --查询有邮箱的用户

多条件查询，默认会把多个条件组成一个and条件的查询
db.集合名.find({level:{$gte:3,$lte:5}})  -- 查询等级大于等于3小于等于5的用户
也可以显示指定and，把两个条件放到一个数组里面
db.集合名.find({$and:[{level:{$gte:3}},{level:{$lte,5}}]})  -- 查询等级大于等于3小于等于5的用户，把and换成 or 表示或者关系

db.集合名.find({level:{$not:{$eq:3}}})  --查询等级不等于3的用户

db.集合名.find({name:{$reges:/张/}})  --查询名字中姓张的用户，张所在的位置为正则表达式
~~~

countDocuments() —— 统计文档数量

可以传入查询条件

findOne() —— 查询满足条件的第一条数据

updateOne() —— 更新一条数据

updateMany() —— 更新多条数据

*如果字段不存在，会创建这个字段，并把值写入这个字段*

~~~c
把等级为1 的用户的钱更新为100
db.集合名.updataOne({level:1},{$set:{money:100}})
~~~

deleteOne() —— 删除一条数据

deleteMany() —— 删除满足条件的多条数据

~~~c
删除等级为1的用户
db.集合名.deleteOne({level:1})
~~~



```c
//切换数据库
use userV1

//输出所有集合
show collections

//创建集合
db.createCollection('user')

//删除集合
db.user.drop()

//插入数据
db.user.insertOne({"name":"name1","age":12})

//查询所有数据
db.user.find()

for (let i = 0; i < 10; i++) {
    db.user.insertOne({"name":"name"+i,"age":i})
}

//删除数据
db.user.deleteMany({})

db.user.updateMany({},{"$unset":{aeg:""}})

//修改
db.user.findOneAndUpdate(
    {name:"name10"},
    {$set:{name:"name2"}}
)

//递增
db.user.updateMany(
    {name:"name2"},
    {$inc:{age:-2}}
)
//  修饰器       作用
//  $inc        递增
//  $rename     重命名列
//  $set        修改列值
//  $unset      删除列

//多个修饰器
db.user.updateMany(
    {username:"gcc"},
    {
        $set:{username:"barech"},
        $inc:{age:5},
        $rename:{sex:"sexuality"},
        $unset:{address:true}
    }
)

// 运算符    作用
// $gt      大于
// $gte     大于等于
// $lt      小于
// $lte     小于等于
// $ne      不等于
// $in      in
// $nin     not in

db.user.find()

//查询指定列
db.user.find({},{name:1})

//排序  1:升序  -1:降序
db.user.find({},{age:1}).sort({age:-1})

//分页 skip:跳过    limit:查询数量      count:计数
db.user.find({},{age:1}).sort({age:-1}).skip(0).limit(5).count()

for (let i = 0; i < 10; i+=2) {
    db.user.find().sort({age:-1}).skip(i).limit(2)
}

// 常用管道：
// $group   将集合中的文档分组，用于统计结果
// $match   过滤数据，只输出符合条件的文档
// $sort    聚合数据进一步排序
// $skip    跳过指定文档数
// $limit   限制集合数据返回文档数
//
// 常用表达式：
// $sum 总和（$num:1同count表示统计）
// $avg 平均
// $min 最小值
// $max 最大值

db.user.find()

db.user.insertOne({_id:1,name:"a",sex:"男",age:21})
db.user.insertOne({_id:2,name:"b",sex:"男",age:20})
db.user.insertOne({_id:3,name:"c",sex:"女",age:20})
db.user.insertOne({_id:4,name:"d",sex:"女",age:18})
db.user.insertOne({_id:5,name:"e",sex:"男",age:19})

//分组查询  统计男生、女生的总年龄
db.user.aggregate([
    {$group:{_id:"$sex",age_sum:{$sum:"$age"}}}
])
//分组查询  统计男生、女生的总人数
db.user.aggregate([
    {$group:{_id:"$sex",sum:{$sum:1}}}
])

//分组查询 无分组条件查询 求学生总数和平均年龄
db.user.aggregate([
    {$group:{_id:null,_num:{$sum:1},_avg:{$avg:"$age"}}}
])

// 查询男生、女生人数，按人数升序
db.user.aggregate([
    {$group:{_id:"$sex",_num:{$sum:1}}},
    {$sort:{_num:1}}
])


for(var i=100000;i<1000000;i++){
    db.data.insertOne({name:"zsr"+i,age:i})
}

db.user.find({name:"a"})
db.data.find({name:"zsr999999"})

//查询索引
db.data.getIndexes()

//创建索引
db.data.createIndex({name: 1})

//创建索引并指定名称
db.data.createIndex({age:1},{name:"age_up"})

//删除索引
db.data.dropIndex("name_1")

//创建符合索引
db.data.createIndex({name:1,age:1})

db.data.dropIndexes("_id_")

db.data.createIndex({name:1},{unique:"name"})
```



| 已弃用的方法                | 替代资源                                                     |
| :-------------------------- | :----------------------------------------------------------- |
| `db.collection.copyTo()`    | 聚合阶段：[`$out`](https://www.mongodb.com/zh-cn/docs/manual/reference/operator/aggregation/out/#mongodb-pipeline-pipe.-out) |
| `db.collection.count()`     | [`db.collection.countDocuments()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.countDocuments/#mongodb-method-db.collection.countDocuments)[`db.collection.estimatedDocumentCount()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.estimatedDocumentCount/#mongodb-method-db.collection.estimatedDocumentCount) |
| `db.collection.insert()`    | [`db.collection.insertOne()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.insertOne/#mongodb-method-db.collection.insertOne)[`db.collection.insertMany()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.insertMany/#mongodb-method-db.collection.insertMany)[`db.collection.bulkWrite()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.bulkWrite/#mongodb-method-db.collection.bulkWrite) |
| `db.collection.remove()`    | [`db.collection.deleteOne()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.deleteOne/#mongodb-method-db.collection.deleteOne)[`db.collection.deleteMany()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.deleteMany/#mongodb-method-db.collection.deleteMany)[`db.collection.findOneAndDelete()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.findOneAndDelete/#mongodb-method-db.collection.findOneAndDelete)[`db.collection.bulkWrite()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.bulkWrite/#mongodb-method-db.collection.bulkWrite) |
| `db.collection.save()`      | [`db.collection.insertOne()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.insertOne/#mongodb-method-db.collection.insertOne)[`db.collection.insertMany()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.insertMany/#mongodb-method-db.collection.insertMany)[`db.collection.updateOne()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.updateOne/#mongodb-method-db.collection.updateOne)[`db.collection.updateMany()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.updateMany/#mongodb-method-db.collection.updateMany)[`db.collection.findOneAndUpdate()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.findOneAndUpdate/#mongodb-method-db.collection.findOneAndUpdate) |
| `db.collection.update()`    | [`db.collection.updateOne()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.updateOne/#mongodb-method-db.collection.updateOne)[`db.collection.updateMany()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.updateMany/#mongodb-method-db.collection.updateMany)[`db.collection.findOneAndUpdate()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.findOneAndUpdate/#mongodb-method-db.collection.findOneAndUpdate)[`db.collection.bulkWrite()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/db.collection.bulkWrite/#mongodb-method-db.collection.bulkWrite) |
| `DBQuery.shellBatchSize`    | [config.set("displayBatchSize", "")](https://www.mongodb.com/zh-cn/docs/mongodb-shell/reference/configure-shell-settings-api/#std-label-mongosh-config-set)[cursor.batchSize()](https://www.mongodb.com/zh-cn/docs/mongodb-shell/reference/methods/#std-label-mongosh-cursor-methods) |
| `Mongo.getSecondaryOk`      | [`Mongo.getReadPrefMode()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/Mongo.getReadPrefMode/#mongodb-method-Mongo.getReadPrefMode) |
| `Mongo.isCausalConsistency` | [`Session.getOptions()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/Session/#mongodb-method-Session.getOptions) |
| `Mongo.setSecondaryOk`      | [`Mongo.setReadPref()`](https://www.mongodb.com/zh-cn/docs/manual/reference/method/Mongo.setReadPref/#mongodb-method-Mongo.setReadPref) |
| `rs.secondaryOk`            | 不再需要。请参阅[从节点上的读取操作](https://www.mongodb.com/zh-cn/docs/mongodb-shell/reference/compatibility/#std-label-read-on-secondary-nodes)。 |





[go语言操作mongodb最近学习在go中操作mongodb，了解到主要有第三方mgo和官方mongo-driver两个 - 掘金 (juejin.cn)](https://juejin.cn/post/6908063164726771719?searchId=2024100720300332EB02809B22F5F44864#heading-46)
