# 一、函数

**函数**是指一段可以直接被另一段程序调用的程序或代码。也就意味着，这一段程序或代码在MySQL中已经给我们提供了，我们要做的就是在合适的业务场景调用对应的函数完成对应的业务需求即可。那么，函数到底在哪儿使用呢？

我们先来看两个场景：

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928134809398.png" alt="image-20220928134809398"  />

1.  在企业的OA或其他的人力系统中，经常会提供的有这样一个功能，每一个员工登录上来之后都能够看到当前员工入职的天数。而在数据库中，存储的都是入职日期，如 2000-11-12，那如果快速计算出天数呢？
2. 在做报表这类的业务需求中，我们要展示出学员的分数等级分布。而在数据库中，存储的是学生的分数值，如98、75，如何快速判定分数的等级呢？

其实，上述的这一类的需求，我们通过MySQL中的函数都可以很方便的实现。



MySQL中的函数主要分为以下四类： 字符串函数、数值函数、日期函数、流程函数。



## 1.1、字符串函数

MySQL中内置了很多字符串函数，常用的几个如下：

| 函数                       | 功能                                                         |
| -------------------------- | ------------------------------------------------------------ |
| concat(S1, S2, ...Sn)      | 字符串拼接，将 S1，S2，... Sn 拼接成一个字符串               |
| lower(str)                 | 将字符串 str 全部转为小写                                    |
| upper(str)                 | 将字符串 str 全部转为大写                                    |
| lpad(str, n, pad)          | 左填充，用字符串 pad 对 str 的左边进行填充，直到为 n 个字符串长度 |
| rpad(str, n, pad)          | 右填充，用字符串 pad 对 str 的右边进行填充，直到为 n 个字符串长度 |
| trim(str)                  | 去掉字符串头部和尾部的空格                                   |
| substring(str, start, len) | 返回从字符串 str 从 start 位置起的 len 个长度的字符串        |



**案例**：

1. concat：字符串拼接

   ```sql
   select concat('Hello', 'MySQL');
   ```

2.  lower：全部转小写

   ```sql
   select lower('Hello');
   ```

3. upper：全部转大写

   ```sql
   select upper('Hello');
   ```

4. lpad：左填充

   ```sql
   select lpad('01', 5, '-');
   ```

5. rpad：右填充

   ```sql
   select lpad('01', 5, '-');
   ```

6. trim：去除空格

   ```sql
   select trim(' Hello MySQL ');
   ```

7. substring：截取子字符串

   ```sql
   select substring('Hello World', 1, 5); -- 结果为Hello，MySQL中第一位字符串索引为1
   ```



**案例**：

由于业务需求变更，企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如：1号员工的工号应该为00001。

```sql
update emp set workno = lpad(workno, 5, '0');
```



## 1.2、数值函数

常见的数值函数如下：

| 函数        | 功能                                   |
| ----------- | -------------------------------------- |
| ceil(x)     | 向上取整                               |
| floor(x)    | 向下取整                               |
| mod(x, y)   | 返回 x/y 的模                          |
| rand()      | 返回 0~1 内的随机数                    |
| round(x, y) | 求参数 x 的四舍五入的值，保留 y 位小数 |



**案例**：

1. ceil：向上取整

   ```sql
   select ceil(1.1);
   ```

2. floor：向下取整

   ```sql
   select floor(1.9);
   ```

3. mod：取模

   ```sql
   select mod(7, 4);
   ```

4. rand：获取随机数

   ```sql
   select rand();
   ```

5. round：四舍五入

   ```sql
   select round(2.344, 2);
   ```



**案例**：

通过数据库的函数，生成一个六位数的随机验证码。

思路：获取随机数可以通过rand()函数，但是获取出来的随机数是在0-1之间的，所以可以在其基础上乘以1000000，然后舍弃小数部分，如果长度不足6位，补0

```sql
select lpad(round(rand() * 1000000, 0), 6, '0');
```



## 1.3、日期函数

常见的日期函数如下：

| 函数                               | 功能                                              |
| ---------------------------------- | ------------------------------------------------- |
| curdate()                          | 返回当前日期                                      |
| curtime()                          | 返回当前时间                                      |
| now()                              | 返回当前日期和时间                                |
| year(date)                         | 获取指定date的年份                                |
| month(date)                        | 获取指定date的月份                                |
| day(date)                          | 获取指定date的日期                                |
| DATE_ADD(date, interval expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1, date2)             | 返回起始时间date1 和 结束时间date2之间的天数      |



**案例**：

1.  curdate：当前日期

   ```sql
   select curdate();
   ```

2. curtime：当前时间

   ```sql
   select curtime();
   ```

3.  now：当前日期和时间

   ```sql
   select now();
   ```

4. YEAR，MONTH，DAY：当前年、月、日

   ```sql
   select year(now());
   select month(now());
   select day(now());
   ```

5.  date_add：增加指定的时间间隔

   ```sql
   select date_add(now(), interval 70 YEAR);
   ```

6.  datediff：获取两个日期相差的天数

   ```sql
   select datediff('2021-10-01', '2021-12-01');  -- -61
   ```



**案例**：

