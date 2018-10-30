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


