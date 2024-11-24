## SQL规范：

1. 所有数据库名、表名、列名使用小写字母，不用数字开头
2. 字母和字母之间使用_下横线分开：information_schema
3. 表名不适用复数，比如学生表不要用students而是student表
4. 参考：主键索引名pk_xxx，唯一索引名uk_xxx，普通索引idx_xxx
5. 注释：-- 表示单行注释；/**/ 表示多行注释，# 为MySQL方言注释，一般不用
6. 方言：SQL是标准语言，每个数据库又有各自的不同语法，叫做数据库的方言



###### 修改密码：

​	alter user root@'localhost' identified by '12345678';

###### 忘记密码：

​	1. 管理员运行cmd，输入：net stop mysql，停止mysql服务
​	2. 使用无验证方式启动mysql服务：mysqld --console --skip-grant-tables --shared-memory
​	3. 打开新的cmd窗口，直接输入命令：mysql -uroot，敲回车就可以登录成功
​	4. 清空密码：update mysql.user set authentication_string='' where user='root' and host='localhost';
​	5. 关闭两个cmd窗口（可能需要关闭mysqld.exe进程），重启管理员cmd窗口，重启服务：net start mysql
​	6. 无密码登录：mysql -uroot
​	7. 重设密码：alter user 'root'@'localhost' identified by '12345678';
​	8. 退出，重进：mysql -uroot -p，使用新密码登录

###### 删除后无法创建用户的问题：

1. 可以换一个新名字创建一个其他用户，一般能成功
2. 如果'xxx'@'localhost'能创建，但是'xxx'@'%'不能创建，那么可以在创建完后修改host即可
3. 记得刷新权限flush privileges;



###### 统一客户端编码：

​	查看编码：show variables like 'character_set%';
​	set character_set_client=gbk;
​	set character_set_connection=gbk;
​	set character_set_results=gbk;
​	或者 set names gbk; 等同于上面3步



###### 数据库设计

- ​	1:1 一对一关系：比如一个学生一个学号；文章与内容
  ​		任意一方添加外键即可，不推荐！
  ​		在Java程序中实现约束
- ​	1:n(n:1) 一对多关系、多对一：比如一个部门多员工，一个员工只有一个部门；文章与评论
  ​		可以使用外键约束，但是不推荐！
  ​		多方建立外键，约束于一方的主键
- ​	n:n 多对多关系：一个学生多个老师，一个老师带多个学生；文章与标签
  ​		设计为三张表：需要一张中间表
  ​		中间表至少两个字段：分别为两个表的主键



###### 用户控制：

- ​	创建：create user '用户名'@'用户所在主机名' identified by '密码';
  ​		通配符 % 表示用户可以从任意主机登录，比如：'root'@'%'
- ​	授权：grant 权限1,权限2... on 数据库名.表名 to '用户名'@'用户主机';
  ​		这里的权限可以是：create/alter/select...
  ​		使用 all 表示所有的权限，使用 *.* 表示所有数据库所有表
- ​	示例：
  ​		create user 'test'@'localhost' identified by '12345678';
  ​		grant create,alter,insert,update,select on mydb.* to 'test'@'localhost';
  ​		grant all privileges on *.* to 'admin'@'%';
  ​		flush privileges;
- ​	可视化登录工具，默认不是8.0的加密sha2方式，可以改用native加密方式：
  ​		alter user 'root'@'localhost' identified with mysql_native_password by '12345678';
- ​	查询所有用户、权限：
  ​		use myql;
  ​		select user,host from user;
  ​		show grants for '用户名'@'主机名';



###### 事务（Transaction）

​	有时候处理数据的操作中含有多个业务步骤，这些操作可以看做一个整体叫做一个事务
​	事务管理要求：这些操作要么全部成功，要么全部失败；成功也即提交事务，失败即回滚事务
​	开启操作：
​		start transaction; -- 开启事务
​		rollback; -- 回滚
​		commit; -- 提交事务

###### MySQL事务：

​		支持自动提交：默认情况下自动提交，每一条增删改语句算一个事务
​		可以修改事务提交方式：set @@autocommit=0; select @@autocommit;
​		或者：set autocommit=1; show variables like 'autocommit'; 设置为1表示自动

###### 事务特性（ACID）：

1. 原子性（Atomicity）：事务是不可分割的最小操作单位，要么同时成功，要么同时失败
2. 一致性（Consistency）：事务操作前后，数据保持完整性约束，数据状态合法一致
3. 隔离性（Isolation）：多个事务之间相互独立，一个事务内部不会影响另一个事务内部
4. 持久性（Durability）：当事务一旦提交后，数据的变化应该是永久性的

