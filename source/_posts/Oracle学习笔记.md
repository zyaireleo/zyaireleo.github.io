---
title: Oracle学习笔记
date: 2020-12-08 08:59:37
tags: 数据库
descriptions: 主要记录一些和mysql记忆中不同之处
---

### 安装

#### 环境：

Windows10+Oracle11g +Oracle Developer19.2

### 基本语法

#### DCL

##### 添加注释

```sql
--为表添加注释
COMMENT ON TABLE EAMP IS '雇员表';
--为列添加注释
COMMENT ON COLLUMN EMP.EMPNO IS '雇员工号';
```



#### DML

- 海量数据操作

```
数据泵 \  SQL Loader\ 外部表
```

- 删除表

```sql
--delete  truncate drop
delete from emp  ; --可以回退
truncate table emp  ;--不能回退
--原因： DML：insert  update  delete  ->可以回退
```

```
测试二者执行时间	
	打开执行时间：
	set timing on/off
对于少量数据： delete 效率高  ，一行一行删除
对于海量数据：truncate效率高 ，  a.drop table 丢弃整张表 ，b.重新创建表
delete支持闪回， truncate不支持闪回
delete不会释放空间 （换两个地方存储数据[undo空间]），trucante会
delete会产生碎片，trunate不会
	如果碎片太多，需要整理碎片：a.  alter table 表名 move ;  b.导出导入
```



#### DDL

- 追加新列

```sql
alter table mytab6 add myother varchar2(10) ;
```

- 修改列

```sql
--修改列的长度
alter table mytab6 modify  myother varchar2(20) ;
--修改列的类型

alter table mytab6 modify  myother number ;
--注意： blob/clob不能修改  ->先删除此列，重新追加

alter table mytab6 add myother2 blob ;

alter table mytab6 modify  myother2 number ;
```

- 删除列

```sql
alter table mytab6 drop column myother2 ;
```

- 重命名列

```sql
alter table mytab6 rename column myother to myother3 ;
```



##### oracle主键

- 主键类型

```sql
检查约束（check）           name > 4 
唯一约束（Unique）          id:1  2  3 4  null 
主键约束（Primary key）     类似唯一约束(唯一) 
外键约束（Foreign Key）      两张表 学生表   课程表(1 2 3)
非空约束(Not null)          不能为null
默认约束（Default）         adress:   西安
```

举个栗子（列级约束）

```sql
create table student(
 stuno number(3) primary key  ,
 stuname varchar2(10) unique  ,
 stuaddress varchar2(20)  default '陕西西安' check(length(stuaddress)>2) ,
 stubid number(3)
);
```

- 增加约束

  ```
  alter table 表名 add constraint 约束名  约束类型
  ```

  - eg

  ```sql
  create table student4(
   stuno number(3) ,
   stuname varchar2(10)  ,
   stuaddress varchar2(20) ,
   subid number(3) 
  );
  alter table student4 add constraint UQ_stuaddress4  unique(stuaddress);
  alter table student4 add constraint PK_stuno4  primary key (stuno );
  
  alter table student4 add constraint CK_stuname4  check(length(stuname)>2);
  
  alter table student4 add constraint FK_student4_sub   foreign key(subid) references sub(sid);
  ```

- 约束命名

  - 规范：约束类型字段名

  ```
  主键：	PK_stuno
  检查约束：  CK字段名
  唯一约束： UQ字段名
  非空约束：  NN字段名
  外键约束：  FK_子表_父表
  默认约束： 一般不需要命名
  加约束名： constraint 约束名 
  ```

  - 举个栗子

  ```sql
  create table student(
   stuno number(3) constraint PK_stuno    primary key  ,
   stuname varchar2(10) constraint NN_stuname  not null  constraint  UQ_stuname  unique ,
   stuaddress varchar2(20) default '陕西西安' constraint CK_stuaddress  check(length(stuaddress)>2),
   stubid number(3)
  );
  ```

  - 表级约束

  ```sql
  create table student2(
   stuno number(3) ,
   stuname varchar2(10)  ,
   stuaddress varchar2(20) ,
   stubid number(3),
    constraint PK_sno primary key(stuno) ,
    constraint UQ_sname_subid unique(stuname,stubid),
    constraint CK_saddress check( length(stuAddress)>2)
  );
  ```

