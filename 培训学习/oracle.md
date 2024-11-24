# 基本概念

1. 表空间：tablespace
2. 用户：user
3. 权限/角色：
   	connect 典型用户
      	resource 用于开发者
      	dba 数据库管理员，具有所有权限
4. 数据类型：
   	varchar/varchar2 字符串
      	integer 整数
      	number(n) 数字，长度为n
      	number(m,n) 小数，总长度m，小数长度n
      	date 日期格式
      	blob 大二进制对象
5. 其他类型：
   	float 可设置精度
      	char(n) 固定长度字符
      	clob 大文件对象，最大支持4G
6. varchar和varchar2区别：
   	varchar2把空串等同于null处理，而varchar仍按照空串处理
      	varchar2字符要用几个字节存储，要看数据库使用的字符集
      	一般使用UTF-8字符集，汉字默认占用3个长度













# 创建数据库和用户

#### 连接：

conn 用户名/密码，conn system/12345678 [as sysdba]
		命令行：sqlplus，输入用户名和密码

#### 创建表空间

```sql
create  tablespace  mydb 
datafile 'D:\Oracle\mydb.dbf' 
size 100m
autoextend on 
next 10m; 
```



​		create tablespace mydb -- 表空间名，相当于数据库
​		datafile 'D:/mydb.dbf' -- 对应的数据文件
​		size 100m -- 初始大小
​		autoextend on -- 开启自动增长
​		next 10m; -- 自动增长大小

#### 创建用户

```sql
create user root identified by root default tablespace mydb; 
```

​		create user root -- 用户名
​		identified by 12345678 -- 密码
​		default tablespace mydb; -- 表空间
​		重新关联用户到表空间：
​		alter user root default tablespace mydb;

#### 用户权限

​		grant dba to root; -- 授权

#### 查看用户

​		select username from user_users
​		select username,default_tablespace from user_users
​		select username from dba_users where account_status='OPEN'

#### 设置显示

​		set linesize 一行大小
​		set pagesize 一页大小
​		col 列名 format a宽度
​		比如：set linesize 1000; col username format a50;

#### 查看所有表

​		select table_name from user_tables;

#### 查看所有序列

​		select * from user_sequences;













# 建表

```sql
CREATE TABLE student (
	xh VARCHAR2 ( 5 ),
	name VARCHAR2 ( 10 ),
	age INTEGER,
	sex NUMBER ( 1 ),
	qq VARCHAR2 ( 10 ),
	birth DATE,
	classid INTEGER,
	constraint student primary key ( xh ) -- 设置主键
);
```



#### 创建序列

- **INCREMENT BY** 用于指定序列增量（默认值：1），如果指定的是正整数，则序列号自动递增，如果指定的是负数，则自动递减。
- **START WITH** 用于指定序列生成器生成的第一个序列号，当序列号顺序递增时默认值为序列号的最小值，当序列号顺序递减时默认值为序列号的最大值。
- **MAXVALUE** 用于指定序列生成器可以生成的组大序列号（必须大于或等于 START WITH，并且必须大于 MINVALUE），默认为 NOMAXVALUE。
- **MINVALUE** 用于指定序列生成器可以生成的最小序列号（必须小于或等于 START WITH，并且必须小于 MAXVALUE），默认值为 NOMINVALUE。
- **CYCLE** 用于指定在达到序列的最大值或最小值之后是否继续生成序列号，默认为 NOCYCLE。
- **CACHE** 用于指定在内存中可以预分配的序列号个数（默认值：20）。

```sql
CREATE sequence sid
INCREMENT BY 1
START WITH 1
MAXVALUE 999999999
NOCYCLE 
NOCACHE;
```



#### 使用序列