###### 隔离级别（Isolation Level）：

|          隔离级别           | 脏读 | 不可重复读 | 幻读 |
| :-------------------------: | :--: | :--------: | :--: |
| Read Uncommitted - 读未提交 |  √   |     √      |  √   |
|  Read Committed - 读已提交  |  ×   |     √      |  √   |
| Repeatable Read - 可重复读  |  ×   |     ×      |  √   |
|    Serializable - 串行化    |  ×   |     ×      |  ×   |

​	如果多个事务操作同一批数据，则会引发数据不一致的问题，可以通过设置不同的隔离级别避免这些问题
​	可能存在的问题：
​		脏读：一个事务读取到另一个事务中【未提交】的数据
​		不可重复读：即虚读，在【同一个事务】中两次【读取】到的数据不一样
​		幻读：一个事务以相同的条件前后两次查询数据，得到的结果不一致
​	隔离级别：
​		Read Uncommitted - 读未提交，产生的问题：脏读、不可重复读、幻读
​		Read Committed - 读已提交（Oracle默认），产生的问题：不可重复读、幻读
​		Repeatable Read - 可重复读 （MySQL默认），产生的问题：幻读
​		Serializable - 串行化可以解决所有的问题，无法支持高并发
​	效率：隔离级别从小到大，安全性越高，但是效率越低
​	设置：
​		show variables like 'transaction_isolation'; -- 查看
​		或者：select @@transaction_isolation; -- 旧版本使用 @@tx_isolation
​		set [global] transaction isolation level xxx; -- global全局，session当前
​		设置全局隔离级别不会影响当前连接，需要重新打开新的会话，

------



## 数据类型：

​		int/integer [unsigned] 整数类型，4字节
​		float/double 小数类型，4/8字节
​		decimal(m,d) m表示总长度，d为小数部分，m-d整数部分
​		char(n) 固定长度的字符串，n个字符
​		varchar(n) 变长字符串，最大长度为n
​		text/blob 长文本、二进制数据
​		date/time 日期(yyyy-MM-dd)、时间(HH:mm:ss)格式，3字节
​		datetime/timestamp 日期和时间组合格式(yyyy-MM-dd HH:mm:ss)，timestamp表示时间戳
​		datetime 8个字节，固定，不能做加减运算，存储null即null
​		timestamp 4个字节，与时区有关，可以加减，存储null表示当前时间

------



## 表的操作：

###### 表查询：

```sql
-- 显示所有数据库
SHOW DATABASES;

-- 显示当前数据库所有表
SHOW TABLES;

-- 查看表创建语句
SHOW CREATE DATABASE mydb;

-- 查看表的详细描述，包括字段
DESC student1;

-- 查看字符集相关设置
SHOW VARIABLES LIKE 'character_set%';
-- 设置编码
SET chsrscter_set_client=utf8mb4;
-- 设置character_set_client，character_set_connection，character_set_results
SET NAMES utf8;
```

###### 创建：

```sql
-- 复制表结构
CREATE TABLE student0 LIKE student;
create table 表名(字段1 类型, 字段2 类型 ..., 约束等); -- 创建
create table if not exists 表名(... -- 判断不存在则创建
```

###### 修改：

```sql
-- 修改表名
ALTER TABLE student0 RENAME TO student1;
-- 修改字段属性，MODIFY不能改名，CHANGE改名字和属性
ALTER TABLE student1 MODIFY birthday TIMESTAMP;	-- (数据类型)
ALTER TABLE student1 CHANGE birthday birth TIMESTAMP;
-- 添加一列
ALTER TABLE student1 ADD COLUMN class VARCHAR(10);
alter table 表名 character set 字符集名称; -- 修改
```

###### 删除：

```sql
drop table 表名;
-- 删除一列
ALTER TABLE student1 DROP COLUMN class;
-- 删除表(如果有就删除)
DROP TABLE IF EXISTS student1;
```

------



## 增删改：

###### 	增加：

​		insert into 表名 values(值1,值2,...值n);
​		insert into 表名(列名1,列名2,...列名n) values(值1,值2,...值n);

```sql
-- 往user2里面的username,password字段里面插入'test001', '123'
insert into user2(username,password) values ('test001', '123')
```



###### 	删除：

​		delete from 表名; -- 不推荐！会删除所有数据！
​		truncate table 表名; -- 如果删除全表数据，先删除表，然后再创建一张一样的表
​		delete from 表名 [where 条件]

```sql
-- 删除password为123的列
DELETE FROM user2 WHERE password=123;
```



###### 	修改：

​		update 表名 set 列名1=值1,列名2=值2,... [where 条件];