##### 级联

- 级联删除和级联置空

  ```sql
  drop table student3;
  create table student3(
   stuno number(3) ,
   stuname varchar2(10)  ,
   stuaddress varchar2(20) ,
   subid number(3)  ,
   constraint FK_student3_sub foreign key(subid) references sub(sid)  on delete cascade
  );
  -- on delete cascade表示级联删除
  -- 换成on delete set null 表示级联置空
  -- 级联删除：当删除父表中的数据时，子表 会跟着删除相对应的数据；
  -- 级联置空：当删除父表中的数据时，子表 会将 相对应的 那一字段的值设置为Null，其他字段不影响；
  
  ```

  

#### DQL

##### 普通查询

- 去重查询

```plsql
select distinct deptno from emp
```

- 连接符

```plsql
select 'hello'||'world' from dual;
```

- 模糊查询

```sql
--姓名中第二个字母是M的员工信息：
select *from emp where ename like  '_M%' ;
	
--姓名中包含M的员工信息：
select *from emp where ename like  '%M%' ;
--姓名长度>6的员工信息：   >6  >=7
select *from emp where ename like  '_______%' ;

--姓名中包含下划线的 
zhang_san
select *from emp where ename  like '%\_%'  escape '\' ;  

--not  in 不能出现null：如果出现了null，结果为null
select *from emp where deptno not in(10,20,30,null) ;
```

- 排序

```plsql
--order by 字段名|表达式|序号
select *from emp order by sal desc ;--默认asc升序
```

- 对null的处理

```sql
nvl:if
		nvl(comm,0 )

	nvl2:if...else
	nvl2(comm,comm,0)
			if(comm==null)  return 0
			else   return comm
```

​	NULL:自身特性： 如果!=NULL则无法查询出任何数据 

```sql
--查询 不是领导的员工信息（子查询时排除NULL）
--不是领导：判断empno 是否存在于mgr中

select * from emp 
where empno not in (select mgr from emp where mgr is not null )
```

##### 函数

单行函数： 字符函数  数值函数  日期函数  转换函数 通用函数

- 时间日期函数

  - 时间函数

  ```
  select current_time() from dual;---- mysql:时间。 
  select current_date() form dual;  ---mysql；日期  
  select current_timestamp() from dual;---mysql：日期时间  
  ```

  - sysdate：当前时间

  ```plsql
  --计算员工工龄 ：入职日期 天 星期 月 年
  select ename ,hiredate , (sysdate - hiredate) , (sysdate - hiredate)/7 , (sysdate - hiredate)/30, (sysdate - hiredate)/365 from emp;
  ```

  - months_between(日期1,日期2) ： 日期1和日期2之间相差的月份

  - add_months(日期,月数)：返回加上x月后的日期d的值 

  - 当前最大是第几天： last_day

  - 下一个星期n是哪一天next_day

    ```plsql
    select next_day(sysdate,'星期五') from dual ;
    ```

- 数字函数

  ```sql
  round（number，n）--返回四舍五入后的值  
  
  trunc（number，n）
  
  mod（x，y）求余数
  
  ceil()上取整 
  
  floor()下取整  
  ```

- 转换函数

  - TO_CHAR

    操作日期

    | 格式元素   | 含义                                 |
    | ---------- | ------------------------------------ |
    | YYYY       | 代表四位、两位数字的年份             |
    | YYMM       | 用数字表示的月份                     |
    | MON        | 月份的缩写、对中文月份来说就是全称   |
    | DD         | 数字表示的日                         |
    | DY         | 星期的缩写，对中文的星期来说就是全称 |
    | HH24、HH12 | 12小时或者24小时进制下的时间         |
    | Ml         | 分钟数                               |
    | ss         | 秒数                                 |

  eg：select sysdate, to_char(sysdate,'yyyy-mon-dd hh12:mi:ss') from dual; 

  - 操作数字

    | 控制符 | 含义                                                         |
    | ------ | ------------------------------------------------------------ |
    | 9      | 代表一位数字，如果该位没有数字则不进行显示，但对于小数点后面的部分仍会强制显示 |
    | 0      | 代表一位数字，如果该位没有数字则强制显示0                    |
    | ￥     | 显示美元符号                                                 |
    | L      | 显示本地货币符号                                             |
    | .      | 显示小数点                                                   |
    | ,      | 显示千分位符号                                               |

  - to_number & to_date 

