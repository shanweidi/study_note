1.解压xp_oracle.rar  到当前文件夹
 
2.更改虚拟机网卡模式  --- 仅主机模式

3.更改虚拟机仅主机模式的网段地址- 192.168.80.0

4.使用cmd ping 192.168.80.88

5.安装pl/sql连接orcal数据库软件  ---- 下一步到底

6.更改pl/sql配置  --- tools - 首选项

7.192.168.80.88:1521/orcl 用户名system 密码itcast


# 视图 #

---视图的概念：视图就是提供一个查询的窗口，所有数据来自于原表。
---对原有表的引用，用户敏感数据隐藏，以及数据同步。

---查询语句创建表

	create table emp as select * from scott.emp;
	select * from emp;

---创建视图【必须有dba权限】

	create view v_emp as select ename, job from emp;

---查询视图

	select * from v_emp;

---修改视图[不推荐]

	update v_emp set job='CLERK' where ename='ALLEN';
	commit;

---创建只读视图

	create view v_emp1 as select ename, job from emp with read only;

---视图的作用？

---第一：视图可以屏蔽掉一些敏感字段。

---第二：保证总部和分部数据及时统一。

# 索引 #

--索引的概念：索引就是在表的列上构建一个二叉树

----达到大幅度提高查询效率的目的，但是索引会影响增删改的效率。

## 单列索引 ##

---创建单列索引

	create index idx_ename on emp(ename);

---单列索引触发规则，条件必须是索引列中的原始值。

---单行函数，模糊查询，都会影响索引的触发。

	select * from emp where ename='SCOTT'

## 复合索引 ##

---创建复合索引

	create index idx_enamejob on emp(ename, job);

---复合索引中第一列为优先检索列

---如果要触发复合索引，必须包含有优先检索列中的原始值。

	select * from emp where ename='SCOTT' and job='xx';---触发复合索引
	select * from emp where ename='SCOTT' or job='xx';---不触发索引
	select * from emp where ename='SCOTT';---触发单列索引。

# pl/sql编程语言 #

---pl/sql编程语言是对sql语言的扩展，使得sql语言具有过程化编程的特性。

---pl/sql编程语言比一般的过程化编程语言，更加灵活高效。

---pl/sql编程语言主要用来编写存储过程和存储函数等。

---声明方法

---赋值操作可以使用:=也可以使用into查询语句赋值

	declare
	    i number(2) := 10;
	    s varchar2(10) := '小明';
	    ena emp.ename%type;---引用型变量
	    emprow emp%rowtype;---记录型变量
	begin
    	dbms_output.put_line(i);
    	dbms_output.put_line(s);
    	select ename into ena from emp where empno = 7788;
    	dbms_output.put_line(ena);
    	select * into emprow from emp where empno = 7788;
    	dbms_output.put_line(emprow.ename || '的工作为：' || emprow.job);
	end;


---pl/sql中的if判断

---输入小于18的数字，输出未成年

---输入大于18小于40的数字，输出中年人

---输入大于40的数字，输出老年人

	declare
	  i number(3) := &ii;
	begin
	  if i<18 then
	    dbms_output.put_line('未成年');
	  elsif i<40 then
	    dbms_output.put_line('中年人');
	  else
	    dbms_output.put_line('老年人');
	  end if;
	end;

---pl/sql中的loop循环

---用三种方式输出1到10是个数字

---while循环

	declare
	  i number(2) := 1;
	begin
	  while i<11 loop
	     dbms_output.put_line(i);
	     i := i+1;
	  end loop;  
	end;

---exit循环

	declare
	  i number(2) := 1;
	begin
	  loop
	    exit when i>10;
	    dbms_output.put_line(i);
	    i := i+1;
	  end loop;
	end;

---for循环

	declare
	
	begin
	  for i in 1..10 loop
	     dbms_output.put_line(i);  
	  end loop;
	end;

#游标：可以存放多个对象，多行记录。#

---输出emp表中所有员工的姓名

	declare
	  cursor c1 is select * from emp;
	  emprow emp%rowtype;
	begin
	  open c1;
	     loop
	         fetch c1 into emprow;
	         exit when c1%notfound;
	         dbms_output.put_line(emprow.ename);
	     end loop;
	  close c1;
	end;

-----给指定部门员工涨工资

	declare
	  cursor c2(eno emp.deptno%type) 
	  is select empno from emp where deptno = eno;
	  en emp.empno%type;
	begin
	  open c2(10);
	     loop
	        fetch c2 into en;
	        exit when c2%notfound;
	        update emp set sal=sal+100 where empno=en;
	        commit;
	     end loop;  
	  close c2;
	end;

----查询10号部门员工信息

	select * from emp where deptno = 10;

# 存储过程 #