查询所有员工的入职天数，并根据入职天数倒序排序。

思路：入职天数，就是通过当前日期 - 入职日期，所以需要使用datediff函数来完成。

```sql
select name, datediff(now(), entrydate) entrydays from emp order by entrydays desc;
```



## 3.4、流程函数

流程函数也是很常用的一类函数，可以在SQL语句中实现条件筛选，从而提高语句的效率。

| 函数                                                         | 功能                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| if(value, t, f)                                              | 如果 value 为 true ，则返回 t ，否则返回 f                   |
| ifnull(value1, value2)                                       | 如果 value1 不为空，返回 value1 ，否则返回 value2            |
| case when [val1] then [res1] ... else [default] end          | 如果 val1 为 true ，返回 res1 ，... 否则返回 default 默认值  |
| case [expr] when [ val1 ] then [res1] ...  else [default] end | 如果expr的值等于val1，返回res1 ，... 否则返回 default 默认值 |



**案例**：

1. if

   ```sql
   select if(false, 'OK', 'Error');
   ```

2. ifnull

   ```sql
   select ifnull('Ok', 'Default');
   select ifnull('', 'Default');
   select ifnull(null, 'Default');
   ```

3. case when then else end

   需求：查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市，其他 ----> 二线城市)

   ```sql
   select
   	name,
   	(case workaddress when '北京' then '一线城市' when'上海' then '一线城市' else '二线城市' end) '工作地址'
   from emp;
   ```



**案例**：

统计班级各个学员的成绩，展示的规则如下：

- \>=85，展示优秀
- \>=60，展示及格
- 否则，展示不及格

```sql
create table score(
    id int comment 'ID',
    name varchar(20) comment '姓名',
    math int comment '数学',
	english int comment '英语',
	chinese int comment '语文'
) comment '学员成绩表';

insert into score (id, name, math, english, chinese) values
	(1, 'Tom', 67, 88, 95),
	(2, 'Rose', 23, 66, 90),
	(3, 'Jack', 56, 98, 76);
```



```sql
select 
	id, 
	name, 
	(case when math >= 85 then '优秀' when math >= 60 then '及格' else '不及格' end) '数学',
	(case when english >= 85 then '优秀' when english >= 60 then '及格' else '不及格' end) '英语',
	(case when chinese >= 85 then '优秀' when chinese >= 60 then '及格' else '不及格' end) '语文'
from score;
```



## 3.5、总结

1. 字符串函数

   ```sql
   concat、lower、upper、lpad、rpad、trim、substring
   ```

2. 数值函数

   ```sql
   ceil、floor、mod、rand、round
   ```

3. 日期函数

   ```sql
   curdate、curtime、now、year、month、day、date_add、datediff
   ```

4. 流程函数

   ```sql
   if、ifnull、case [...] when ... then ... else ... end
   ```



MySQL的常见函数我们学习完了，那接下来，我们就来分析一下，在前面讲到的两个函数的案例场景，思考一下需要用到什么样的函数来实现？

1. 数据库中，存储的是入职日期，如 2000-01-01，如何快速计算出入职天数呢？

   答案：datediff

2. 数据库中，存储的是学生的分数值，如98、75，如何快速判定分数的等级呢？

   答案：case ... when ...



# 二、约束



## 2.1、概述

概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

目的：保证数据库中数据的正确、有效性和完整性。

分类：

| 约束                      | 描述                                                     | 关键字          |
| ------------------------- | -------------------------------------------------------- | --------------- |
| 非空约束                  | 限制该字段的数据不能为null                               | not null        |
| 唯一约束                  | 保证该字段的所有数据都是唯一、不重复的                   | unique          |
| 主键约束                  | 主键是一行数据的唯一标识，要求非空且唯一                 | primary key     |
| 默认约束                  | 保存数据时，如果未指定该字段的值，则采用默认值           | default         |
| 检查约束 (8.0.16版本之后) | 保证字段值满足某一个条件                                 | check           |
| 外键约束                  | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | **foreign key** |

**注意**：约束是作用于表中字段上的，可以在创建/修改表的时候添加约束。



## 2.2、约束演示

上面我们介绍了数据库中常见的约束，以及约束涉及到的关键字，那这些约束我们到底如何在创建表、修改表的时候来指定呢，接下来我们就通过一个案例，来演示一下。



**案例**：

根据如下需求，完成表结构的创建：

| 字段名 | 字段含义   | 字段类型    | 约束条件                  | 约束关键字                  |
| ------ | ---------- | ----------- | ------------------------- | --------------------------- |
| id     | ID唯一标识 | int         | 主键，并且自动增长        | primary key，auto_increment |
| name   | 姓名       | varchar(10) | 不为空，并且唯一          | not null，unique            |
| age    | 年龄       | int         | 大于0，并且小于等于120    | check                       |
| status | 状态       | char(1)     | 如果没有指定该值，默认为1 | default                     |
| gender | 性别       | char(1)     | 无                        |                             |



对应的建表语句为：

```sql
create table tb_user(
    id int auto_increment primary key comment 'ID唯一标识',
    name varchar(10) not null unique comment '姓名',
    age int comment '年龄',
    status char(1) default 1 comment '状态',
    gender char(1) comment '性别'
);
```

