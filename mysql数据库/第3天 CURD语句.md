## CRUD增删改查

### Insert语句

 ![image-20220226145252205](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226145252205.png)

```mysql
INSERT INTO emp (id,`name`,sex)
	VALUES (1,'lec',0);
```

使用细节：

- 数据输入时，字符/字符串使用单引号引起来。

- 可以插入空值NULL。（前提是该字段允许为空）

- 可以一次性添加多条语句。

![image-20220226151829290](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226151829290.png)

- 如果是给表中**所有字段添加数据**，**可以不写**前面的**字段名称**。
- 默认值使用：当不给某个字段值时，如果有默认值回自动添加。



### Update语句

![image-20220226152645188](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226152645188.png)

set：修改哪些列 和 赋予哪些值

where：指定应更新哪些行。没有where字句，则更新所有行。

expr是表达式。

```mysql
#全部员工薪资设置为3000
UPDATE emp 
	SET salary = 3000;
#某个员工薪资设置为5000
UPDATE emp 
	SET salary = 8000
	WHERE sex = '1';
#某个员工薪资设置增加1000
UPDATE emp 
	SET salary = salary+1000
	WHERE sex = '1';
```

- 如果要修改多字段： set 字段1=值1，字段2=值2...



### Delete语句

![image-20220226154246134](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226154246134.png)

```mysql
DELETE FROM emp 
	WHERE sex = 'llll';
```

- Delete语句不能删除某一列的值（可使用update设为 null 或者' '）

- 没有where字句，则删除所有行（记录）。
- delete语句只删除记录，不删除表。（DROP TABLE table_name ;）



### Select语句

#### 一、单表查询

##### 1.普通查询

![image-20220226161619758](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226161619758.png)

distinct 独特的。

![image-20220226162338059](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226162338059.png)



##### 2.运算查询

![image-20220226162432780](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226162432780.png)

可以是列名column，亦可以是表达式。

![image-20220226162635547](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226162635547.png)

AS 后面的字符串（含中文）可以不加单引号。

- 指定**列** 别名的关键字AS，可省略
- 同样指定**表** 别名的关键字AS，也可省略。

如： select * from emp (as) worker; 设置emp表别名为worker。



##### 3.where常用运算符（过滤搜索）

![image-20220226164132831](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226164132831.png)

不等号 和 like 和 逻辑运算符使用：

![image-20220226165138071](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226165138071.png)

between and是闭区间：

![image-20220226165425641](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226165425641.png)

in的使用：

![image-20220226165623976](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226165623976.png)



##### 4.排序查询结果

![image-20220226165923642](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226165923642.png)

asc升序（默认）、desc降序

![image-20220226170110793](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220226170110793.png)



#### 二、查询加强

##### 1. 日期可以使用比较字符（大于小于等于）

​		注意日期格式即可

![image-20220301191008434](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301191008434.png)

##### 2. like操作符（模糊）

​		%表示0到多个任意字符

​		_表示单个人任意字符

![image-20220301191106511](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301191106511.png)

##### 3. 查询空值，使用 xx is null。

 ![image-20220301191601055](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301191601055.png)

##### 4. 多次排序。

​	后面继续写即可。

![image-20220301192230273](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301192230273.png)

##### 5.  分页查询

需求：查找结果显示时，当数据量非常大，不可能全部显示，需要一页一页显示。

![image-20220301192905064](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301192905064.png)

使用：（显示第x页：前三条记录）

![image-20220301193121086](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301193121086.png)

![image-20220301193224543](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301193224543.png)

##### 6. 分组加强

![image-20220301195213043](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301195213043.png)

![image-20220301195237264](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301195237264.png)

![image-20220301195305835](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301195305835.png)

##### 总结

语法顺序

 ![image-20220301195402863](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301195402863.png)

案例：

![image-20220301200214619](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220301200214619.png)



#### 三、多表查询（重点、难点）

##### 1. 概念和使用：

select * from 表1, 表2;

多表查询默认处理返回的结果是：**笛卡尔积**

（第一张表的每一行和第二张表每一行结合成一条记录，共 表1行数*表2行数 条记录）

 **所以多表查询重点在于，写出过滤条件。**

