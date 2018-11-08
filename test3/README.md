# 实验三：创建分区表
## 实验内容：
* 创建orders表：
代码：
```
CREATE TABLE ORDERS
(
order_id NUMBER(10,0)NOT NULL,
customer_name VARCHAR2(40 BYTE)NOT NULL,
customer_tel VARCHAR2(40 BYTE)NOT NULL,
order_date DATE NOT NULL,
employee_id NUMBER(6,0) NOT NULL,
discount NUMBER(8,2)DEFAULT 0,
trade_receivable NUMBER(8,2)DEFAULT 0
)
TABLESPACE USERS
PCTFREE 10
INITRANS 1
STORAGE
(
BUFFER_POOL DEFAULT
)
PARTITION BY RANGE (order_date)  //RANGE分区类型
(
PARTITION partition_before_2017 VALUES LESS THAN (
TO_DATE(' 2017-01-01 00: 00: 00', 'SYYYY-MM-DD HH24: MI: SS',
'NLS_CALENDAR=GREGORIAN'))TABLESPACE USERS,

PARTITION partition_before_2018 VALUES LESS THAN (
TO_DATE(' 2018-01-01 00: 00: 00', 'SYYYY-MM-DD HH24: MI: SS',
'NLS_CALENDAR=GREGORIAN'))TABLESPACE USERS02,

PARTITION partition_before_2019 VALUES LESS THAN (
TO_DATE(' 2019-01-01 00: 00: 00', 'SYYYY-MM-DD HH24: MI: SS',
'NLS_CALENDAR=GREGORIAN'))TABLESPACE USERS03
);
```
截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-1.png)
* 创建从表：
```
CREATE TABLE order_details
(
id NUMBER(10,0)NOT NULL,
order_id NUMBER(10,0)NOT NULL,
product_id VARCHAR2(40 BYTE)NOT NULL,
product_num NUMBER(8,2) NOT NULL,
product_price NUMBER(8,2) NOT NULL,
CONSTRAINT order_details_fk1 FOREIGN KEY (order_id)
REFERENCES orders ( order_id )
ENABLE
)
TABLESPACE USERS
PCTFREE 10 
INITRANS 1
STORAGE( BUFFER_POOL DEFAULT )
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1);
```
截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-2.png)
* 插入单条数据
```
INSERT INTO orders(customer_name, customer_tel, order_date, employee_id, trade_receivable, discount) VALUES('WANG', '152', to_date ( '2016-12-20 18:31:34' , 'YYYY-MM-DD HH24:MI:SS' ), 001, 16, 6);
```
* 插入多条数据：
```
insert into orders
select *
from orders;
截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-3.png)
```
* 授权用户：
```
grant SELECT_CATALOG_ROLE to NEW_USER_lin
grant SELECT ANY DICTIONARY toNEW_USER_lin
```
截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-6.png)
* 分区查询：
```
SELECT
    orders.order_id,
    orders.order_date,
    order_details.order_id,
    TRADE_RECEIVABLE,
    PRODUCT_ID,
    PRODUCT_PRICE
FROM orders partition (PARTITION_BEFORE_2017) LEFT JOIN order_details partition (PARTITION_BEFORE_2017)
ON (orders.order_id = order_details.order_id);
```
截图：
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-7.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-9-1.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-9-2.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/%E5%AE%9E%E9%AA%8C3-9-3.png)
* 不分区查询：
```
set autotrace on;
select * from orders_nopartition, order_details_nopartition where orders_nopartition.order_id = order_details_nopartition.order_id(+);
```
表空间：是一个或多个数据文件的集合，所有的数据对象都存放在指定的表空间中，但主要存放的是表， 所以称作表空间。
分区表：当表中的数据量不断增大，查询数据的速度就会变慢，应用程序的性能就会下降，这时就应该考虑对表进行分区。表进行分区后，逻辑上表仍然是一张完整的表，只是将表中的数据在物理上存放到多个表空间(物理文件上)，这样查询数据时，不至于每次都扫描整张表。

表分区的具体作用
Oracle的表分区功能通过改善可管理性、性能和可用性，从而为各式应用程序带来了极大的好处。通常，分区可以使某些查询以及维护操作的性能大大提高。此外,分区还可以极大简化常见的管理任务，分区是构建千兆字节数据系统或超高可用性系统的关键工具。

分区功能能够将表、索引或索引组织表进一步细分为段，这些数据库对象的段叫做分区。每个分区有自己的名称，还可以选择自己的存储特性。从数据库管理员的角度来看，一个分区后的对象具有多个段，这些段既可进行集体管理，也可单独管理，这就使数据库管理员在管理分区后的对象时有相当大的灵活性。但是，从应用程序的角度来看，分区后的表与非分区表完全相同，使用 SQL DML 命令访问分区后的表时，无需任何修改。