在为字段添加约束时，我们只需要在字段之后加上约束的关键字即可，需要关注其语法。



我们执行上面的SQL把表结构创建完成，然后接下来，就可以通过一组数据进行测试，从而验证一下，约束是否可以生效。

```sql
insert into tb_user(name,age,status,gender) values ('Tom1',19,'1','男'), ('Tom2',25,'0','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values (null,19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom4',80,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',-1,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',121,'1','男');
insert into tb_user(name,age,gender) values ('Tom5',120,'男');
```



## 2.3、外键约束



### 2.3.1、介绍

外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

我们来看一个例子：

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928171312144.png" alt="image-20220928171312144"  />



左侧的emp表是员工表，里面存储员工的基本信息，包含员工的ID、姓名、年龄、职位、薪资、入职日期、上级主管ID、部门ID，这个部门的ID是关联的部门表dept的主键id，那emp表的dept_id就是外键，关联的是另一张表的主键。



**注意**：目前上述两张表，只是在逻辑上存在这样一层关系；在数据库层面，并未建立外键关联，所以是无法保证数据的一致性和完整性的。

没有数据库外键关联的情况下，能够保证一致性和完整性呢？我们来测试一下。



准备数据：

```sql
create table dept(
	id int auto_increment comment 'ID' primary key,
	name varchar(50) not null comment '部门名称'
) comment '部门表';

INSERT INTO dept (id, name) VALUES (1, '研发部'), (2, '市场部'),(3, '财务部'), (4, '销售部'), (5, '总经办');


create table emp(
    id int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '姓名',
    age int comment '年龄',
    job varchar(20) comment '职位',
    salary int comment '薪资',
    entrydate date comment '入职时间',
    managerid int comment '直属领导ID',
    dept_id int comment '部门ID'
) comment '员工表';

INSERT INTO emp (id, name, age, job,salary, entrydate, managerid, dept_id) VALUES
(1, '金庸', 66, '总裁',20000, '2000-01-01', null,5),(2, '张无忌', 20, '项目经理',12500, '2005-12-05', 1,1),
(3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),(4, '韦一笑', 48, '开发',11000, '2002-02-05', 2,1),
(5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),(6, '小昭', 19, '程序员鼓励师',6600, '2004-10-12', 2,1);
```



接下来，我们可以做一个测试，删除id为1的部门信息。

删除成功之后，部门表不存在id为1的部门，而在emp表中还有很多的员工，关联的为id为1的部门，此时就出现了数据的不完整性。 

而要想解决这个问题就得通过数据库的外键约束。



### 2.3.2、语法

#### 1、添加外键

```sql
create table 表名(
    字段名 数据类型...,
    ...
    [constraint] [外键名称] foreign key (外键字段名字) references 主表(主表列名)
);

alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表(主表列名);
```



**案例**：

为emp表的dept_id字段添加外键约束,关联dept表的主键id。

```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept (id);
```

添加了外键约束之后，我们再到dept表(父表)删除id为1的记录，然后看一下会发生什么现象。 

此时将会报错，不能删除或更新父表记录，因为存在外键约束。

![image-20220928210659189](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928210659189.png)



#### 2、删除外键

```sql
alter table 表名 drop foreign key 外键名称;
```



**案例**：

删除emp表的外键fk_emp_dept_id

```sql
alter table emp drop foreign key fk_emp_dept_id;
```



### 2.3.3、删除/更新行为

添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。具体的删除/更新行为有以下几种：

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| no action   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| restrict    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| cascade     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录。 |
| set null    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null）。 |
| set default | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |



语法：

```sql
alter table 表名 add constraint 外键名称 foreign key (外键字段) references 主表名(主表字段名) on update cascade on delete cascade;
```



**案例**：

由于NO ACTION 是默认行为，我们前面语法演示的时候，已经测试过了，就不再演示了，这里我们再演示其他的两种行为：CASCADE、SET NULL。

1. cascade

   ```sql
   alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade;
   ```

   修改父表id为1的记录，将id修改为6

   <img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928211722832.png" alt="image-20220928211722832" style="zoom:80%;" />

   我们发现，原来在子表中dept_id值为1的记录，现在也变为6了，这就是cascade级联的效果。

   **注意**：在一般的业务系统中，不会修改一张表的主键值。

   删除父表id为6的记录，我们发现，父表的数据删除成功了，但是子表中关联的记录也被级联删除了。

2. set null

   在进行测试之前，我们先需要删除上面建立的外键 fk_emp_dept_id。然后再通过数据脚本，将emp、dept表的数据恢复了。

   ```sql
   alter table emp add constraint fk_emp_dept_id foreign key (dept) references dept(id) on update set null on delete set null;
   ```

   接下来，我们删除id为1的数据，看看会发生什么样的现象。

   我们发现父表的记录是可以正常的删除的，父表的数据删除之后，再打开子表 emp，我们发现子表emp的dept_id字段，原来dept_id为1的数据，现在都被置为NULL了。

   <img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928212045450.png" alt="image-20220928212045450" style="zoom:80%;" />

   这就是SET NULL这种删除/更新行为的效果。