- coalesce :从左往后 找到第一个不为null的值

- 条件判断函数

  - decode(字段,条件1，返回值1，条件2，返回2，....,最后表达式)

    ```plsql
    select ename,job , sal 涨前,  decode(job, 'PRESIDENT',sal+1000,'MANAGER',sal+500,sal+300) 涨后 FROM EMP;
    ```

  - case表达式

    ```plsql
    	select ename ,job ,sal  涨前,case job 
    	when  'PRESIDENT' then sal+1000
    	when  'MANAGER' then sal+500
    	else  sal + 300 end
    涨后 
    	from emp ; 
    ```

- 字符函数

```plsql
substr(str,begin,len)  --从1开始数

length --字符数  
lengthb --字节数

lpad/ rpad：--填充
trim --去掉任意字符

Instr（）--字符串出现的位置, instr（ string ，’A‘） 
replace --替换
```

- 数值函数

```sql
round() --round(数字,n位数):四舍五入  ，保留n位小数
trunc() --trunc(数字,n位数):舍尾，保留n位小数

mod() --取模
```

多行函数：组函数、 聚合函数

//我直接TM省略，和mysql大差不差????

##### 多表查询

- 交叉连接(笛卡尔积)
- 内连接

```
//多张表通过  相同字段进行匹配，只显示匹配成功的数据
select * from emp e ,dept d
where e.deptno = d.deptno ;
```

不等值连接(用的少)

- 左外连接

```
select * from emp e ,dept d
where e.deptno = d.deptno(+) ;
```

- 右外连接

```
select * from emp e
left outer join dept d
on e.deptno(+) = d.deptno 
```

- 全外连接

   左外 + 右外连接 - 去重

- 自连接

  将一张表 通过别名 “视为”不同的表

```
//查询 员工姓名，以及 该员工的领导姓名
select e.ename ,b.ename from emp e,emp b
where e.mgr =b.empno;
```

##### 查询优化

- 层次连接

```
select level ,empno, ename ,mgr from emp 
	connect by prior  empno=mgr
	start with mgr is null
	order by level ;
```

- 子查询

  ```
  select *from emp  where sal > (select SAL from emp where ename = 'SCOTT' )
  ```

  - 分组查询

  ```
  select deptno,min(sal) from emp  
  group by deptno
  having min(sal) > ( select min(sal) from emp where deptno =10  );
  ```

  - 增强group by：rollup()

  ```sql
  select deptno,job ,sum(sal)   from emp group by rollup( deptno,job) ;
  
  --group by  rollup( a,b)相当于：
  --		group by a,b	
  --		group by a,
  --		group by null 
  ```

  





- 修改oracle默认日期格式

```plsql
alter session set NLS_DATE_FORMAT = 'yyyy-mm-dd' ;

alter session set NLS_DATE_FORMAT = 'DD-MON-RR' ;
```

- 范围查询

```plsql
--必须
between  小 and  大
```

- 模糊查询

```sql
--姓名中包含下划线的 

select *from emp where ename  like '%\_%'  escape '\' ;  

--not  in 不能出现null：如果出现了null，结果为null
select *from emp where deptno not in(10,20,30,null) ;
	
```

- 查看当前系统编码格式：

```sql
select * from nls_database_parameters ;
```

#### PLSQL

##### Helleo World

- 代码

```plsql
--打开之后才能执行
set serveroutput on;

--declare 定义变量、常量、光标（游标除外）、例外（自定义异常）
declare
--先变量名 再变量类型

	-- :=为赋值符号,
	psex char(3) :='男';
	
	--可以先声明不赋值
	pname varchar2(10);
	
	--引用型(类型和emp表的empname字段保持一致, 推荐使用)
	pname emp.empname%type;

begin
	dmbs_output.put_line('Hello World');
end;

```

- another eg

```plsql
set serveroutput on ;
declare
	pnum number := 3 ;
    
begin
    if pnum=1   then   dbms_output.put_line('一');
    elsif pnum=2  then   dbms_output.put_line('二');
    else dbms_output.put_line('其他');
    end if ;   
    
end ;
```

