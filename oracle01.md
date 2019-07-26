# Oracle基础入门 #

## 1.什么是oracle ##
	
	用户：管理表的最小单位      ----   对应Mysql数据库

	数据文件：存储用户表数据	
	
	表空间：多个数据文件组成表空间

	一个实例可以有多个表空间和多个用户 
	一个表空间可以有多个用户  多个数据文件
	一个数据文件可以有多个表信息
	一个用户可以有多个表信息

## 2.如何使用oracle使用（链接 / PL/sql） ##
	oracle安装
		1、把 xp_oracle_student.rar 解压到当前目录下名为 xp_oracle_student 的文件夹中
		
		2、运行 xp_oracle_student 目录下的 xp_oralce.vmx，会把 虚拟机 挂到 VMWare中
![](4.png)		

		3、VMWare 菜单
			编辑-->虚拟网络编辑器-->更改设置(右下角)-->选择网络-->仅主机-->子网IP改为 192.168.80.0-->应用-->确定
![](5.png)
			
		4.修改虚拟机网络设置
		
![](6.png)
		
		5.开启虚拟机		
		
		6、安装 PL/SQL
			安装完成后，打开，不输入任何用户名和密码，直接登录，进行配置。
			
# （以下配置暂时不需要配置） #
		7、在本机中配置环境变量(不是虚拟机)
			右击-->我的电脑-->属性-->高级系统设置-->环境变量-->系统变量-->新建
				变量名：TNS_ADMIN
				变量值：tnsnames.ora 文件所在目录

				
		8、乱码解决
			5.1、打开PL/SQL,查看服务器端编码
				select userenv('language') from dual;
			我实际查到的结果为:AMERICAN_AMERICA.ZHS16GBK
			5.2、执行语句 select * from V$NLS_PARAMETERS 
				查看第一行中PARAMETER项中为NLS_LANGUAGE 对应的VALUE项中是否和第一步得到的值一样。
				如果不是，需要设置环境变量.
				否则PLSQL客户端使用的编码和服务器端编码不一致,插入中文时就会出现乱码.
			5.3、设置环境变量
				计算机->属性->高级系统设置->环境变量->新建
				设置变量名:NLS_LANG,变量值:第1步查到的值， 我的是	AMERICAN_AMERICA.ZHS16GBK
			5.4、重新启动PLSQL,插入数据正常
			
		9、oracle用户
			sys/system/scott
			itcast

## 安装PL/SQL ##

1.运行 plsqldev1000.exe 下一步到安装完成

2.运行破解 keygen.exe 软件

3.输入破解信息

4.运行桌面图标 PLSQL Developer --点击取消，进入pl/sql未登录界面
![](3.png)

5.解压 instantclient_12_1.rar 压缩包

6.点击pl/sql ---  tools---配置instantclient_12_1路径

![](1.png)

7.重新打开Pl/sql进行登陆
	用户名：system
	密码：itcast
	数据库链接：192.168.80.88:1521/orcl
![](2.png)
		

## 3.oracle增删该查---表空间的创建 ## 

--创建表空间

	create tablespace itheima
	datafile 'c:\itheima.dbf'
	size 100m
	autoextend on
	next 10m;

--删除表空间
	
	drop tablespace itheima;

--创建用户

	create user itheima
	identified by itheima
	default tablespace itheima;

--给用户授权

--oracle数据库中常用角色

connect--连接角色，基本角色

resource--开发者角色

dba--超级管理员角色

--给itheima用户授予dba角色
	
	grant dba to itheima;

---切换到itheima用户下

---创建一个person表

	create table person(
	       pid number(20),
	       pname varchar2(10)
	);

---修改表结构

---添加一列
	
	alter table person add (gender number(1));

---修改列类型

	alter table person modify gender char(1);

---修改列名称

	alter table person rename column gender to sex;
---删除一列

	alter table person drop column sex;

---查询表中记录

	select * from person;
----添加一条记录

	insert into person (pid, pname) values (1, '小明');
	commit;

----修改一条记录

	update person set pname = '小马' where pid = 1;
	commit;

----三个删除

--删除表中全部记录

	delete from person;

--删除表结构

	drop table person;

--先删除表，再次创建表。效果等同于删除表中全部记录。

--在数据量大的情况下，尤其在表中带有索引的情况下，该操作效率高。

--索引可以提供查询效率，但是会影响增删改效率。

	truncate table person;

----序列不真的属于任何一张表，但是可以逻辑和表做绑定。