## 2.4、总结

1. 非空约束：not null
2. 唯一约束：unique
3. 主键约束：primary key (自增：auto_increment)
4. 默认约束：default
5. 检查约束：check
6. 外键约束：foreign key



# 三、多表查询



## 3.1、多表关系

项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：

- 一对多 (多对一)
- 多对多
- 一对一



### 3.1.1、一对多

- 案例：部门 与 员工的关系
- 关系：一个部门对应多个员工，一个员工对应一个部门
- 实现：在多的一方建立外键，指向一的一方的主见

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928212522914.png" alt="image-20220928212522914" style="zoom:80%;" />



### 3.1.2、多对多

- 案例：学生 与 课程的关系
- 一个学生可以选修多门课程，一门课程也可以供多个学生选择
- 实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220928212637497.png" alt="image-20220928212637497" style="zoom:80%;" />



对应的SQL脚本：

```sql
create table student(
    id int auto_increment primary key comment '主键ID',
    name varchar(10) comment '姓名',
    no varchar(10) comment '学号'
) comment '学生表';

insert into student values (null, '黛绮丝', '2000100101'), (null, '谢逊', '2000100102'), (null, '殷天正', '2000100103'),(null, '韦一笑', '2000100104');


create table course(
    id int auto_increment primary key comment '主键ID',
    name varchar(10) comment '课程名称'
) comment '课程表';

insert into course values (null, 'Java'), (null, 'PHP'), (null , 'MySQL') ,
(null, 'Hadoop')


-- 创建中间表
create table course(
    id int auto_increment comment '主键' primary key,
    studentid int not null comment '学生id',
    courseid int not null comment '课程id',
    constraint fk_courseid foreign key courseid references course(id),
    constraint fk_studentid foreign key studentid references student(id)
)comment '学生课程中间表';

insert into student_course values (null,1,1),(null,1,2),(null,1,3),(null,2,2),(null,2,3),(null,3,4);
```



### 3.1.3、一对一

- 案例：用户 与 用户详情的关系
- 关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
- 实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)

对应的SQL脚本：

```sql
create table tb_user(
    id int auto_increment primary key comment '主键ID',
    name varchar(10) comment '姓名',
    age int comment '年龄',
    gender char(1) comment '1: 男 , 2: 女',
    phone char(11) comment '手机号'
) comment '用户基本信息表';

create table tb_user_edu(
    id int auto_increment primary key comment '主键ID',
    degree varchar(20) comment '学历',
    major varchar(50) comment '专业',
    primaryschool varchar(50) comment '小学',
    middleschool varchar(50) comment '中学',
    university varchar(50) comment '大学',
    userid int unique comment '用户ID',
    constraint fk_userid foreign key (userid) references tb_user(id)
) comment '用户教育信息表';

insert into tb_user(id, name, age, gender, phone) values
    (null,'黄渤',45,'1','18800001111'),
    (null,'冰冰',35,'2','18800002222'),
    (null,'码云',55,'1','18800008888'),
    (null,'李彦宏',50,'1','18800009999');
    
insert into tb_user_edu(id, degree, major, primaryschool, middleschool, university, userid) values
    (null,'本科','舞蹈','静安区第一小学','静安区第一中学','北京舞蹈学院',1),
    (null,'硕士','表演','朝阳区第一小学','朝阳区第一中学','北京电影学院',2),
    (null,'本科','英语','杭州市第一小学','杭州市第一中学','杭州师范大学',3),
    (null,'本科','应用数学','阳泉第一小学','阳泉区第一中学','清华大学',4);
```



## 3.2、多表查询概述



### 3.2.1、数据准备

1. 删除之前 emp，dept表的测试数据