- loop示例

```plsql
set serveroutput on ;
declare
	    pnum number:=1 ;
        psum number:= 0 ;
begin
        loop
            exit when pnum >5 ;
            psum := psum + pnum ; --sum+= i ;
            pnum := pnum +1 ;
        end loop ;
        dbms_output.put_line(psum);
end ;
```

##### 存储过程

- 语法

```plsql
create  procedure 过程名
/*
参数列表，可以无参
参数名 	出/入参	参数类型;
IN是入参，OUT是出参
O_TEST 	IN NUMBER;
IN_TEST	OUT	NUMBER;
*/
as
/*
定义参数

V_TEST	NUMBER(4);
V_TEST2	VARCHAR2(10)
*/
begin 
/*
plsql语句
*/
end ;
```

- 示例

```plsql
--存储过程： 传入员工编号，返回姓名、工作
create or replace procedure getEmpInfo(pempno in emp.empno%type, pename out emp.ename%type, pjob out emp.job%type )
as
   
begin 
    select ename  ,job into pename ,pjob from emp where empno = pempno;
end ;
```



##### 光标

- 语法

```
定义
	cursor 光标名(参数列表)
	is
	select ....
```

- 光标的属性

```
%isopen  	%rowcount	%found	%notfound 
```

- 示例1

```plsql
--查询并打印全部员工的姓名、薪水
set serveroutput on ;
declare
	--变量、常量、光标（游标）、例外（自定义异常）
  cursor cemp  is select ename ,sal from emp ;
  pename emp.ename%type ; 
  psal emp.sal%type ;
  
begin
      open cemp ;--1.打开光标
      loop    --2.循环 准备获取每一行数据
         fetch  cemp  into pename,psal ;--3.一行一行获取光标的值
         exit when cemp%notfound ;
         
         dbms_output.put_line(pename||'的工资是：'||psal);
         
      end loop ;
      close cemp ;--关闭光标
    
end ;
```

- 示例2

```plsql
--涨工资。 每个10%， 按入职时间顺序涨工资，且涨后的总工资不能超过5万。计算 需要涨工资的人个数 以及涨后的工资总额。

set serveroutput on ;

declare 
 cursor cemp is select empno,sal from emp order by hiredate asc ;
 pempno emp.empno%type ;
 psal emp.sal%type ;
 countEmp number :=0 ;
 salTotal number :=0 ;
  
begin 
    OPEN cemp ;
    loop 
        
        --exit when salTotal > 50000 ;--  5.1
        
        fetch cemp into pempno ,psal ;
        exit when cemp%notfound ;
        
        if salTotal + psal*1.1 < 50000
            --涨工资
           then  update emp set sal = sal*1.1 where empno = pempno;
           countEmp := countEmp +1;
            --salTotal: 50  ,51 
            salTotal := salTotal + psal*1.1 ;  --psal 是变量， sal是表的字段
        else 
            dbms_output.put_line('人数'||countEmp ||'--'|| '涨后的总额'||salTotal);
        
            exit  ;
        
        end if ;
    
    end loop ;
 
    close cemp ;

end ;
```

- 示例3

```plsql
/*统计各部门的工资情况。格式如下
部门编号		<2000的人数		2000-4000人数		>4000人数	工资总额
		count1			count2			count3		salTotal
		
光标保存各个部门：10  20  30*/
set serveroutput on ;

declare 
    cursor cdept is select deptno from dept ;--10  20 30 40
    pdeptno dept.deptno%type ;
    
    --部门中员工的所有工资
    cursor cemp(dno number) is select sal from emp where deptno = dno;
    psal emp.sal%type ;
    
    count1 number;
    count2 number;
    count3 number ;
    
    --各部门工资总额
    salTotal number ;
    
    
begin 
    open  cdept;
    loop  --外层循环：遍历所有的部门 编号；；内层循环：遍历某个部门的所有工资
        fetch cdept into pdeptno ;
        exit when cdept%notfound ;
        count1 :=0 ;
        count2 :=0 ;
        count3 :=0 ;
        select sum(sal) into salTotal from emp where deptno =pdeptno ;
        open  cemp(pdeptno) ;
            loop
                fetch cemp into psal ;
                exit when cemp%notfound ;
                if psal<2000 then count1:=count1+1;
                elsif psal>=2000 and psal<4000 then  count2:=count2+1;
                else count3:= count3+1 ;
                end if ;                
            end loop ;       
        
        close cemp ;
        dbms_output.put_line(pdeptno||' '||count1||' '||count2||' '||count3||' '||salTotal);     
        
    end loop;
    
    close cdept;

end ;

public void hellworld()
{
	System.out.println("hello world")
}

```