----序列：默认从1开始，依次递增，主要用来给主键赋值使用。

----dual：虚表，只是为了补全语法，没有任何意义。

	create sequence s_person;
	select s_person.nextval from dual;

----添加一条记录

	insert into person (pid, pname) values (s_person.nextval, '小明');
	commit;
	select * from person;

----scott用户，密码tiger。

--解锁scott用户

	alter user scott account unlock;

--解锁scott用户的密码【此句也可以用来重置密码】

	alter user scott identified by tiger;

--切换到scott用户下

## --单行函数：作用于一行，返回一个值。 ##

---字符函数

	select upper('yes') from dual;--YES
	select lower('YES') from dual;--yes

----数值函数

	select round(56.16, -2) from dual;---四舍五入，后面的参数表示保留的位数
	select trunc(56.16, -1) from dual;---直接截取，不在看后面位数的数字是否大于5.
	select mod(10, 3) from dual;---求余数

----日期函数

----查询出emp表中所有员工入职距离现在几天。

	select sysdate-e.hiredate from emp e;

----算出明天此刻

	select sysdate+1 from dual;

----查询出emp表中所有员工入职距离现在几月。

	select months_between(sysdate,e.hiredate) from emp e;

----查询出emp表中所有员工入职距离现在几年。

	select months_between(sysdate,e.hiredate)/12 from emp e;

----查询出emp表中所有员工入职距离现在几周。

	select round((sysdate-e.hiredate)/7) from emp e;

----转换函数
---日期转字符串

	select to_char(sysdate, 'fm yyyy-mm-dd hh24:mi:ss') from dual;

---字符串转日期

	select to_date('2018-6-7 16:39:50', 'fm yyyy-mm-dd hh24:mi:ss') from dual;

----通用函数
---算出emp表中所有员工的年薪
----奖金里面有null值，如果null值和任意数字做算术运算，结果都是null。

	select e.sal*12+nvl(e.comm, 0) from emp e;

---条件表达式
---条件表达式的通用写法，mysql和oracle通用

---给emp表中员工起中文名

	select e.ename, 
	       case e.ename
	         when 'SMITH' then '曹贼'
	           when 'ALLEN' then '大耳贼'
	             when 'WARD' then '诸葛小儿'
	               --else '无名'
	                 end
	from emp e;

---判断emp表中员工工资，如果高于3000显示高收入，如果高于1500低于3000显示中等收入，

-----其余显示低收入

	select e.sal, 
	       case 
	         when e.sal>3000 then '高收入'
	           when e.sal>1500 then '中等收入'
	               else '低收入'
	                 end
	from emp e;

----oracle中除了起别名，都用单引号。

----oracle专用条件表达式

	select e.ename, 
	        decode(e.ename,
	          'SMITH',  '曹贼',
	            'ALLEN',  '大耳贼',
	              'WARD',  '诸葛小儿',
	                '无名') "中文名"             
	from emp e;


## --多行函数【聚合函数】：作用于多行，返回一个值。 ##

	select count(1) from emp;---查询总数量
	select sum(sal) from emp;---工资总和
	select max(sal) from emp;---最大工资
	select min(sal) from emp;---最低工资
	select avg(sal) from emp;---平均工资


# ---分组查询 #

---查询出每个部门的平均工资

---分组查询中，出现在group by后面的原始列，才能出现在select后面

---没有出现在group by后面的列，想在select后面，必须加上聚合函数。

---聚合函数有一个特性，可以把多行记录变成一个值。

	select e.deptno, avg(e.sal)--, e.ename
	from emp e
	group by e.deptno;

---查询出平均工资高于2000的部门信息

	select e.deptno, avg(e.sal) asal
	from emp e
	group by e.deptno
	having avg(e.sal)>2000;

---所有条件都不能使用别名来判断。
--比如下面的条件语句也不能使用别名当条件

	select ename, sal s from emp where sal>1500;

---查询出每个部门工资高于800的员工的平均工资

	select e.deptno, avg(e.sal) asal
	from emp e
	where e.sal>800
	group by e.deptno;

----where是过滤分组前的数据，having是过滤分组后的数据。

---表现形式：where必须在group by之前，having是在group by之后。

---查询出每个部门工资高于800的员工的平均工资

---然后再查询出平均工资高于2000的部门

	select e.deptno, avg(e.sal) asal
	from emp e
	where e.sal>800
	group by e.deptno
	having avg(e.sal)>2000;

---多表查询中的一些概念
---笛卡尔积

	select *
	from emp e, dept d;