2. 执行如下脚本，创建emp表与dept表并插入测试数据

   ```sql
   -- 创建dept表，并插入数据
   create table dept(
   ```

	id int auto_increment comment 'ID' primary key,
   	name varchar(50) not null comment '部门名称'
   ) comment '部门表';

   INSERT INTO dept (id, name) VALUES (1, '研发部'), (2, '市场部'),(3, '财务部'), (4, '销售部'), (5, '总经办'), (6, '人事部');


   -- 创建emp表，并插入数据
   create table emp(
       id int auto_increment comment 'ID' primary key,
       name varchar(50) not null comment '姓名',
       age int comment '年龄',
       job varchar(20) comment '职位',
       salary int comment '薪资',
       entrydate date comment '入职时间',
       managerid int comment '直属领导ID',
       dept_id int comment '部门ID'
   ) comment '员工表';

   -- 添加外键
   alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);

   INSERT INTO emp (id, name, age, job,salary, entrydate, managerid, dept_id) VALUES
       (1, '金庸', 66, '总裁',20000, '2000-01-01', null,5),
       (2, '张无忌', 20, '项目经理',12500, '2005-12-05', 1,1),
       (3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),
       (4, '韦一笑', 48, '开发',11000, '2002-02-05', 2,1),
       (5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),
       (6, '小昭', 19, '程序员鼓励师',6600, '2004-10-12', 2,1),
       (7, '灭绝', 60, '财务总监',8500, '2002-09-12', 1,3),
       (8, '周芷若', 19, '会计',48000, '2006-06-02', 7,3),
       (9, '丁敏君', 23, '出纳',5250, '2009-05-13', 7,3),
       (10, '赵敏', 20, '市场部总监',12500, '2004-10-12', 1,2),
       (11, '鹿杖客', 56, '职员',3750, '2006-10-03', 10,2),
       (12, '鹤笔翁', 19, '职员',3750, '2007-05-09', 10,2),
       (13, '方东白', 19, '职员',5500, '2009-02-12', 10,2),
       (14, '张三丰', 88, '销售总监',14000, '2004-10-12', 1,4),
       (15, '俞莲舟', 38, '销售',4600, '2004-10-12', 14,4),
       (16, '宋远桥', 40, '销售',4600, '2004-10-12', 14,4),
       (17, '陈友谅', 42, null,2000, '2011-10-12', 1,null);
   ```
   
   dept表共6条记录，emp表共17条记录。



### 3.2.2、概述

多表查询就是指从多张表中查询数据。

原来查询单表数据，执行的SQL形式为：

​```sql
select * from emp;
   ```

那么我们要执行多表查询，就只需要使用逗号分隔多张表即可，如：

```sql
select * from emp, dept;
```

具体的执行结果如下：

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929104357582.png" alt="image-20220929104357582"  />

此时,我们看到查询结果中包含了大量的结果集，总共102条记录。

而这其实就是员工表emp所有的记录(17) 与 部门表dept所有记录(6) 的所有组合情况，这种现象称之为笛卡尔积。



笛卡尔积：笛卡尔乘积是指在数学中，两个集合A集合 和 B集合的所有组合情况。

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929104521000.png" alt="image-20220929104521000"  />

而在多表查询中，我们是需要消除无效的笛卡尔积的，只保留两张表关联部分的数据。

![image-20220929104540407](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929104540407.png)

在SQL语句中，如何来去除无效的笛卡尔积呢？我们可以给多表查询加上连接查询的条件即可。

```sql
select * from emp, dept where emp.dept_id = dept.id;
```

![image-20220929104718710](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929104718710.png)

而由于id为17的员工，没有dept_id字段值，所以在多表查询时，根据连接查询的条件并没有查询到。



### 3.2.3、分类

- 连接查询
  - 内连接：相当于查询A、B交集部分数据
  - 外连接：
    - 左外连接：查询左表所有数据，以及两张表交集部分数据
    - 右外连接：查询右表所有数据，以及两张表交集部分数据
    - 自连接：当前表与自身的连接查询，自连接必须使用表别名
- 子查询



## 3.3、内连接

![image-20220929110409085](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929110409085.png)

内连接查询的是两张表交集部分的数据。(也就是绿色部分的数据)。



内连接的语法分为两种: 隐式内连接、显式内连接。先来学习一下具体的语法结构。

1. 隐式内连接

   ```sql
   SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ;
   ```

2. 显式内连接

   ```sql
   SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
   ```



**案例**：

1. 查询每一个员工的姓名，及关联的部门的名称 (隐式内连接实现)

   表结构：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select emp.name, dept.name from emp, dept where emp.dept_id = dept.id;
   
   -- 为每一张表起别名,简化SQL编写
   select e.name, d.name from emp e, dept d where e.dept_id = d.id;
   ```

2. 查询每一个员工的姓名，及关联的部门的名称 (显式内连接实现)

   表结构：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select e.name, d.name from emp e inner join dept d on e.dept_id = d.id;
   ```



表的别名：

- tablea as 别名1, tableb as 别名2;
- tablea 别名1, tableb 别名2;



**注意**：

- 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。



## 3.4、外连接

![image-20220929111724775](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20220929111724775.png)

外连接分为两种，分别是：左外连接 和 右外连接。具体的语法结构为：

1. 左外连接

   ```sql
   SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
   ```

   左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据。

2. 右外连接

   ```sql
   SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
   ```

   右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据。



**案例**：

1.  查询emp表的所有数据，和对应的部门信息

   由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。

   表结构：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select e.*,d.name from emp e left outer join dept d on e.dept_id = d.id;
   
   select e.*,d.name from emp e left join dept d on e.dept_id = d.id;
   ```

