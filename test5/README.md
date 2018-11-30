# 实验五————PL/SQL编程
## 实验步骤:
* 1、创建一个包(Package)，包名是MyPack。
#### 截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE实验五-1.png)
* 2、在MyPack中创建一个函数SaleAmount ，查询部门表，统计每个部门的销售总金额，每个部门的销售额是由该部门的员工(ORDERS.EMPLOYEE_ID)完成的销售额之和。函数SaleAmount要求输入的参数是部门号，输出部门的销售金额。
#### 截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE实验五-2.png)
* 3、在MyPack中创建一个过程，在过程中使用游标，递归查询某个员工及其所有下属，子下属员工。过程的输入参数是员工号，输出员工的ID,姓名，销售总金额。信息用dbms_output包中的put或者put_line函数。输出的员工信息用左添加空格的多少表示员工的层次（LEVEL）。比如下面显示5个员工的信息。
#### 截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE实验五-3.png)