--存储过程：存储过程就是提前已经编译好的一段pl/sql语言，放置在数据库端

--------可以直接被调用。这一段pl/sql一般都是固定步骤的业务。

----给指定员工涨100块钱
	create or replace procedure p1(eno emp.empno%type)
	is

	begin
	   update emp set sal=sal+100 where empno = eno;
	   commit;
	end;
	
	select * from emp where empno = 7788;

----测试p1

	declare
	
	begin
	  p1(7788);
	end;

# 存储函数 #

----通过存储函数实现计算指定员工的年薪

----存储过程和存储函数的参数都不能带长度

----存储函数的返回值类型不能带长度

	create or replace function f_yearsal(eno emp.empno%type) return number
	is
	  s number(10);     
	begin
	  select sal*12+nvl(comm, 0) into s from emp where empno = eno;
	  return s;
	end;

----测试f_yearsal

----存储函数在调用的时候，返回值需要接收。

	declare
	  s number(10); 
	begin
	  s := f_yearsal(7788);
	  dbms_output.put_line(s);
	end;

---out类型参数如何使用

---使用存储过程来算年薪

	create or replace procedure p_yearsal(eno emp.empno%type, yearsal out number)
	is
	   s number(10);
	   c emp.comm%type;
	begin
	   select sal*12, nvl(comm, 0) into s, c from emp where empno = eno;
	   yearsal := s+c;
	end;

---测试p_yearsal

	declare
	  yearsal number(10);
	begin
	  p_yearsal(7788, yearsal);
	  dbms_output.put_line(yearsal);
	end;

----in和out类型参数的区别是什么？

---凡是涉及到into查询语句赋值或者:=赋值操作的参数，都必须使用out来修饰。


# 存储过程和存储函数的区别 #

---语法区别：关键字不一样，

------------存储函数比存储过程多了两个return。

---本质区别：存储函数有返回值，而存储过程没有返回值。

----------如果存储过程想实现有返回值的业务，我们就必须使用out类
型的参数。

----------即便是存储过程使用了out类型的参数，起本质也不是真的有了返回值，

----------而是在存储过程内部给out类型参数赋值，在执行完毕后，我们直接拿到输出类型参数的值。

----我们可以使用存储函数有返回值的特性，来自定义函数。

----而存储过程不能用来自定义函数。

----案例需求：查询出员工姓名，员工所在部门名称。

----案例准备工作：把scott用户下的dept表复制到当前用户下。

	create table dept as select * from scott.dept;

----使用传统方式来实现案例需求

	select e.ename, d.dname
	from emp e, dept d
	where e.deptno=d.deptno;

----使用存储函数来实现提供一个部门编号，输出一个部门名称。

	create or replace function fdna(dno dept.deptno%type) return dept.dname%type
	is
	  dna dept.dname%type;
	begin
	  select dname into dna from dept where deptno = dno;
	  return dna;
	end;

---使用fdna存储函数来实现案例需求：查询出员工姓名，员工所在部门名称。

	select e.ename, fdna(e.deptno)
	from emp e;
	
# 触发器，就是制定一个规则，在我们做增删改操作的时候， #

----只要满足该规则，自动触发，无需调用。

----语句级触发器：不包含有for each row的触发器。

----行级触发器：包含有for each row的就是行级触发器。

-----------加for each row是为了使用:old或者:new对象或者一行记录。

---语句级触发器

----插入一条记录，输出一个新员工入职

	create or replace trigger t1
	after
	insert
	on person
	declare
	
	begin
	  dbms_output.put_line('一个新员工入职');
	end;

---触发t1

	insert into person values (1, '小红');
	commit;
	select * from person;
	
---行级别触发器

---不能给员工降薪

---raise_application_error(-20001~-20999之间, '错误提示信息');

	create or replace trigger t2
	before
	update
	on emp
	for each row
	declare
	
	begin
	  if :old.sal>:new.sal then
	     raise_application_error(-20001, '不能给员工降薪');
	  end if;
	end;

----触发t2

	select * from emp where empno = 7788;
	update emp set sal=sal-1 where empno = 7788;
	commit;
	
----触发器实现主键自增。【行级触发器】

---分析：在用户做插入操作的之前，拿到即将插入的数据，

------给该数据中的主键列赋值。

	create or replace trigger auid
	before
	insert
	on person
	for each row
	declare
	
	begin
	  select s_person.nextval into :new.pid from dual;
	end;

--查询person表数据

	select * from person;
	
---使用auid实现主键自增
	
	insert into person (pname) values ('a');
	commit;
	insert into person values (1, 'b');
	commit;


----oracle10g    ojdbc14.jar
----oracle11g    ojdbc6.jar

mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=10.2.0.4.0 -Dpackaging=jar -Dfile=ojdbc14.jar

