#     实验二————用户权限管理

##    在pdborcl插接式数据库中创建一个新的本地角色con_res_view_lin，该角色包含connect和resource角色，同事也包含CREATE和VIEW权限。
###    步骤一：
 以system登录到pdborcl，创建角色con_res_view_lin和用户new_user_lin，并授权和分配空间：
* 代码：
```
$ sqlplus system/123@pdborcl
SQL> CREATE ROLE con_res_view;
Role created.
SQL> GRANT connect,resource,CREATE VIEW TO con_res_view;
Grant succeeded.
SQL> CREATE USER new_user IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
User created.
SQL> ALTER USER new_user QUOTA 50M ON users;
User altered.
SQL> GRANT con_res_view TO new_user;
Grant succeeded.
SQL> exit
```
* 运行结果：

![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-1.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-2.png)

### 步骤二：
新用户new_user_lin连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
* 代码：
```
$ sqlplus new_user/123@pdborcl
SQL> show user;
USER is "NEW_USER"
SQL> CREATE TABLE mytable (id number,name varchar(50));
Table created.
SQL> INSERT INTO mytable(id,name)VALUES(1,'zhang');
1 row created.
SQL> INSERT INTO mytable(id,name)VALUES (2,'wang');
1 row created.
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
View created.
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------
zhang
wang
SQL> GRANT SELECT ON myview TO hr;
Grant succeeded.
SQL>exit
```
* 运行结果:

![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-3.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-4.png)
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-5.png)


### 步骤三：
用户hr连接到pdborcl，查询new_user授予它的视图myview
* 代码：
```
$ sqlplus hr/123@pdborcl
SQL> SELECT * FROM new_user.myview;
NAME
--------------------------------------------------
zhang
wang
SQL> exit
```
* 运行结果：
![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-6.png)
### 步骤四：
查看数据库的使用情况：
* 代码：
```
SQL>SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS';

SQL>SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
 free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
 Round(( total - free )/ total,4)* 100 "使用率%"
 from (SELECT tablespace_name,Sum(bytes)free
        FROM   dba_free_space group  BY tablespace_name)a,
       (SELECT tablespace_name,Sum(bytes)total FROM dba_data_files
        group  BY tablespace_name)b
 where  a.tablespace_name = b.tablespace_name;
```
* 运行结果：

 ![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-7.png)
 ![](https://github.com/2016LMS/Oracle/blob/master/picture/ORACLE%E5%AE%9E%E9%AA%8C%E4%BA%8C-8.png)