- 第一次 NEXTVAL 返回的是初始值；随后的 NEXTVAL 会自动增加 INCREMENT BY 对应的值，然后返回增加后的值。
- CURRVAL 总是返回当前 SEQUENCE 的值，但是在第一次 NEXTVAL 初始化之后才能使用 CURRVAL ，否则会出错。
- 一次 NEXTVAL 会增加一次 SEQUENCE 的值，所以如果在同一个语句里面使用多个NEXTVAL，其值就是不一样的。
- 如果指定 CACHE 值，Oracle 就可以预先在内存里面放置一些 SEQUENCE，这样存取的快些。 CACHE 里面的取完后，Oracle 自动再取一组到 CACHE。
- 但使用 CACHE 或许会跳号，比如数据库突然不正常关闭（shutdown abort)， CACHE 中的 SEQUENCE 就会丢失。所以可以在 CREATE SEQUENCE 的时候用 NOCACHE 防止这种情况。

```sql
select sid.currval from dual; -- 序列当前值（刚创建时不能查询）
select sid.nextval from dual; -- 序列的下一个值
insert into t1 values(sid.nextval, 'x', ...) -- 使用序列值插入
```



#### 删除序列

```sql
DROP SEQUENCE sid;
```









# 部分语法

#### 连表查询

【 ( + )】标记的表为从表；

也可以使用mysql的连表查询

```sql
SELECT
	t.name tname,
	c.name classname,
	s.NAME sname 
FROM
	teacher t,
	class c,
	student s 
WHERE
	t.id ( + ) = c.tid 
	AND c.id ( + ) = s.CLASSID;
```







#### 分页查询

实现思路：嵌套子查询，固定rownum为一个【真实】字段

```sql
-- 先使用子查询排序，生成s2表，然后再查询s2表的rownum作为条件
select rownum, s2.* from (select rownum 原序列, s1.* from student1 s1 order by age) s2 where rownum<=3;
-- 使用别名，rownum是针对当前查询的关键字，所以要使用内部的rownum必须添加别名
select * from (select rownum r, s1.* from student1 s1) where r>3 and r<=6;
```







#### 条件查询

case when "条件（可以多个）" then “满足条件执行” ELSE ”其他条件都不成立执行“ end

```sql
-- 当成绩小于60等级显示不及格，小于90显示及格，其他显示高手
SELECT
	"name",
	"score",
	( CASE WHEN "score" < 60 THEN '不及格' WHEN "score" < 90 THEN '及格' ELSE '高手们' END ) 等級 
FROM
	"student1";
```



例子：

1. 当做聚合函数条件
2. 行转列 

```sql
-- 1. 使用case语句实现：一个查询中统计有多少男同学，多少女同学，男同学中及格人数，女同学中及格人数
SELECT
	( CASE WHEN "sex" = 1 THEN '男' WHEN "sex" = 0 THEN '女' ELSE '未知' END ) 性别 ,
	COUNT( * ) 人数,
	COUNT( CASE WHEN "score" > 60 THEN 1 ELSE NULL END ) 及格人数 
FROM
	"student1" 
GROUP BY
	"sex";
	
-- 2. 假设有如下数据表，如何实现行专列，请写出SQL语句：
-- 	city number type
-- 	北京 101     水
-- 	北京 25      电
-- 	北京 37      煤
-- 	北京 44      水
-- 	北京 36      电
-- 	上海 78      水
-- 	上海 91      电
-- 	上海 191	     水
-- 	上海 44      煤
-- 最终查询结果结构类似：
-- 	城市 水 电 煤
SELECT
	"city" 城市,
	MAX( CASE WHEN "type" = '水' THEN "number" ELSE NULL END ) 水,
	MAX( CASE WHEN "type" = '电' THEN "number" ELSE NULL END ) 电,
	MAX( CASE WHEN "type" = '煤' THEN "number" ELSE NULL END ) 煤 
FROM
	"test" 
GROUP BY
	"city";
```







#### 索引

1. 创造普通索引

   create index 【索引的名字】on 【表名】(【字段名】);

   ```sql
   create index index_qq on STUDENT(qq);
   ```

