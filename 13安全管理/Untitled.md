#### 1. 安全控制概述 

三个认证过程
第一个是验证用户连接到SQL Server数据库服务器的资格。（**合法的登录帐号**）
第二个是验证用户具备访问某个数据库的资格。（**数据库的合法用户**）
第三个是验证用户是否具有该数据库一些**相应的操作权限**。 

#### 2. 登录名

SQL Server 2012的安全权限是基于标识用户身份的登录标识符(Login ID，登录ID)，登录ID就是控制访问SQL Server 数据库服务器的登录名。

* 由SQL Server负责验证的登录名 --  混合验证模式
* Windows网络账户，可以是组用户  --  Windows身份验证模式

```sql
CREATE LOGIN login_name
   { WITH <option_list1> | FROM <sources> }
<sources> ::=
   WINDOWS [WITH <windows_options> [,…] ]
<option_list1> ::= 
   PASSWORD = 'password' 
[ , <option_list2> [ ,... ] ]
<option_list2> ::=  
    SID = sid
    | DEFAULT_DATABASE = database 
    | DEFAULT_LANGUAGE = language
<windows_options> ::= 
    DEFAULT_DATABASE = database
    | DEFAULT_LANGUAGE = language
```

删除登录名

```sql
DROP LOGIN login_name
```

不能删除正在使用的登录帐户，也不能删除拥有任何数据库对象、服务器级别对象的登录帐户。

#### 3. 数据库用户

建立Windows用户

建立sqlserver验证

```sql
CREATE USER user_name [ { { FOR | FROM }
{ LOGIN login_name  } ]
```

user_name：指定在此数据库中用于识别该用户的名称。
LOGIN login_name：指定要映射为数据库用户的SQL Server登录名。
如果省略FOR LOGIN，则新的数据库用户将被映射到同名的SQL Server登录名。

删除

```sql
DROP USER user_name
```



#### 4. 权限管理

###### 对象权限（DML数据操纵功能）

是指用户对数据库中的表、视图等对象中数据的增加、删除、修改等各种操作权，可细分到列。

###### 语句权限（DDL数据定义功能）

相当于数据定义语言（DDL）的语句权限，这种权限专指是否允许执行：CREATE TABLE、CREATE VIEW等与创建数据库对象有关的操作。

###### 隐含权限（内置权限）

指由SQL Server预定义的服务器角色和数据库角色所具有的权限，这些权限内置于上述角色，无需明确授予。 

* **GRANT：**授予(√) ：允许用户或角色具有某种操作权
* **REVOKE：**收回(□) ：不允许用户或角色具有某种操作权，或者收回曾经授予的权限。用户或角色仍可以通过继承而获得相应的权限。
* **DENY：**拒绝(×) ：拒绝某用户或角色具有某种操作权，即使该用户或角色由于继承而获得这种操作权，也不允许执行相应的操作（sysadmin 角色除外）。

##### 管理语句权限

###### 授权语句

```sql
GRANT  语句权限名 [ ， … ]  
TO {数据库用户名 | 用户角色名} [ ， … ]


GRANT CREATE TABLE TO user1
GRANT CREATE TABLE, CREATE VIEW TO user1, user2
```

###### 收权语句

```sql
REVOKE 语句权限名 [ ， … ]  
FROM { 数据库用户名 | 用户角色名 } [ ， … ]

REVOKE CREATE TABLE FROM user1
```

###### 拒绝语句

```sql
DENY  语句权限名 [ ， … ]  
TO  {数据库用户名 | 用户角色名} [ ， … ]

DENY CREATE VIEW TO user1
```

##### 管理对象权限

###### 授权语句

```sql
GRANT 对象权限名 [ ， … ]
ON {表名 | 视图名 | 存储过程名}
TO { 数据库用户名 | 用户角色名 } [ ， … ]

GRANT SELECT ON Student TO user1
GRANT SELECT, UPDATE ON SC TO user1
```

###### 收权语句

```sql
REVOKE 对象权限名 [ ， … ]  
 ON { 表名 | 视图名 | 存储过程名 }
 FROM { 数据库用户名 | 用户角色名 } [ ， … ]

REVOKE SELECT ON Student FROM user1
```

###### 拒绝语句

```sql
DENY 对象权限名 [ ， … ] 
ON {表名 | 视图名 | 存储过程名}
TO { 数据库用户名 | 用户角色名 } [ ， … ]

DENY UPDATE ON SC TO user1 
```



#### 5. 角色