##### 例外（异常）

- 系统例外
  	no_data_found 	
    	too_many_rows 
    	zero_Divide
    	value_error:算术或转换错误
    	timeout_on_resource：资源等待超时
- 示例1

```plsql
set serveroutput on ;
declare 
   pnum number ;
   
begin 

   pnum := 1/0 ;
   
   exception 
     when zero_divide then dbms_output.put_line('0不能作为除数');
     when too_many_rows then dbms_output.put_line('行数太多');
     when others then dbms_output.put_line('其他例外..');
end ;
```

- 示例2

```plsql
--是否存在编号50的部门，如果不存在 ，抛出一个例外；如果存在，将该部门的员工姓名 打印。。

set serveroutput on ;
declare 
  cursor cemp(dno number) is select ename from emp where deptno = dno ;
   pename emp.ename%type ;
   no_emp_found exception ;
   
begin 
   open cemp(50);
     fetch cemp into pename ;
    if cemp%notfound then  raise no_emp_found ;
    
    else 
           loop
                  exit when cemp%notfound ;
                  fetch cemp into pename ;
           end loop ;
        
    end if ;
        
    exception  
           when no_emp_found then dbms_output.put_line('自定义例外,没有此号部门...');
  
   close cemp;
   
end ；
```

##### IFELSE

- 语法

```
if 条件 then ..;
elsif 条件 then ...
else ..
end if ;
```

- 示例

```plsql
set serveroutput on ;
declare
	pnum number := 3 ;    
begin
    if pnum=1   then   dbms_output.put_line('一');
    elsif pnum=2  then   dbms_output.put_line('二');
    else dbms_output.put_line('其他');
    end if ;  
end ;
```

##### 循环

- while

```
while 条件
loop 
...
end loop ;
```

- do while

```
loop 
...
   exit when i>5 ;
end loop;
```

- for循环

```plsql
for  i in  1 .. 10
loop

..
end loop ;
--示例
set serveroutput on ;
declare	    
begin
  for x in 1 .. 5
    loop
            dbms_output.put_line(x);
    end loop ;
end ;

--求1-5之和
declare
	    pnum number:=1 ;
        psum number:= 0 ;
begin
        loop
            exit when pnum >5 ;
            psum := psum + pnum ; --sum+= i ;
            pnum := pnum +1 ;
        end loop ;
        dbms_output.put_line(psum);
end ;
```



##### 存储函数

- 语法

  存储函数:与存储过程的最大区别： 必须有return

  ```plsql
  create [or replace] function PRO_NAME -- 函数名(参数列表)
  	return --返回值类型 
  as
  
  begin 
  	..
  	return  --返回值
  end ;
  ```

- 示例

```plsql
--查询某个员工的年收入
create or replace function getTotalSal(pid in number) 
    return number 
as 
    empSal emp.sal%type ;
    empComm emp.comm%type ;
begin 
    select sal ,comm into empSal,empComm from emp where empno = pid ;
    dbms_output.put_line( empSal*12+  nvl( empComm,0));
    return empSal*12+  nvl( empComm,0) ;
end ;
```



##### 程序包

- 作用: 可以用来定义光标(sql返回值是一个几何,  用光标来进行接收)

- 创建包

![image-20201216134820933](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\创建包.png)

- 包体

![image-20201216135333754](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\包主体.png)

##### 触发器

- 理解为与表相关联的，PLSQL程序(事件????), 当执行DML时，自动执行触发器

- 语法

```plsql
create or replace trigger 触发器名
before|after
delete|insert |update [of 列名]
on 表
for each row [ when (条件)]
...plsql 代码
/
```

- 创建语句