2. 创建联合索引

   create index 【索引的名字】on 【表名】(【字段名】,【字段名】,【字段名】);

   ```sql
   CREATE INDEX index_unite ON STUDENT ( CLASSID, name, birth );
   ```

3. 查询所有索引

   user_indexes：oracle自带的表

   ```sql
   select  index_name 索引名,table_name 表名 from user_indexes;
   ```

4. 查询某张表中所有索引

   ```sql
   select  index_name 索引名,table_name 表名 from user_indexes where table_name = 'STUDENT';
   ```

   





​	语法和MySQL一致
​	创建：create index idx1 on t1(c1)
​	复合索引：create index idx2 on t1(c1,c2)
​	查询所有索引：select index_name,table_name from user_indexes
​	获取索引列名：select * from user_ind_columns where index_name='xxx';
​	每个索引就是一颗B+树
​	







#### 视图

​	提供一个查询的窗口，数据来源于原表，类似临时表
​	创建：create view v1 as select x from y;
​	查看：select view_name from user_views;
​	使用：select * from v1;
​	修改：update v1 set x=1 where ...; commit;
​	不推荐修改视图！会影响原表，推荐创建只读视图
​	只读：...with read only;
​	作用：

	1. 视图可以屏蔽敏感字段
 	2. 分部门查询数据（总部->分部）







#### 存储过程与存储函数

区别：
		存储过程：提前编译好的PL/SQL语言固定逻辑，在SQL中直接调用
		存储函数：相当于有返回值的存储过程，带return
		两者都可以有参数，参数分为in（输入）和out（输出）类别



###### 存储过程

存储过程的创建

```sql
-- 如果没有参数就直接省略括号
-- 两个传入参数
CREATE OR REPLACE PROCEDURE pro4(aid IN INTEGER, uage NUMBER, UNAME out VARCHAR2)
AS
	-- 定义变量
    counta number(1) := 0;
BEGIN
    -- 方法体
    SELECT COUNT(*) INTO counta FROM "student1" WHERE "id" = aid AND "age" >= uage;
    if counta > 0 then
        SELECT "name" INTO UNAME FROM "student1" WHERE "id" = aid AND "age" >= uage;
    else
        UNAME:=null;

    END if;
end;
```



存储过程的调用

```sql
-- 方式1
DECLARE
-- 定义变量
BEGIN
-- 调用方法
    job3pro1();
END;
```



###### 存储函数

存储函数的创建，调用和存储过程一样,但是存储函数有返回值的情况必须要定义变量接收

```sql
CREATE OR REPLACE FUNCTION f1( addAge STUDENT.AGE%TYPE) return NUMBER
AS
    counta number(1) := 0;
BEGIN
    -- 方法体
    SELECT COUNT(*) INTO counta FROM STUDENT WHERE AGE < 20;
    if counta > 0 then
        update STUDENT set AGE=AGE+addAge WHERE AGE < 20;
    END if;
		return sql%rowcount;
end;
```

存储函数调用

```sql
DECLARE
aname NUMBER;
BEGIN
		aname := f1(1);
		dbms_output.put_line(aname);
END;

```









#### 游标的使用

```sql
CREATE OR REPLACE FUNCTION job3pro4 return NUMBER
AS
		crow STUDENT%rowtype; -- 行类型
		cursor c1 is select * from STUDENT; -- 游标，有查询到的所有数据
BEGIN
    open c1;-- 打开游标
		-- 循环
		loop 
        fetch c1 into crow; -- 获取当前游标数据给定义的crow，并且游标会移到下一个
        exit when c1%notfound; -- 如果没有任何内容，就会跳出循环
        dbms_output.put_line(crow.NAME || '的年龄：' || crow.AGE);
    end loop;-- 循环结束
    close c1;
		return sql%rowcount;
end;
```







#### PL/SQL

