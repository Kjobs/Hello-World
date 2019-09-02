### MongoDB学习记录

#### 下载安装

Windows:[下载位置](https://www.mongodb.com/download-center/community)

+ ZIP文件免安装，可直接命令行操作
- MSI文件需安装，可查官网[安装步骤](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)详情

#### 创建数据目录

> 安装不会主动创建db目录，需手动创建

在安装MongoDB那个盘的根目录下创建，例：d:/data/db

#### 运行MongoDB服务器

在MongoDB的bin目录下运行指令
  
`> mongod.exe --port 27017 --dbpath d:\data\db` 

#### 连接至MongoDB服务

`> mongo.exe --port 27017`

注：上面两个步骤也可不用指定端口，即不用`--port 27017`这段代码

#### 出现** WARNING

> Access control is not enabled for the database.Read and write access to data and configuration is unrestricted.

原因：在连接MongDB服务时，服务器希望建立安全连接，所以需要创建一个管理员(admin)角色的用户进行操作

创建管理员
```
use admin
db.createUser(
  {
    user: "myUserAdmin",
    pwd: "abc123",
    roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
  }
)
```

之后重启MongoDB服务器

`> mongod --auth --port 27017 --dbpath d:\data\db`

再连接至MongoDB服务

`> mongo --port 27017 -u "myUserAdmin" -p "abc123" --authenticationDatabase "admin"`

运气好的话，上面那个warring就可以消失了-_-
> 第二天连接服务似乎又出现了-_-貌似是因为没有加`--auth`参数，重启服务需要加上这个参数，表示以用户认证方式连接服务

#### 查看、插入或更新数据出现错误

```
> db.test.insert({"name":"aaa"})
WriteCommandError({
        "ok" : 0,
        "errmsg" : "not authorized on test to execute command *** ,
        "code" : 13,
        "codeName" : "Unauthorized"
})
```

原因（1）：没有操作权限，通过`db.getUsers()`也发现没有用户，需要添加用户（方法同创建管理员，需注意roles类型）

原因（2）：有用户，但是当前连接认证的用户没有查看当前表的操作权限，需要重新连接认证为当前表用户或超级管理员用户。

用户认证：`db.auth("db_user","db_pass")`

#### 操作符（详情[戳这里](https://docs.mongodb.com/manual/reference/operator/)）
1. 查询
2. 投影
3. 更新

### 建立索引

MongoDB默认会为插入的文档生成_id字段（如果应用本身没有指定该字段），_id是文档唯一的标识，为了保证能根据文档id快递查询文档，MongoDB默认会为集合创建_id字段的索引。

MongoDB支持多种类型的索引，包括单字段索引、复合索引、文本索引等。

#### 单字段索引
```
> db.collection.createIndex({name:1})
```
上述语句针对name域建立了索引，{name:1}表示升序索引，{name:-1}表示降序索引。对于单字段索引，升序/降序效果是一样的。

#### 复合索引
```
> db.collection.createIndex({name1:1, name2:1})
```
上述语句中的复合索引是先对name1排序，再在name1相同基础上对name2进行排序。另外，不光能满足多个字段组合起来的查询，也能满足能匹配复合索引前缀的查询;  
例{name1:1}就是{name1:1, name2:1}的前缀，所以类似 `db.collection.find({name1:"value"})` 的查询也可以通过该索引来加速。
但类似 `db.collection.find({name1:"value"})` 的查询则无法使用该复合索引，需要建立另外的复合索引  
```
db.collection.find({name2:1, name1:1})
```
若name1字段取值有限，即拥有相同name1字段的文档会有很多；而name2字段较丰富，相同那么字段的文档比较少——
则可以考虑先按name2查找，再在相同name2的文档里查找指定name1字段值的文档。所以在建立复合索引时，应把name2字段放在前面，name1置后.

#### 多键索引(Multikey index)
当索引的field为数组时,多key索引会为数组里的每一个元素建立一条索引，例(查相同兴趣爱好)：
```
{"name" : "jack", "age" : 19, habbit: ["football, runnning"]}
db.person.createIndex( {habbit: 1} )  // 自动创建多key索引
db.person.find( {habbit: "football"} )
```

#### 复合多键索引
针对多键索引，也可以建立相应的复合索引，查询条件与之前的复合索引类似。只是有一点限制：  

+ 复合多键索引的索引域(field)最多只有一个是数组  

例：
```
{ _id: 1, a: [ 1, 2 ], b: [ 1, 2 ], description: "a and b - both arrays" }
```
不能创建索引`{a:1, b:1}` ，因为a和b都是数组；  
```
{ _id: 1, a: [1, 2], b: 1, description: "a array" }
{ _id: 2, a: 1, b: [1, 2], description: "b array" }
```
则都可以创建索引`{a:1, b:1}` ，因为每个文档的索引域都只有一个是数组