```sql
-- 把username为佩奇的money该为6666
update user2 set money=6666 where username='佩奇';
```



###### 	注意：

1. 对于删除和修改，如果不加任何条件，则对表中所有记录进行删改
2. 插入数据库时，列名和值要一一对应，可以不编写列名，那么值需要按顺序全部写上
3. 字符串类型需要使用引号【单双引号都可以】引起来，不是反引号！
4. 反引号用在表名、列名、数据库名上

------



## 查询：

###### 简单查询：

select * from 表名;

###### 完整语法：

​		select
​				字段列表
​		from
​				表名列表
​		where
​				条件列表
​		group by
​				分组字段
​		having
​				分组之后的条件
​		order by
​				排序
​		limit
​				分页限定

###### 查询最后一次插入的数据

```SQL
select last_insert_id()
```

```java
//java代码
sql = "select last_insert_id()";
try (PreparedStatement stmt2 = conn.prepareStatement(sql);
     ResultSet rs = stmt2.executeQuery()) {
    if (rs.next()) {
        //只会查到一个所以第一个就是
        rs.getInt(1);
    }
}
```





###### 条件查询：

```sql
-- 查询年龄为18和17的
SELECT * FROM student WHERE age IN (17, 18);
-- 查询年龄在18和17之间的
SELECT * FROM student WHERE age BETWEEN 17 AND 18;
-- 查询年龄为null的
SELECT * FROM student WHERE age<=>NULL;
SELECT * FROM student WHERE age IS NULL;
```

###### 模糊查询：

```
-- % 任意长度字符，_ 一个长度字符
SELECT * FROM student WHERE NAME LIKE '乔%';
```

###### 去除重复：DISTINCT

```sql
-- 去除重复的
SELECT DISTINCT NAME FROM student;
```

###### 基本运算：

```sql
-- 基本运算(只是显示，不会影响实际数据)
SELECT NAME, score+100 FROM student;
```

###### 排序：ORDER BY

```sql
-- 按分数排序
SELECT * FROM student ORDER BY score;
-- 降序排序
SELECT * FROM student ORDER BY score DESC;
-- 先按分数排序，分数相同按年龄排序
SELECT * FROM student ORDER BY score,age;
-- 先按分数降序排序，分数相同按年龄升序排序
SELECT * FROM student ORDER BY score DESC,age;
```

###### 只查询第一条

```sql
SELECT * FROM edu_course ec ORDER BY price DESC LIMIT 1
```

###### 聚合查询：

```sql
-- 查询所有数据条数
SELECT COUNT(*) 总人数 FROM student;
-- 使用where统计过滤后的条数
SELECT COUNT(*) 总人数 FROM student WHERE age>=18;
-- 查询总分数,平均分,最大值,最小值
SELECT SUM(score) 总分数, AVG(score) 平均分, MAX(score)  最大值, MIN(score) 最小值 FROM student;
```

###### 分组查询：GROUP BY

```sql
-- 按成绩分组，将分组显示出来(分组聚合一起使用)
SELECT sex 性别, COUNT(*) 人数 FROM student GROUP BY sex;
-- 先过滤，再分组查询
SELECT sex 性别, COUNT(*) 人数 FROM student WHERE score>85 GROUP BY sex;
-- 先分组查询，再过滤
SELECT sex 性别, COUNT(*) 人数 FROM student GROUP BY sex HAVING COUNT(*)>2;
-- 先过滤，再分组查询，再过滤
SELECT sex 性别, COUNT(*) 人数 FROM student WHERE score>=100  GROUP BY sex HAVING COUNT(*)>=1;

```

###### 分页查询：

```sql
SELECT * FROM student LIMIT 2 OFFSET 0;
-- 2：显示多少条，0从第几条开始
SELECT * FROM student LIMIT 0, 2;
-- 每页n条，第p页：limit (p-1)*n, n;
```

###### 联表查询：

```sql
-- 联表查询
SELECT * FROM student_day04, class_day04 c WHERE class_id=c.id;
-- 内连接
SELECT * FROM student_day04 INNER JOIN class_day04 c ON class_id=c.id;
-- 左外连接（outer可以省略）
SELECT * FROM student_day04 LEFT JOIN class_day04 c ON class_id=c.id;
-- 右外连接（outer可以省略）
SELECT * FROM student_day04 RIGHT JOIN class_day04 c ON class_id=c.id;
```

1. 笛卡尔积：
   	select * from user, `order`; -- 所有数据 m*n 行
      	等同于：select * from user inner join `order`; -- 内连接