```plsql
--修改某行触发
create or replace trigger logInsertStudent
after
insert on student
declare
--没有所以不写
begin
    DBMS_OUTPUT.PUT_LINE('增加成功');
end;
/

--具体修改某列触发
create or replace trigger logUpdateStudent
after 
update of sname
declare
begin
	DBMS_OUTPUT.PUT_LINE('修改成功');
end;
/
--修改到多行时, 触发器也只会触发一次. 即打印一次(默认为语句级触发器,修改为行级触发器的方法为:for each row [ when (条件)] )

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
--插入校验: 新增员工,但是必须在工作日, 工作时间内新增
create or replace trigger securityCheckStudent
before  insert 
on student
begin
        --校验  --不正常
        if to_char(sysdate,'day')  in ('星期六','星期日')  or to_number( to_char(sysdate,'hh24') ) not between 9 and 18
        then 
            --禁止插入，例外
            raise_application_error(-20001,'禁止非工作时间插入学生'  );
        end if ;
end ;
/
 
 
--使用触发器确保：涨工资，涨后的工资 不能少于涨前的工资
create or replace trigger checkSalary
before update 
on emp 
for each row 
begin   --new  old
    if :new.sal < :old.sal  --3000 - 5000 
    then 
    raise_application_error(-20002 , '涨后的工资不能小于涨前的！');
    end if ;
end ;
/
```

​		ps:  raise_application_error(-20002 , '涨后的工资不能小于涨前的！'); 错误编码范围为: 20000~20999

​		question:  那么, 这个数据校验可以做一些什么呢????

- 验证

![image-20201216143141667](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\触发器.png)

##### 数据字典

了解就行

​	即元数据,   数据库的各种描述信息，系统自带很多表 , 如dictionary、user_objects 、user_tables、user_tab_columns ;

数据字典的命名规范：

```
user：当前用户能够使用的
all：系统中全部的
dba:管理员
v$:性能相关

user_sequences ;
user_synonyms;
user_关键字s;
eg: user_tab_comments  查注释
all_tab_comments
eg:  user_triggers查看有哪些触发器
```

#### 其他

##### DBCA

- 打开

  ![image-20201216155357132](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca位置.png)

- 创建数据库用的最多

![image-20201216155009655](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\DBCA_创建新库.png)

- 下一步

  数据仓库：分析数据用的，只查询  不DML。

![image-20201216155552548](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca步骤2.png)

- 下一步

![image-20201216155902621](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca步骤3.png)

- 直接到第五步

![image-20201216161358703](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca步骤第五步.png)

- 第六步

![image-20201216161542601](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca步骤第六步.png)

- 第七步

![image-20201216161823409](G:\java\blog\zyaireleo.github.io\source\_posts\Oracle学习笔记\dbca第七步.png)

归档和备份有关

​	备份：
​		热备份 ：联机备份 ，必须要启用归档
​		冷备份 ： 脱机备份

- 第八步

##### 其他设置或参数、常用命令

- 打开执行时间

```plsql
set timing on/off
```

- 查看保留字:

```plsql
select *from v$reserved_words order by keyword asc ;
```

- 查看回收站

```plsql
show recyclebin
```

- 清空回收站

```plsql
purge recyclebin;
```

- 还原回收站

  闪回

- 修改默认的日期格式

```plsql
--默认 DD-MON-RR
alter session set NLS_DATE_FORMAT = 'yyyy-mm-dd' ;
alter session set NLS_DATE_FORMAT = 'DD-MON-RR' ;
```

- 查看当前系统编码格式

```plsql
select * from nls_database_parameters ;
```

- 查看用户下的表

```sql
--查看用户下的所有表
SELECT * FROM TAB;
--查看当前用户下的表
SELECT * FROM USER_TABLES;

```



##### 三大范式

1. 1NF:确保每列的原子性（不可再分）

2. 宏观：每张表只描述一件事情（例如，一个student表 描述的全部是学生字段）

   微观：通过2NF定义：除了主键以外的其他字段，都依赖于主键

3. 微观：除了主键以外的其他字段，都不传递依赖于主键

建议：三大范式 只是一个建议，不必严格遵守。实际使用时，需要“规范性”和“易用性、性能”间综合考虑

##### 序列