oracle数据库链接信息

	String driver = "oracle.jdbc.driver.OracleDriver";
	String url = "jdbc:oracle:thin:@192.168.80.88:1521:orcl";
	String username = "scott";
	String password = "tiger";

一。使用oracle 完成基本的数据库操作

	String url="jdbc:oracle:thin:@192.168.80.88:1521:orcl";
        String user="itheima";
        String pass="itheima";

        //1.注册驱动
        Class.forName("oracle.jdbc.driver.OracleDriver");
        //2.获得链接
        Connection conn = DriverManager.getConnection(url, user, pass);
        //3.创建preperstatment
        PreparedStatement ps = conn.prepareStatement("select * from emp where empno = ?");
        ps.setString(1,"7788");
        //4.执行sql语句
        ResultSet resultSet = ps.executeQuery();
        //遍历集合
        while(resultSet.next()){
            Object ename = resultSet.getObject("ename");
            System.out.println(ename);
        }
        //5.关闭链接  -- 先开后关
        resultSet.close();
        ps.close();
        conn.close();

二。使用jdbc调用oracle存储过程和存储函数
	
	     {?= call <procedure-name>[(<arg1>,<arg2>, ...)]}  -- 存储函数
         {call <procedure-name>[(<arg1>,<arg2>, ...)]} --存储过程的调用

		       String url="jdbc:oracle:thin:@192.168.80.88:1521:orcl";
        String user="itheima";
        String pass="itheima";

        //1.注册驱动
        Class.forName("oracle.jdbc.driver.OracleDriver");
        //2.获得链接
        Connection conn = DriverManager.getConnection(url, user, pass);
        //3.创建preperstatment
        /**
         * {?= call <procedure-name>[(<arg1>,<arg2>, ...)]}  -- 存储函数
         *    {call <procedure-name>[(<arg1>,<arg2>, ...)]} --存储过程的调用
         *    procedure-name
         *    declare
         * 	  s number(10);
         * 	begin
         * 	  s := f_yearsal(7788);
         * 	  dbms_output.put_line(s);
         * 	end;
         * p_yearsal(eno emp.empno%type, yearsal out number)
         */
        CallableStatement ps = conn.prepareCall("{call P_YEARSAL(?,?)}");
        ps.setString(1,"7788");  //in
        //因为第二个之是out值，所以需要制定值得类型以及位置
        ps.registerOutParameter(2, OracleTypes.NUMBER);  // out
        //4.执行sql语句
        ps.execute();
        //遍历集合
        Object yearsal = ps.getObject(2);
        System.out.println("年薪:"+yearsal);
        ps.close();
        conn.close();

三。如何是用mybatis调用存储过程以及存储函数

	存储函数调用
	1） <!-- statementType="CALLABLE" -->
    <select id="f_YEARSAL" statementType="CALLABLE">
        {#{yearsal ,mode=OUT,jdbcType=DOUBLE}= call f_yearsal(#{eno,mode=IN,jdbcType=DOUBLE})}
    </select>

	2）
		public void test(){
        Map<String,Object> map = new HashMap<>();
        map.put("yearsal",null);
        map.put("eno","7788");
        itemsMapper.f_YEARSAL(map);
        Object yearsal = map.get("yearsal");
        System.out.println(yearsal);
    }

	存储过程
	<!-- statementType="CALLABLE" -->
    <select id="d_YEARSAL" statementType="CALLABLE">
        {call f_yearsal(#{eno,mode=IN,jdbcType=DOUBLE},#{yearsal ,mode=OUT,jdbcType=DOUBLE})}
    </select>
	
 	@Test
    public void tes2(){
        Map<String,Object> map = new HashMap<>();
        map.put("eno","7788");
        map.put("yearsal",null);
        itemsMapper.f_YEARSAL(map);
        Object yearsal = map.get("yearsal");
        System.out.println(yearsal);
    }


# 重点内容总结： #
	1.oracle 子查询  （重点）
	2.oracle分页查询（面试题） (重点)
	3.了解视图的概念以及视图的使用场景（屏蔽敏感字段，数据同步） create view (视图名称) AS 创建视图的例信息（了解）
	4.了解索引概念，已经索引优势。  create index (索引名称) on 索引字段（了解）
	5.存储过程   ---  一段写好得pl/sql脚本 （无返回值）  （了解）
	6.存储函数	 ---  一段写好得pl/sql脚本 （有返回值）（了解）
	7.触发器    ----  在执行增删改前或者之后后，触发一段pl/sql脚本 (了解)
	8.基于mybatis调用存储过程以及存储函数得流程   ----  （理解）

# 练习 #

1.查询所有部门得平均工资

2.查询指定员工得部门信息以及薪资等级

3.查询工资最低得五名员工得姓名以及部门名称


用oracle 整合SSM  ----  完善转账案例