2. 查询dept表的所有数据，和对应的员工信息(右外连接)

   由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。

   表结构：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select d.*,e.* from emp e right outer join dept d on e.dept_id = d.id;
   
   select d.*,e.* from emp e right join dept d on e.dept_id = d.id;
   ```



**注意**：

- 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。而我们在日常开发使用时，更偏向于左外连接。



## 3.5、自连接



### 3.5.1、自连接查询

自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。我们先来学习一下自连接的查询语法：

```sql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ;
```

而对于自连接查询，可以是内连接查询，也可以是外连接查询。



**案例**：

1. 查询员工 及其 所属领导的名字

   表结构：emp

   ```sql
   select a.name, b.name from emp a, emp b where a.managerid = b.id;
   ```

2. 查询所有员工 emp 及其领导的名字 emp，如果员工没有领导，也需要查询出来

   表结构：emp a，emp b

   ```sql
   select a.name, b.name from emp a left join emp b on a.managerid = b.id;
   ```



**注意**：

- 在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。



### 3.5.2、联合查询

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```sql
SELECT 字段列表 FROM 表A ...
UNION [ ALL ]
SELECT 字段列表 FROM 表B ....;
```



- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。
- union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重。



**案例**：

1. 将薪资低于 5000 的员工，和 年龄大于 50 岁的员工全部查询出来。

   当前对于这个需求，我们可以直接使用多条件查询，使用逻辑运算符 or 连接即可。那这里呢，我们也可以通过union/union all来联合查询。

   ```sql
   select * from emp where salary < 5000
   union all
   select * from emp where age > 50
   ```

   union all查询出来的结果，仅仅进行简单的合并，并未去重。

   union 联合查询，会对查询出来的结果进行去重处理。



**注意**：

- 如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错。



## 3.6、子查询



### 3.6.1、概述

1. 概念

   SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。

   ```sql
   SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
   ```

   子查询外部的语句可以是 INSERT / UPDATE / DELETE / SELECT 的任何一个。

2. 分类

   根据子查询结果不同,分为：

   - 标量子查询 (子查询结果为单个值)
   - 列子查询 (子查询结果为一列)
   - 行子查询 (子查询结果为一行)
   - 表子查询 (子查询结果为多行多列)

   根据子查询位置，分为：

   - WHERE之后
   -  FROM之后
   - SELECT之后



### 3.6.2、标量子查询

子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。

常用的操作符：= 、<>、>、>=、<、<= 



**案例**：

1. 查询 "销售部" 的所有员工信息

   - 查询 "销售部" 部门ID

     ```sql
     select id from dept where name = '销售部';
     ```

   -  根据 "销售部" 部门ID, 查询员工信息

     ```sql
     select * from emp where dept_id = (select id from dept where name = '销售部');
     ```

2. 查询在 "方东白" 入职之后的员工信息

   - 查询 方东白 的入职日期

     ```sql
     select entrydate from emp where name = '方东白';
     ```

   - 查询指定入职日期之后入职的员工信息

     ```sql
     select * from emp where entrydate > (select entrydate from emp where name = '方东白');
     ```



### 3.6.3、列子查询

子查询返回的结果是一列（可以是多行），这种子查询称为列子查询。

常用的操作符：in、not in、 any、some、 all



| 操作符 | 描述                                   |
| ------ | -------------------------------------- |
| in     | 在指定的集合范围之内，多选一           |
| not in | 不在指定的集合范围之内                 |
| any    | 子查询返回列表中，有任意一个满足即可   |
| some   | 与any等同，使用some的地方都可以使用any |
| all    | 子查询返回列表的所有值都必须满足       |



**案例**：

1. 查询 "销售部" 和 "市场部" 的所有员工信息

   - 查询 "销售部" 和 "市场部" 的部门ID

     ```sql
     select id from dept where name = '销售部' or name = '市场部';
     ```

   - 根据部门ID，查询员工信息

     ```sql
     select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');
     ```

2. 查询比 财务部 所有人工资都高的员工信息

   - 查询所有 财务部 人员工资

     ```sql
     select id from dept where name = '财务部';
     select salary from emp where dept_id = (select id from dept where name = '财务部');
     ```

   - 比 财务部 所有人工资都高的员工信息

     ```sql
     select * from emp where salary > all ( select salary from emp where dept_id = (select id from dept where name = '财务部') );
     ```

3. 查询比研发部其中任意一人工资高的员工信息

   - 查询研发部所有人工资

     ```sql
     select salary from emp where dept_id = (select id from dept where name = '研发部');
     ```

   - 比研发部其中任意一人工资高的员工信息

     ```sql
     select * from emp where salary > any ( select salary from emp where dept_id = (select id from dept where name = '研发部') );
     ```




### 3.6.4、行子查询

子查询返回的结果是一行（可以是多列），这种子查询称为行子查询。

常用的操作符：= 、<> 、in、not in



**案例**：

1. 查询与 "张无忌" 的薪资及直属领导相同的员工信息

   - 查询 "张无忌" 的薪资及直属领导

     ```sql
     select salary, managerid from emp where name = '张无忌';
     ```

   - 查询与 "张无忌" 的薪资及直属领导相同的员工信息

     ```sql
     select * from emp where (salary, managerid) = (select salary, managerid from emp where name = '张无忌');
     ```



### 3.6.5、表子查询

子查询返回的结果是多行多列，这种子查询称为表子查询。

常用的操作符：in



**案例**：

1. 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息

   - 查询 "鹿杖客" , "宋远桥" 的职位和薪资

     ```sql
     select job, salary from emp where name = '鹿杖客' or name = '宋远桥';
     ```

   - 查询与 "鹿杖客"，"宋远桥" 的职位和薪资相同的员工信息

     ```sql
     select * from emp where (job, salary) in ( select job, salary from emp where name = '鹿杖客' or name = '宋远桥' );
     ```

2. 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息

   - 入职日期是 "2006-01-01" 之后的员工信息

     ```sql
     select * from emp where entrydate > '2006-01-01';
     ```

   - 查询这部分员工，对应的部门信息

     ```sql
     select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id ;
     ```



## 3.7、多表查询案例

数据环境准备：

```sql
create table salgrade(
    grade int,
    losal int,
    hisal int
) comment '薪资等级表';
insert into salgrade values (1,0,3000);
insert into salgrade values (2,3001,5000);
insert into salgrade values (3,5001,8000);
insert into salgrade values (4,8001,10000);
insert into salgrade values (5,10001,15000);
insert into salgrade values (6,15001,20000);
insert into salgrade values (7,20001,25000);
insert into salgrade values (8,25001,30000);
```

这里主要涉及到的表就三张：emp员工表、dept部门表、salgrade薪资等级表。



1.  查询员工的姓名、年龄、职位、部门信息 （隐式内连接）

   表：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select e.name , e.age , e.job , d.name from emp e, dept d where e.dept_id = d.id;
   ```