模拟自增，本质就是内存中的数组 

- 建立序列语句

```sql
create sequence 序列名
increment by 步长
start with 起始值
maxvalue | nomaxvalue
minvalue | nominvalue
cycle | nocycle
cache n | no cache ;

-- cycle是否循环  cache元素的个数 <= 循环元素个数
-- 循环序列 不能用于给 主键/唯一约束的键 赋值
--修改序列：只对修改之后的操作有效：alter sequence myseq increment by 2
```

- 查看序列

```
select *from user_sequences ;
```

- 序列有2个属性：

  nextval:下一个值

  currval:当前值

- 裂缝

```
 [1,2,3...,20]   [21,]
断电、异常、回滚、多表使用同一个序列 ……
```

##### 索引

- 建立索引

```sql
create index 索引名
-- create index myindex  on emp(deptno) ;
--主键默认 就是索引
```

- 什么时候 适合建立索引：

  - 数据集中的列,经常在where中使用的列， 数据量大
  - 数据集中的列：主键列(empno,id)不集中，但是因为 会被频繁使用 ，因此也适合建索引

- 删除索引
  drop index 索引名

  ```sql
  drop index 索引名
  drop index myindex ;
  ```

##### 同义词

数据库对象（表 视图 索引...）起别名 （默认私有/专用）

- 可能会遇到 查看其它用户的表，报错“表或视图不存在” 。是因为权限不足

  - grant xxx  to 用户名;

    ```sql
    
    grant select on hr.employees  to scott；--con / sysdaba 完成授权
    
    grant create synonym to scott ; -- 授于建别名的权限
    
    --revoke xxx from 用户名;回收权限
    ```

- 创建公有同义词

```sql
create public synonym  hremp2 for hr.employees ;
```

- 公有同义词的操作（创建、删除） 一般建议由管理员操作

```
drop [public ] sysnonym 同义词名
```

##### 视图

- 创建视图的语法：

  ```
  create  view 视图名
  as 
  select ...语句
  ```

- 优点

  - 简化查询

  ```sql
  --原始SQL:
  
  select d.deptno 部门编号,e.empno,e.ename,e.sal,e.comm ,d.dname from emp e ,dept d   where  e.deptno =d.deptno and  d.deptno=20;
  --用视图封装以上的查询结果：
  create  view  myempview
  as 
  	select d.deptno 部门编号,e.empno,e.ename,e.sal,e.comm ,d.dname from emp e ,dept d where e.deptno=d.deptno and  d.deptno=20; 
  --用视图简化查询：
  select * from myempview ;
  ```

  - b增加数据的安全性。可以将其他开发人 需要的某些字段封装到一个视图中交付

     DML  +Query  和表完全一致

- 注意：
  	某个用户在创建视图时，可能权限不足。需要授权。
    	如果在创建视图时，给某个字段起了别名，那么在视图中 就只能识别 该“别名”而不能识别原来真实的字段名。

- 通过sys 授予scott 创建视图的权限。
  	撤销revoke create view  from scott;
    	创建grant  xxxx to scott

- 尝试修改视图时：update myempview set ename = 'hello' where  empno=7934 ;

  报错:无法修改与非键值保存表对应的列

  本次的原因：在二表关联时，忘了编写 连接条件

  ```sql
  select d.deptno 部门编号,e.empno,e.ename,e.sal,e.comm ,d.dname from emp e ,dept d   where  【e.deptno =d.deptno】 and  d.deptno=20;
  ```

- drop  view myempview ;

- 视图选项

  ```sql
  create view 视图名
  as
  select 语句..
  with check option ;
   --with check option :限制对视图操作时，必须满足where子句
   --强烈建议使用：with read only ;
  --视图只建议查看，不建议DML。因为对视图的操作，会影响原表。
  
  create view myempview
  as
   select empno ,ename ,deptno from emp where deptno =20   with read only ;
  ```

- 如果非要对视图进行增删改，还需要遵循一些严格的苛刻条件。
  	当视图中存在以下之一时，不能Insert/update
    		group by 、distinct、组函数、列的定义为表达式

  ​	当视图中存在以下之一时， 不能delete

  ​	group by 、distinct、rownum伪劣

  -->视图只查看，不要DML

### 建表语句