​	对SQL语言的扩展，提供过程化编程扩展
​	声明方法：
​		declare ---定义变量
​		begin   ---逻辑
​		end;    ---结束
​	变量：
​		i number(2) := 10;
​		s varchar2(10) := 'abc123';
​		x t1.c1%type; ---直接引用t1表中c1列类型
​		xrow t1%rowtype; ---记录型记录，保存一行记录
​		in number(2) := &ii; ---输入，ii为传递的值
​		c number := SQL%ROWCOUNT; --- 获取增删改影响的条数
​	输出：
​		dbms_output.put_line(i);
​		select c1 into x from t1; ---赋值查询的值
​		dbms_output.put_line(xrow.t1 || '+' || xrow.t2); ---取值
​		raise_application_error(数字, '信息');
​	结构：
​		if 条件 then...elsif 条件 then...else...end if;
​			示例：if i<1 then ... else ... end if;
​		while 条件 loop...end loop;
​			示例：while i<10 loop ...; 
​				i:=i+1; end loop;
​		loop exit when 条件 ... end loop;
​			示例：loop
​				exit when i=10; 
​				i:=i+1; end loop;
​		for i in 1..10 loop ... end loop;
​			说明：1..10 相当于 [1,2,3,4,5,6,7,8,9,10]
​	游标：
​		存放多个对象，多行对象，相当于Java中的ResultSet
​		定义：cursor c1 is select * from t1;
​		应用：open打开，fetch获取，close关闭
​		示例：
​			open c1; loop -- 相当于while(ResultSet.next())
​			fetch c1 into xrow; -- 相当于ResultSet获取一条数据，如果没有数据c1==notfound
​			exit when c1%notfound; -- 相当于ResultSet.next()==false
​			dbms_output.put_line(xrow.c1 || xrow.c2 ...);
​			end loop; close c1;
​		带参：
​			cursor c2(param t1.c1%type) is ...
​			open c2(10);
​			loop ...
​			close c2;







#### 触发器

触发器就是一个规则，在增删改的时候触发运行
	分类：语句级触发器；行级别触发器（包含for each row）
	:old - 删之前改行数据值（用于行级别触发器）
	:new - 增改之后该行数据值（用于行级别触发器）
	after - 在增删改【之后】触发
	before - 在增删改【之前】触发



语法：

```sql
-- 自增id的触发器
CREATE or REPLACE TRIGGER 触发器名 before INSERT ON 表名 FOR each ROW
DECLARE
BEGIN
	-- 要执行的
END;
```





###### id自增的实现

```sql
-- 自增id的触发器
CREATE or REPLACE TRIGGER job4trig1 before INSERT ON STUDENT FOR each ROW
DECLARE
BEGIN
	-- 先判断是否为空或者小于0
	IF :new.XH IS  NULL OR :new.XH < 0 then
	-- 查询序列下一个值，并保存到新插入的行中
		SELECT sxh.nextval INTO :new.xh from dual;
	END IF;
END;
```











#### 日期插入

orcale插入日期需要使用to_date('2020-11-11','yyyy-MM-dd')函数

oracle不支持直接插入多条数据









#### 常用方法