2. 内连接：
   	select * from user u, `order` o where u.id=o.uid; -- 仅有符合条件的行数，用where
      	select * from user u inner join `order` o on u.id=o.uid; -- 用on表示链接条件
      	where - 可以用在笛卡尔积和内连接中
      	on - 必须和join联表查询一起使用
3. 左外连接：
   	select * from user u left outer join `order` o on u.id=o.uid; -- 左表为主
      	select * from `order` o left join user u on u.id=o.uid; -- outer可以省略
4. 右外连接：
    select * from user u right outer join `order` o on u.id=o.uid; -- 右表为主

###### 子查询

​	嵌套查询或者一个查询结果作为另一个查询的条件
​	子查询结果：分为单列单行、单列多行、多列多行

1. 多列多行：相当于Map，多个key，每个key都是一个数组
   	子查询相当于一个临时表，使用在FROM语句之后：
      	select * from (select * from c2) as c3; -- 必须有别名！
      	select * from c1, (select * from c2) as c3 where c1.id=c3.id;
2. 单列多行：相当于一个数组
   	可以使用【多行多列】的操作方式
      	额外：父查询可以使用IN，但是比较运算符无法使用：
      	select * from t1 where c1 IN (select c2 from t2);
3. 单列单行：只有一个值
   	可以使用【多行多列、单行多列】的操作方式
      	额外：父查询可以使用 <= >= <> = 等比较运算符：
      	select * from t1 where c1>=(select count(*) from t2);

------









## 索引：

```sql
-- 添加索引
ALTER TABLE class_day04 ADD INDEX (teacher);-- 没有名字
ALTER TABLE class_day04 ADD INDEX indx_teacher(teacher);-- 有名字

-- 查看表索引
SHOW INDEX FROM class_day04;

-- 删除外键
ALTER TABLE student_day04 DROP FOREIGN KEY student_day04_ibfk_1;
-- 建表时添加
PRIMARY KEY(列名),-- 主键
FOREIGN KEY(列名) REFERENCES 主表名(主键)-- 外键
```

1. 索引：index，创建、添加索引
   	create table x(c1 varchar(6), index c1_idx(c1)) -- 1.不推荐
      	alter table x add index x(c1), add index y(c2) -- 2.推荐
      	create [unique] index idx_xxx on x(c1,c2) -- 3.创建索引
      	drop [unique] index idx_xxx on x; -- 删除索引
      	查看所有的索引：show index from 表名;
      	说明：良好的索引能够极大地优化查询效率，不好的索引不仅浪费空间，有可能降低查询效率
2. 外键约束：foreign key，表中的外键列就是另一个表中的主键
   	作用：让表与表产生关系，从而保证数据的正确性
      	概念：主表，即主键所在表，可以约束其他表；从表，可以是多个表，使用主表主键的表
      	create table x(c1 int, constraint 外键名称 foreign key(c1) references 主表名(主表列名)
      	alter table x drop foreign key c1; -- 删除外键
      	alter table x add constraint 外键名称 foreign key(c1) references 主表名(主表列名) on update cascade on delete cascade; -- 添加外键
      	说明：on update cascade-表示级联更新；on delete cascade-表示级联删除
      	阿里巴巴开发手册：不得使用外键与级联，一切外键概念必须在应用层解决

------



## 函数：

```sql
-- 拼接字符串
SELECT CONCAT('a','b');
-- 和math方法一样
SELECT ABS(-1), CEIL(2.1), FLOOR(2.9), ROUND(2.4), ROUND(2.5);
-- 查看版本
SELECT VERSION();
```

1. 字符串函数：
   	length('abc123') -- 长度
    concat('abc', 100) -- 拼接
    lower('abCD')/upper('xyz') -- 大小写转换
    ltrim/rtrim/trim(' x ') -- 修剪左、右、前后空格
    substr('abc123',2,3) -- 第2个位置开始截取长度3的子串，下标1开始
    repeat/reverse/strcmp -- 重复、反向、比较等
    
     instr( string1, string2, start_position,nth_appearance )  
    
    字
    
2. 日期函数
    curdate()/current_date() -- 当前日期
      	curtime()/current_time() -- 当前时间
      	now() -- 当前日期+时间
      	current_timestamp() -- 当前时间戳：日期和时间
      	adddate('2021-11-11', interval 10 day) -- 计算日期：day天、year年、month月、hour时、minute分、second秒

3. 其他函数：
    if(条件, '成立结果', '不成立结果') -- 条件
      	ifnull(username, '空') -- 如果username为null显示为'空'，否则为username
      	conv(100, 10, 2) -- 将100从10进制转为2进制
      	version() -- 数据库版本
      	last_insert_id() -- 获取最后一次插入数据的id

------









