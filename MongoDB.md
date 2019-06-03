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