2. 查询年龄小于30岁的员工的姓名、年龄、职位、部门信息（显式内连接）

   表：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select e.name, e.age, e.job, d.name from emp e inner join dept d on e.dept_id = d.id where e.age < 30;
   ```

3. 查询拥有员工的部门ID、部门名称

   表：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select distinct d.id, d.name from emp e, dept d where e.dept_id = d.id;
   ```

4. 查询所有年龄大于40岁的员工，及其归属的部门名称；如果员工没有分配部门，也需要展示出来(外连接)

   表：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select e.*, d.name from emp e left join dept d on e.dept_id = d.id where e.age > 40;
   ```

5. 查询所有员工的工资等级

   表：emp，salgrade

   连接条件：emp.salary >= salgrade.losal and emp.salary <= salgrade.hisal

   ```sql
   select e.*, s.grade, s.losal, s.hisal from emp e, salgrade s where e.salary >= s.losal and e.salary <= s.hisal;
   
   select e.*, s.grade, s.losal, s.hisal from emp e, salgrade s where e.salary between s.losal and e.salary;
   ```

6. 查询 "研发部" 所有员工的信息及工资等级

   表：emp，salgrade，dept

   连接条件：emp.salary between salgrade.losal and salgrade.hisal，emp.dept_id = dept.id

   ```sql
   select e.*, s.grade from emp e, dept d, salgrade s where e.dept_id = d.id and e.salary between s.losal and e.salary and d.name = '研发部';
   ```

7.  查询 "研发部" 员工的平均工资

   表：emp，dept

   连接条件：emp.dept_id = dept.id

   ```sql
   select avg(e.salary) from emp e, dept d where e.dept_id = d.id and d.name = '研发部';
   ```

8. 查询工资比 "灭绝" 高的员工信息。

   - 查询 "灭绝" 的薪资

     ```sql
     select salary from emp where name = '灭绝';
     ```

   - 查询比她工资高的员工数据

     ```sql
     select * from emp where salary > (select salary from emp where name = '灭绝');
     ```

9. 查询比平均薪资高的员工信息

   - 查询员工的平均薪资

     ```sql
     select avg(salary) from emp;
     ```

   - 查询比平均薪资高的员工信息

     ```sql
     select * from emp where salary > (select avg(salary) from emp);
     ```

10. 查询低于本部门平均工资的员工信息

    - 查询指定部门平均薪资

      ```sql
      select avg(e1.salary) from emp e1 where e1.dept_id = 1;
      select avg(e1.salary) from emp e1 where e1.dept_id = 2;
      ```

    - 查询低于本部门平均工资的员工信息

      ```sql
      select * from emp e2 where e2.salary < (select avg(e1.salary) from emp e1 where e1.dept_id = e2.dept_id);
      ```

11. 查询所有的部门信息，并统计部门的员工人数

    ```sql
    select d.id, d.name, (select count(*) from emp e where e.dept_id = d.id) '人数' from dept d;
    ```

12.  查询所有学生的选课情况，展示出学生名称，学号，课程名称

    表：student，course，student_course

    连接条件：student.id = student_course.studentid，course.id = student_course.courseid

    ```sql
    select s.name, s.no, c.name from student s, student_course sc, course c where s.id = sc.studentid and c.courseid = c.id ;
    ```



## 3.8、总结

1. 多表关系

   一对多：在多的一方设置外键，关联一的一方的主键

   多对多：建立中间表，中间表包含两个外键，关联两张表的主键

   一对一：用于表结构拆分，在其中任何一方设置外键，关联另一方的主键

2. 多表查询

   - 内连接：隐式、显示
   - 外连接：左外、右外
   - 自连接
   - 子查询：标量子查询、列子查询、行子查询、表子查询



# 四、事务



## 4.1、事务简介

事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

就比如：张三给李四转账1000块钱，张三银行账户的钱减少1000，而李四银行账户的钱要增加1000。 这一组操作就必须在一个事务的范围内，要么都成功，要么都失败。

正常情况：转账这个操作，需要分为以下这么三步来完成，三步完成之后，张三减少1000，而李四增加1000，转账成功

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002100052690.png" alt="image-20221002100052690" style="zoom:80%;" />

异常情况：转账这个操作，也是分为以下这么三步来完成，在执行第三步是报错了，这样就导致张三减少1000块钱，而李四的金额没变，这样就造成了数据的不一致，就出现问题了。

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002100135731.png" alt="image-20221002100135731" style="zoom:80%;" />

为了解决上述的问题，就需要通过数据的事务来完成，我们只需要在业务逻辑执行之前开启事务，执行完毕后提交事务。如果执行过程中报错，则回滚事务，把数据恢复到事务开始之前的状态。

<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002100214566.png" alt="image-20221002100214566" style="zoom:80%;" />

**注意**：默认MySQL的事务是自动提交的，也就是说，当执行完一条DML语句时，MySQL会立即隐式的提交事务。



## 4.2、 事务操作

数据准备：

```sql
drop table if exists account;
create table account(
    id int primary key AUTO_INCREMENT comment 'ID',
    name varchar(10) comment '姓名',
    money double(10,2) comment '余额'
) comment '账户表';
insert into account(name, money) VALUES ('张三', 2000), ('李四', 2000);
```



### 4.2.1、未控制事务

1. 测试正常情况

   ```sql
   -- 1. 查询张三余额
   select * from account where name = '张三';
   -- 2. 张三的余额减少1000
   update account set money = money - 1000 where name = '张三';
   -- 3. 李四的余额增加1000
   update account set money = money + 1000 where name = '李四';
   ```

   测试完毕之后检查数据的状态, 可以看到数据操作前后是一致的。

   <img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002100421870.png" alt="image-20221002100421870" style="zoom:80%;" />

2. 测试异常情况

   ```sql
   -- 1. 查询张三余额
   select * from account where name = '张三';
   -- 2. 张三的余额减少1000
   update account set money = money - 1000 where name = '张三';
   出错了....
   -- 3. 李四的余额增加1000
   update account set money = money + 1000 where name = '李四';
   ```

   我们把数据都恢复到2000，然后再次一次性执行上述的SQL语句(出错了.... 这句话不符合SQL语法，执行就会报错)，检查最终的数据情况，发现数据在操作前后不一致了。

   <img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002100533031.png" alt="image-20221002100533031" style="zoom:80%;" />



### 4.2.2、控制事务一

1. 查看/设置事务提交方式

   ```sql
   SELECT @@autocommit;
   SET @@autocommit = 0; —— 关闭自动提交事务，仅在同一会话中有效
   ```

2. 提交事务

   ```sql
   COMMIT;
   ```

3. 回滚事务

   ```sql
   ROLLBACK;
   ```



**注意**：上述的这种方式，我们是修改了事务的自动提交行为，把默认的自动提交修改为了手动提交，此时我们执行的DML语句都不会提交，需要手动的执行commit进行提交。



### 4.2.3、控制事务二

1. 开启事务

   ```sql
   START TRANSACTION 或 BEGIN;
   ```

2. 提交事务

   ```sql
   COMMIT;
   ```

3. 回滚事务

   ```sql
   ROLLBACK;
   ```



**转账案例**：

```sql
-- 开启事务
start transaction
-- 1. 查询张三余额
select * from account where name = '张三';
-- 2. 张三的余额减少1000
update account set money = money - 1000 where name = '张三';
-- 3. 李四的余额增加1000
update account set money = money + 1000 where name = '李四';
-- 如果正常执行完毕, 则提交事务
commit;
-- 如果执行过程中报错, 则回滚事务
rollback;
```



## 4.3、事务四大特性

- 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。
- 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。
- 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。
- 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

上述就是事务的四大特性，简称ACID。



## 4.4、并发事务问题

1. 脏读：一个事务读到另外一个事务还没有提交的数据。

   <img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002102059922.png" alt="image-20221002102059922" style="zoom:80%;" />

   比如B读取到了A未提交的数据。

2. 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。

   ​	<img src="https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002102151306.png" alt="image-20221002102151306" style="zoom:80%;" />

   事务A两次读取同一条记录，但是读取到的数据却是不一样的。

3. 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，再次查询还是没有，好像出现了 "幻影"。

   ![image-20221002102252558](https://raw.githubusercontent.com/zsc-dot/pic/master/img/Git/image-20221002102252558.png)



## 4.5、事务隔离级别

为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | ---- | ---------- | ---- |
| Read uncommitted      | √    | √          | √    |
| Read committed        | ×    | √          | √    |
| Repeatable Read(默认) | ×    | ×          | √    |
| Serializable          | ×    | ×          | ×    |



1. 查看事务隔离级别

   ```sql
   SELECT @@TRANSACTION_ISOLATION;
   ```

2. 设置事务隔离级别

   ```sql
   SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED |READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
   ```

   - session：当前会话生效
   - global：全局生效

**注意**：事务隔离级别越高，数据越安全，但是性能越低。



## 4.6、总结

1. 事务简介

   事务是一组操作的集合，这组操作要么全部执行成功，要么全部执行失败。

2. 事务操作

   ```sql
   START TRANSACTION; -- 开启事务
   commit;rollback; -- 提交/回滚事务
   ```

3. 事务四大特性

   原子性、一致性、隔离性、持久性

4. 并发事务问题

   脏读、不可重复读、幻读

5. 事务隔离级别

   Read uncommitted、Read committed、Repeatable Read(默认)、Serializable