​	注1：大部分和MySQL类似，但是所有函数不能单独使用，必须加from dual
​	注2：Oracle中拼接字符串可以使用concat或者||
​	字符函数：
​		length(字符串) 长度
​		lower/upper(字符串) 大小写转换
​		concat(字符串1,字符串2) 拼接
​		ltrim/rtrim/trim(字符串) 截取空格
​		replace(字符串,旧子串,新子串) 替换旧字符串为新的字符串
​		instr(字符串,子串,开始下标) 从指定下标开始查询子串（indexOf）
​		substr(字符串,开始,长度) 截取字符串，从开始到指定长度
​	数字函数：
​		和Math包中的函数类似，与MySQL函数等同
​		round(数字[,位数]) 在数字的指定位置四舍五入
​		trunc(数字[,位数]) 在数字指定位置直接截取，类似floor
​	转换函数：
​		sysdate 字段，表示系统事件
​		systimestamp 当前日期+时间
​		add_months(时间,月数) 指定时间加上指定月数
​		last_day(日期) 当月最后一天
​		extract(片段 from 日期) 提取片段，片段=YEAR|MONTH|DAY|HOUR|MINUTE|SECOND
​	日期函数：
​		to_char(日期或者数字, 格式) 转换为指定格式的字符串
​		to_date(字符串,日期格式) 将字符串转为日期格式
​		to_number(字符串,数字格式) 字符串转数字
​	其他：
​		decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值) 相当于switch、多重if语句
​		nvl(字段,默认值) 如果字符为空返回默认值，否则返回字段的值，相当于MySQL中的ifnull
​		nvl2(字段,非空返回值,空返回值) 根据字段是否为空返回对应值，相当于MySQL中的if
​		avg/sum/min/max/count 聚合函数，和MySQL一致
​		nvl相当于MySQL中的ifnull，nvl2相当于MySQL中的if


JDBC
	事务特性：ACID
		原子性（Atomicity）：事务是不可分割的最小操作单位，要么同时成功，要么同时失败
		一致性（Consistency）：事务操作前后，数据保持完整性约束，数据状态合法一致
		隔离性（Isolation）：多个事务之间相互独立，一个事务内部不会影响另一个事务内部
		持久性（Durability）：当事务一旦提交后，数据的变化应该是永久性的
	事务级别：
		Read Uncommitted - 读未提交，产生的问题：脏读、不可重复读、幻读
		Read Committed - 读已提交（Oracle默认），产生的问题：不可重复读、幻读
		Repeatable Read - 可重复读 （MySQL默认），产生的问题：幻读
		Serializable - 串行化可以解决所有的问题，无法支持高并发
	Oracle默认：Read Committed，与MySQL不同
	代码操作与MySQL一致