---等值连接

	select *
	from emp e, dept d
	where e.deptno=d.deptno;

---内连接

	select *
	from emp e inner join dept d
	on e.deptno = d.deptno;

---查询出所有部门，以及部门下的员工信息。【外连接】
	
	select *
	from emp e right join dept d
	on e.deptno=d.deptno;

---查询所有员工信息，以及员工所属部门

	select *
	from emp e left join dept d
	on e.deptno=d.deptno;

---oracle中专用外连接

	select *
	from emp e, dept d
	where e.deptno(+) = d.deptno;

	select * from emp;

---查询出员工姓名，员工领导姓名

---自连接：自连接其实就是站在不同的角度把一张表看成多张表。

	select e1.ename, e2.ename
	from emp e1, emp e2
	where e1.mgr = e2.empno;

------查询出员工姓名，员工部门名称，员工领导姓名，员工领导部门名称

	select e1.ename, d1.dname, e2.ename, d2.dname
	from emp e1, emp e2, dept d1, dept d2
	where e1.mgr = e2.empno
	and e1.deptno=d1.deptno
	and e2.deptno=d2.deptno;

---子查询

---子查询返回一个值

---查询出工资和SCOTT一样的员工信息

	select * from emp where sal in
	(select sal from emp where ename = 'SCOTT')

---子查询返回一个集合

---查询出工资和10号部门任意员工一样的员工信息

	select * from emp where sal in
	(select sal from emp where deptno = 10);

---子查询返回一张表

---查询出每个部门最低工资，和最低工资员工姓名，和该员工所在部门名称

---1，先查询出每个部门最低工资

	select deptno, min(sal) msal
	from emp 
	group by deptno;

---2，三表联查，得到最终结果。

	select t.deptno, t.msal, e.ename, d.dname
	from (select deptno, min(sal) msal
	      from emp 
	      group by deptno) t, emp e, dept d
	where t.deptno = e.deptno
	and t.msal = e.sal
	and e.deptno = d.deptno;


----oracle中的分页

---rownum行号：当我们做select操作的时候，

--每查询出一行记录，就会在该行上加上一个行号，

--行号从1开始，依次递增，不能跳着走。

----排序操作会影响rownum的顺序

	select rownum, e.* from emp e order by e.sal desc

----如果涉及到排序，但是还要使用rownum的话，我们可以再次嵌套查询。

	select rownum, t.* from(
	select rownum, e.* from emp e order by e.sal desc) t;


----emp表工资倒叙排列后，每页五条记录，查询第二页。

----rownum行号不能写上大于一个正数。

	select * from(
	    select rownum rn, tt.* from(
	          select * from emp order by sal desc
	    ) tt where rownum<11
	) where rn>5



# Select语句执行顺序 #

	from--where--group by--having--select--order by,  
	from:需要从哪个数据表检索数据 
	where:过滤表中数据的条件 
	group by:如何将上面过滤出的数据分组 
	having:对上面已经分组的数据进行过滤的条件  
	select:查看结果集中的哪个列，或列的计算结果 
	order by :按照什么样的顺序来查看返回的数据 
	 
	2.from后面的表关联，是自右向左解析的 
	而where条件的解析顺序是自下而上的。 






# 作业 #
	  查询员工姓名  员工部门  工资等级 上级姓名 上级部门  工资等级


------查询出员工姓名 emp，员工部门名称 dept，员工的工资等级(sal1) 员工领导姓名 emp2，员工领导部门名称 dept2 领导工资等级(sal2)
-----when 
select e1.ename, d1.dname, 
case
    when e1.sal>3001 then '等级五'
	        when e1.sal>2001 then '等级四'
            when e1.sal>1400 then '等级三'
              when e1.sal>1200 then '等级二'
                when e1.sal>800 then '等级一'
	               end
,e2.ename, d2.dname,
case
    when e2.sal>3001 then '等级五'
	        when e2.sal>2001 then '等级四'
            when e2.sal>1400 then '等级三'
              when e2.sal>1200 then '等级二'
                when e2.sal>800 then '等级一'
	               end
from emp e1, emp e2, dept d1, dept d2
where e1.mgr = e2.empno
and e1.deptno=d1.deptno
and e2.deptno=d2.deptno;

总结：
	
	分组查询 
	多表联查
	子查询
	自查询


分配
	30：回顾SSM搭建

	30：讲解练习以及反馈

	120：练习oracle 

	60:SSM搭建


















