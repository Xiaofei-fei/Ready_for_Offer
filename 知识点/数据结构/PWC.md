# SQL面试题

## 生成一个查询，员工ID，员工姓名，老板ID，老板姓名

## 分析：

对该表进行右外连接可以找出所有员工的老板，选出所有有老板的员工即可，若员工是最高级别老板，则不筛选。若要显示为空对table进行左外连接即可。



```sql
1.	CREATE TABLE emp(  
2.	    empId VARCHAR(20),  
3.	    empName VARCHAR(20),  
4.	    mgrId VARCHAR(20)  
5.	);  
6.	  
7.	INSERT INTO emp VALUES  
8.	(001, 'A', 002),  
9.	(002, 'B', 004),  
10.	(003, 'C', 004),  
11.	(004, 'D', 007),  
12.	(005, 'E', 006),  
13.	(006, 'F', 007),  
14.	(007, 'G', 007),  
15.	(008, 'H', 006);  
16.	  
17.	select * from emp  
18.	select e2.empId empId,e2.empName empName,e1.mgrId bossID,e1.empName bossName from emp e1 right join emp e2 on e1.empId = e2.mgrId; 
```

## 结果显示

### emp表

![](https://i.bmp.ovh/imgs/2022/05/15/dafbdc1351064b3f.png)

### 最终结果

![](https://i.bmp.ovh/imgs/2022/05/15/656325cd3d069755.png)

## 验证关系

![](https://i.bmp.ovh/imgs/2022/05/15/68b41ae4e3424e7a.png)