Mybatis
	pom.xml依赖坐标：com.oracle.database.jdbc:ojdbc8:21.3.0.0
	mybatis.xml配置：
		driver="oracle.jdbc.OracleDriver"
		url="jdbc:oracle:thin:@127.0.0.1:1521:orcl" 标准版
		url="jdbc:oracle:thin:@localhost:1521:XE" 快速版
		username="xxx"; password="xxx"
	其他配置和mysql一致
	调用存储过程、存储函数：
		使用Map作为参数，返回值也会自动填充到Map参数中
		<select id="testP1" parameterType="java.util.Map" statementType="CALLABLE" useCache="false">
			{ call P1(
				#{a, mode=IN, jdbcType=INTEGER},
				#{b, mode=OUT, jdbcType=FLOAT},
				#{c, mode=OUT, jdbcType=VARCHAR} ) }
		</select>
​		存储函数类似，但是有返回值
		<select id="testF1" parameterType="map" resultType="String" statementType="CALLABLE" useCache="false">
			{ #{b, mode=OUT, jdbcType=VARCHAR} = call F1(
				#{a, mode=IN, jdbcType=INTEGER}
			) }
		</select>
​		说明：存储函数的返回值相当于OUT参数！同样定义在Map中即可。
​	获取自增主键：
​		数据库中：create sequence USER_ID INCREMENT by 1 START WITH 1 NOMAXVALUE CYCLE;
​		<insert id="addUser" parameterType="com.zking.User">
​			<selectKey resultType="int" keyProperty="id" keyColumn="id" order="BEFORE">
​				select USER_ID.nextval from dual
​			</selectKey>
​			insert into t values (#{id}, #{name}, ...)
​		</insert>
​	注意和MySQL的区别：
​		获取的主键保存在#{id}中，另外MySQL中order使用AFTER，MySQL中resultType可以不用配置
​		Oracle中无法直接使用useGeneratedKey，但是MySQL可以
​		其他除了配置、SQL语句细微差别，其他一致
​	Mybatis-plus和Mybatis操作一致









# JDBC

​	依赖：
​		<dependency>
​            <groupId>com.oracle.database.jdbc</groupId>
​            <artifactId>ojdbc8</artifactId>
​            <version>21.5.0.0</version>
​        </dependency>
​	

	使用方式和MySQL相似：
		driverClassName: oracle.jdbc.OracleDriver
		url: jdbc:oracle:thin:@localhost:1521:xe
		username: xxx
		password: xxx
	
	调用存储过程：
		CallableStatement stmt = conn.prepareCall("{call pro1(?,?)}");
		stmt.setObject(1, xxx);
		stmt.registerOutParameter(2, OracleTypes.NUMBER);
		stmt.execute();
		System.out.println(stmt.getObject(2)); // 获取返回值
		说明：输入参数和普通参数一样，输出参数必须使用registerOutParameter方法
	
	调用存储函数：
		conn.prepareCall("{?= call func1(?,?,?,?)}");
		注意：第1个是返回值，第2个才是参数
#### 问题

###### 无法使用图形化工具连接Oracle：

	1. 确保驱动是正确的
	2. 确保使用XE、正确的用户名密码、主机名和端口号
	3. 确保Oracle开头相关的服务是开启的！

###### 中文乱码问题解决

1. 查看服务器端编码：select userenv('language') from dual; 结果为：AMERICAN_AMERICA.ZHS16GBK

 2. 执行语句：select * from V$NLS_PARAMETERS，查看第一行中PARAMETER项中为NLS_LANGUAGE对应的VALUE项中是否和第一步得到的值一样

    - 如果不是，需要设置环境变量！

    - 否则PLSQL客户端使用的编码和服务器端编码不一致，插入中文时出现乱码

3. 设置环境变量：计算机->属性->高级系统设置->环境变量->新建，设置变量名：NLS_LANG，变量值：第1步查到的值（AMERICAN_AMERICA.ZHS16GBK）

4. 重新启动PLSQL，测试插入数据











# springmvc整合

#### 插入时获取id

数据库中：create sequence USER_ID INCREMENT BY 1 START WITH 1 NOMAXVALUE CYCLE;

注意和MySQL的区别：
	MySQL:<selectKey order="AFTER">select last_insert_id()</selectKey>
	Oracle:<selectKey order="BEFORE">select UID2.nextval from DUAL</selectKey>
	获取的主键保存在#{id}中，另外MySQL中order使用AFTER，MySQL中resultType可以不用配置
	Oracle中无法直接使用useGeneratedKey，但是MySQL可以
	其他除了配置、SQL语句细微差别，其他一致

mapper.xml配置

```xml
<insert id="addUser" parameterType="com.zking.User">
    <selectKey resultType="int" keyProperty="id" keyColumn="id" order="BEFORE">
        select USER_ID.nextval from dual
    </selectKey>
    insert into t values (#{id}, #{name}, ...)
</insert>
```








#### mybatis调用存储过程/函数



###### mapper接口方法定义

```java
@Mapper
public interface IStudentMapper extends BaseMapper<Student> {

    void job3pro2(Map<String ,Object> map);
    void job3pro3(Map<String ,Object> map);
}
```



###### mapper.xml

mode：是什么参数；

​	普通参数和in参数都为【IN】

​	out参数和存储函数返回值都为【OUT】

jdbcType：参数类型

```xml    &lt;!--存储过程--&gt;
<!--存储过程-->
<select id="job3pro2" statementType="CALLABLE" parameterType="java.util.Map">
    {call job3pro2(
    #{man, mode = OUT, jdbcType=INTEGER},
    #{girl, mode = OUT, jdbcType=INTEGER}
    )}
</select>

<!--存储函数-->
<select id="job3pro3" statementType="CALLABLE" parameterType="java.util.Map">
    {#{count1, mode = OUT,jdbcType=INTEGER} = call job3pro3(
    #{sum, mode = IN, jdbcType=INTEGER}
    )}
</select>
```











#### 实体类区别

只有主键添加了注解实现自增

// 添加序列注解配置
@KeySequence(value = "序列名", dbType = DbType.ORACLE)

//标记对应表的主键
@TableId(type = IdType.INPUT)

```java
package com.xh.entity;

import com.baomidou.mybatisplus.annotation.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
//自动生成全参构造函数
@AllArgsConstructor
//自动生成无参构造函数
@NoArgsConstructor
//标记是这个表
@TableName("user2")
// 1. 添加序列注解配置
@KeySequence(value = "sid", dbType = DbType.ORACLE)
public class User {
    //标记对应表的主键
    @TableId(type = IdType.INPUT)
    private Integer id;
    //标记对应表的字段名
    @TableField("username")
    private String username;
    @TableField("password")
    private String password;
    @TableField("money")
    private Integer money;
}

```







#### spring.xml

添加了使用序列的属性

```xml
    <bean id="sqlSessionFactory"
          class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
        <!--配置mybatis需要的数据库连接-->
        <property name="dataSource" ref="dataSource"/>
        <!--分页插件-->
        <property name="plugins">
            <bean class="com.github.pagehelper.PageInterceptor"/>
        </property>
        <!--配置mybatisplus-->
        <property name="configuration">
            <bean class="com.baomidou.mybatisplus.core.MybatisConfiguration">
                <property name="mapUnderscoreToCamelCase" value="true"/>
            </bean>
        </property>
        <!--oracle主键使用序列-->
        <property name="globalConfig">
            <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig">
                <property name="dbConfig">
                    <bean class="com.baomidou.mybatisplus.core.config.GlobalConfig.DbConfig">
                        <property name="keyGenerators">
                            <bean class="com.baomidou.mybatisplus.extension.incrementer.OracleKeyGenerator"/>
                        </property>
                    </bean>
                </property>
            </bean>
        </property>

    </bean>

```









#### 分页插件：

​		只需要修改mybatis.xml配置中的SQL方言：
​		<property name="helperDialect" value="oracle"/>













# 相关操作SQL参考

- -- CASCADE 的意思是将用户的数据库数据一并删除，并没有删除相应的表空间
  drop user xxx CASCADE;
- -- 查询所有表空间物理位置
  select t1.name,t2.name from v$tablespace t1, v$datafile t2 where t1.ts# = t2.ts#;
  select FILE_NAME from dba_data_files;
- -- 删除空的表空间，不包含物理文件
  drop tablespace tablespace_name;
- -- 删除【非空】表空间，不包含物理文件
  drop tablespace tablespace_name including contents;
- -- 删除【非空】表空间，包含物理文件
  drop tablespace tablespace_name including contents and datafiles;
- -- 如果其他表空间中的表有外键等约束关联到了本表空间中的表的字段，就要加上CASCADE CONSTRAINTS
  drop tablespace tablespace_name including contents and datafiles CASCADE CONSTRAINTS;
- -- 查看表空间的总大小
  select tablespace_name,sum(bytes)/1024/1024 from dba_data_files group by tablespace_name;
- -- 查看表空间已经使用大小、分配情况
  select SEGMENT_TYPE,owner,sum(bytes)/1024/1024 from dba_segments where tablespace_name='?' group by segment_type,owner;
- -- 增加表空间大小
  ALTER DATABASE DATAFILE 'D:\xxx\mydb.dbf' RESIZE 1024M; 
  ALTER DATABASE DATAFILE 'D:\xxx\mydb.dbf' AUTOEXTEND ON NEXT 200M MAXSIZE UNLIMITED;
- 错误：Oracle is not available => 不要安装数据库到包含中文文件夹的目录下
- 修改端口：
  	使用 system 登录
    	调用：call dbms_xdb.cfg_update(updateXML(dbms_xdb.cfg_get(),'/xdbconfig/sysconfig/protocolconfig/httpconfig/http-port/text()',8081));









