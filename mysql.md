### SQL语句

#### 修改root密码
```sql
> update user set password=password("new_passord") where user = "root";
```


#### ORDER BY语句  
按升序排列:
`asc`  
按降序排列 :
`desc`   
根据id字段按照降序排列，从大到小
```sql
> select * from tablename order by id desc;
```
根据id字段按照升序排列，从小到大  
```sql
> select * from tablename order by id asc;
```


#### union和union all的区别  
+ union用于合并两个或多个select语句的结果集，并消去表中任何重复项；
- union all允许重复的值，即union all不消除重复行


#### 在查询中使用正则表达式
使用regexp
```sql
> select City from Station where City regexp '^[aeiou]';
```  
使用rlike
```sql
> select distinct City from Station where City rlike '^[^aeiou]' or City rlike '[^aeiou]$';
```


#### 字段修改

显示字段
```sql
> show full columns from table; 
```

+ MySQL中使用使用alter table语句来修改字段类型，这里注意，因为我们修改字段的名称，所以SQL语句中会出现两个t_name，这并不是写错了。例:
```sql
> alter table book change Publisher Publisher varchar(40);
      Query OK, 0 rows affected (0.16 sec)
      Records: 0  Duplicates: 0  Warnings: 0
```

修改字段名
```sql
> alter table 表名称 change 字段名称 字段名称 字段类型 [是否允许非空];
```

修改字段类型
```sql
> alter table 表名称 modify 字段名称 字段类型 [是否允许非空];
```

+ 添加表字段  
```sql
> alter table table1 add transactor varchar(10) not Null;
```

+ 删除某一字段  
```sql
> ALTER TABLE mytable DROP 字段名;
```

+ 修改表中信息  
```sql
> update book set ISBN = ".." where Title = "..";
```

+ 在已有表基础上添加主键  
```sql
> alter table table_name add id int primary key auto_increment;
> alter table table_name add column id int auto_increment not null,add primary key(id);
```

+ 改变字段位置  
```sql
> ALTER TABLE 表名 MODIFY 字段名1 数据类型 FIRST ｜ AFTER 字段名2;
```

+ 删除表中所有信息  
```sql
> delete from table; #不立即删除；
> truncate table employee; #删除包含自增主键在内所有信息
```

  
#### 修改记录
例：创建一个新闻表：
```sql
mysql> create table news_table(
     > id int(5) primary key not null auto_increment,
     > title nvarchar(100) not null,
     > news_url varchar(150) not null,
     > news_date varchar(20) not null,
     > source varchar(15) not null,
     > type varchar(20) not null);
```
删除重复记录  
```sql
> delete from edu_news where id not in (select * from (select max(id) from edu_news group by title having count(id) > 1) as b);
```


#### 修改用户
1. 添加用户并分配权限  
授权格式：`grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";`  
例：`grant all privileges on wordpress.* to user@localhost identified by "password";`
2. 删除用户
```sql
> use msyql;
> delete from user where user = "";
```


#### 将table导出为csv格式
自动输出中文字符集  
```sql
> select * from db into outfile 'test.csv' CHARACTER SET gbk fields terminated by ',' optionally enclosed by '"' escaped by '"' lines terminated by '\r\n';
```


#### 将xls/txt文件导入数据库

1) txt---需要将txt另存为无BOM的utf-8格式的txt文件  
```sql
> load data local infile "D:\data.txt" into table table_name;
```

2) xls---只需将xls中的一个表另存为txt文件即可。

#### 查看MySQL服务占用端口
使用命令直接查看
```sql
> show global variables like "port";
```

---

### 语法及规范

#### CHAR和VARCHAR的区别
 > char(M)类型的数据列里，每个值都占用M个字节，如果长度小于M，MYSQL则用空格补足；  
 > 在varchar类型的数据列里，每个值只占字符字节数加一个用于记录字长的字节(即总长度L+1)
+ char是一种固定长度的类型，varchar是一种可变长度的类型；
+ 如果字符串字段总是具有设置的长度(例如，状态简写等)就是用CHAR，否则使用VARCHAR。


#### 查询中的引号
（1）数值不应该用引号括住；  
（2）字符串值（对于CHAR，VARCHAR以及TEXT列类型）必须用引号括住；  
（3）日期和时间值必须用引号括住；  
（4）函数不能用引号括住；  
（5）单词NULL不能用引号括住；  
（6）需要在数值中使用引号时，使用转义字符。


### MySQL EXPLAIN
> explain命令可以获取select语句的执行计划，在查询性能优化中是很重要的部分

#### Explain Output Column

explain命令每个字段的说明

| 字段 | 说明 |
| :---: | :---: |
|id|执行编号，标识select所属的行，按select出现的顺序增长，id越大优先级越高，越先被执行|
|select_type|每个select子句的类型，简单或复杂查询
|table|访问引用的表名（或某个查询的引用，如“derived1”）
|partitions|若是基于分区表的查询，显示将访问的分区
|type|MySQL在表中找到所需行的方式，关联或访问类型（ALL、index、range、ref、eq_ref、const/system、NULL）
|possible_keys|指出哪一些索引可能有利于高效的查询，查询所涉及到的索引均会被列出
|key|显示MySQL实际使用的键（索引）来做优化查询
|key_len|显示索引字段的最大可能长度（字节数），根据表定义来计算的
|ref|显示之前的表在key列记录的索引中查询值所用的列或常量
|rows|根据表统计信息及索引选用情况，估算找到所需记录所需要读取的行数
|filtered|
|Extra|额外信息，如using index、filesort等


补充说明

* EXPLAIN不会告诉你关于触发器、存储过程的信息或用户自定义函数对查询的影响情况
* EXPLAIN不考虑各种Cache
* EXPLAIN不能显示MySQL在执行查询时所作的优化工作
* 部分统计信息是估算的，并非精确值
* EXPALIN只能解释SELECT操作，其他操作要重写为SELECT后查看执行计划