```mysql
#1 显示雇员名，工资，及其所在的部门名字（雇员表中只有部门号，没有部门名）
SELECT ename,salary,dname FROM emp,dept
	WHERE emp.deptno = dept.deptno
--表名.列名

#2 显示在部门号为3的雇员名，工资，及其所在的部门名字。
SELECT dname,ename,salary
	FROM emp,dept
	WHERE emp.deptno = dept.deptno AND emp.deptno = 3

#3 显示各个员工的名字，工资，工资等级
SELECT ename,salary,grade
	FROM emp,salgrade
	WHERE salary BETWEEN losal AND hisal

```

- 提醒： 多表查询条件不能少于 **表的个数-1**，否则出现笛卡尔积。

​		如：3张表要有2个条件。



**多表查询的过程：第一张表的每条数据，拿出来与第二张表的各个记录进行对应拼接。**

**如：问题#1，把emp每个员工记录，与dept的每个部门记录对照，看哪条可以对应（给出条件是deptno相同的对应），拼接。**

​		**问题#3，把emp每个员工记录，与salgrade的每个工资等级对照，看哪条可以对应（给出的条件是emp表的salary数据要在工资表的losal和hisal中，看哪条记录符合），拼接。**



##### 2. 自连接

是指在同一张表的连接查询。（将同一张表看作两张表）

思考题目： 显示员工名称和他上级名称

![image-20220303193952216](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220303193952216.png)

即：mgr上级编号也在这张表中。

![image-20220303194123844](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220303194123844.png)



##### 3.子查询

是指嵌入在其他sql语句中的select语句，也称嵌套查询。

单行子查询：只返回一行数据的子查询语句

多行子查询：返回多行数据的子查询（in关键字）

```mysql
#显示与smith同一部门的所有员工
/*
	1.先查找名为smith员工的部门号
	2.查找出所有该部门号的员工
	采用嵌套
*/
SELECT * FROM emp
	WHERE deptno = (
		SELECT deptno 
		FROM emp 
		WHERE ename='李'
	)
```



- 子查询当作临时表使用

```mysql
#各个部门中，工资最高的员工
/*
	1.先用分组和max函数把各个部门最高工资算出来
	2.把上列得出结果（部门名，最高工资），作为一张表，查询出来是那个人。
*/
SELECT * FROM ( 
	SELECT deptno,MAX(salary) AS Max_salary
	FROM emp
	GROUP BY deptno
) temp ,emp
	WHERE emp.deptno = temp.deptno 
	AND emp.salary = temp.Max_salary 
#temp是临时表名字。给出两个where条件即可确定员工
```

![image-20220303201546977](C:\Users\10275\AppData\Roaming\Typora\typora-user-images\image-20220303201546977.png)

上述题目子查询得到的结果。



子查询得到结果，可用操作符

- 操作符

  - All

  ```mysql
  #查询工资比三号部门所有人都高的员工
  -- 使用all关键字写法
  SELECT * FROM emp
  	WHERE salary > ALL(
  	SELECT salary
  		FROM emp
  		WHERE deptno = 3
  		)
  -- 常规Max写法		
  SELECT * FROM emp
  	WHERE salary >(
  	SELECT MAX(salary) 
  		FROM emp
  		WHERE deptno = 3
  		)
  ```

  - any

  ```mysql
  #查询工资比三号部门其中一个人高的员工
  -- 使用any关键字写法
  SELECT * FROM emp
  	WHERE salary > any(
  	SELECT salary
  		FROM emp
  		WHERE deptno = 3
  		)
  -- 常规Min写法		
  SELECT * FROM emp
  	WHERE salary >(
  	SELECT min(salary) 
  		FROM emp
  		WHERE deptno = 3
  		)
  ```

  

- 多列子查询

此前除了当作表的子查询，其他都是只返回单个字段(单列)的子查询。 

```mysql
#得到和名为‘刘’员工的部门和岗位一致的其他员工（不包括’刘‘自己）
#全新写法：多字段比较
SELECT * FROM emp
	WHERE (deptno, job) = (
		SELECT deptno, job
		FROM emp
		WHERE ename = '刘'
	) AND emp.ename != '刘' 
	
#其他写法：作为新表
SELECT * FROM emp ,
	(SELECT * FROM emp WHERE ename = '刘') temp -- 新表temp
		WHERE emp.deptno = temp.deptno
		AND emp.job = temp.job 
		AND emp.ename != '刘' 
```



> select	表1.*	from  表1，表2
>
> 该形式表示把表1的所有字段输出
