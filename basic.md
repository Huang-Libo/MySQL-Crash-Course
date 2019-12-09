# 基础


## 概念

**DBMS**：数据库管理系统，分为两大类：  

1. 基于共享文件系统的 DBMS，如：Access，FileMaker。  
2. 基于客户机-服务器的 DBMS，如：MySQL，Oracle，Microsoft SQL Server，SQLite。  

**SQL**：是 Structure Query Language（结构化查询语言）的缩写，发音为字母 S-Q-L 或 sequel。它是一种专门用来与数据库通信的语言。  

**主键（primary key）**：一列（或一组列），其值能够唯一区分表中的每一行。（主键的值不能为 NULL）  

**外键（foreign key）**：为表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。  

**模式（schema）**：关于数据库和表的布局及特性的信息。（有时候**模式**也用作**数据库**的同义词）  

**维护引用完整性**：通过指定**主键**和**外键**来实现。  

## 连接

> MySQL 的默认端口是 3306。如果使用了其他端口，需在连接的时候用 `-P` (大写的P) 来指定。  

使用自带的命令行工具：  

```
mysql -uroot -p
```

使用 `mycli`：  

```
mycli -h localhost -u root
```

## 基本操作

显示有哪些数据库：  

```sql
SHOW databases;
```

选择要使用的数据库：  

```sql
USE crash_course;
```

显示表的字段：  

```sql
SHOW COLUMNS FROM customers;
-- MySQL 支持用 DESCRIBE 作为 SHOW COLUMNS FROM 的一种快捷方式：
DESCRIBE customers;
```

查询结果为：  

```
+--------------+-----------+------+-----+---------+----------------+
| Field        | Type      | Null | Key | Default | Extra          |
+--------------+-----------+------+-----+---------+----------------+
| cust_id      | int(11)   | NO   | PRI | <null>  | auto_increment |
| cust_name    | char(50)  | NO   |     | <null>  |                |
| cust_address | char(50)  | YES  |     | <null>  |                |
| cust_city    | char(50)  | YES  |     | <null>  |                |
| cust_state   | char(5)   | YES  |     | <null>  |                |
| cust_zip     | char(10)  | YES  |     | <null>  |                |
| cust_country | char(50)  | YES  |     | <null>  |                |
| cust_contact | char(50)  | YES  |     | <null>  |                |
| cust_email   | char(255) | YES  |     | <null>  |                |
+--------------+-----------+------+-----+---------+----------------+
```

其他常用的 `SHOW` 语句：  

```sql
-- 显示广泛的服务器状态信息
SHOW STATUS;
-- 显示服务器的错误信息
SHOW ERRORS;
-- 显示服务器的警告信息
SHOW WARNINGS;
```

显示创建数据库时用的语句：  

```sql
SHOW CREATE DATABASE crash_course;
```

```
+--------------+----------------------------------------------------------------------------------------------------------------------------------------+
| Database     | Create Database                                                                                                                        |
+--------------+----------------------------------------------------------------------------------------------------------------------------------------+
| crash_course | CREATE DATABASE `crash_course` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci */ /*!80016 DEFAULT ENCRYPTION='N' */ |
+--------------+----------------------------------------------------------------------------------------------------------------------------------------+
```

显示创建表时用的语句：  

```sql
SHOW CREATE TABLE customers;
```

```
+-----------+------------------------------------------------------------------------------------------+
| Table     | Create Table                                                                             |
+-----------+------------------------------------------------------------------------------------------+
| customers | CREATE TABLE `customers` (                                                               |
|           |   `cust_id` int(11) NOT NULL AUTO_INCREMENT,                                             |
|           |   `cust_name` char(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL,        |
|           |   `cust_address` char(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL, |
|           |   `cust_city` char(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL,    |
|           |   `cust_state` char(5) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL,    |
|           |   `cust_zip` char(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL,     |
|           |   `cust_country` char(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL, |
|           |   `cust_contact` char(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL, |
|           |   `cust_email` char(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL,  |
|           |   PRIMARY KEY (`cust_id`)                                                                |
|           | ) ENGINE=InnoDB AUTO_INCREMENT=10006 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci  |
+-----------+------------------------------------------------------------------------------------------+
```

显示用户的权限：  

```sql
SHOW GRANTS;
```

```
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Grants for root@localhost                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, SHUTDOWN, PROCESS, FILE, REFERENCES, INDEX, ALTER, SHOW DATABASES, SUPER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER, CREATE TABLESPACE, CREATE ROLE, DROP ROLE ON *.* TO `root`@`localhost` WITH GRANT OPTION                                                                                       |
| GRANT APPLICATION_PASSWORD_ADMIN,AUDIT_ADMIN,BACKUP_ADMIN,BINLOG_ADMIN,BINLOG_ENCRYPTION_ADMIN,CLONE_ADMIN,CONNECTION_ADMIN,ENCRYPTION_KEY_ADMIN,GROUP_REPLICATION_ADMIN,INNODB_REDO_LOG_ARCHIVE,PERSIST_RO_VARIABLES_ADMIN,REPLICATION_SLAVE_ADMIN,RESOURCE_GROUP_ADMIN,RESOURCE_GROUP_USER,ROLE_ADMIN,SERVICE_CONNECTION_ADMIN,SESSION_VARIABLES_ADMIN,SET_USER_ID,SYSTEM_USER,SYSTEM_VARIABLES_ADMIN,TABLE_ENCRYPTION_ADMIN,XA_RECOVER_ADMIN ON *.* TO `root`@`localhost` WITH GRANT OPTION |
| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION                                                                                                                                                                                                                                                                                                                                                                                                                                   |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

## 查看帮助

```sql
help; -- 或使用 \？
```

```
+-------------+----------------------------+------------------------------------------------------------+
| Command     | Shortcut                   | Description                                                |
+-------------+----------------------------+------------------------------------------------------------+
| \G          | \G                         | Display current query results vertically.                  |
| \dt         | \dt[+] [table]             | List or describe tables.                                   |
| \e          | \e                         | Edit command with editor (uses $EDITOR).                   |
| \f          | \f [name [args..]]         | List or execute favorite queries.                          |
| \fd         | \fd [name]                 | Delete a favorite query.                                   |
| \fs         | \fs name query             | Save a favorite query.                                     |
| \l          | \l                         | List databases.                                            |
| \once       | \o [-o] filename           | Append next result to an output file (overwrite using -o). |
| \timing     | \t                         | Toggle timing of commands.                                 |
| connect     | \r                         | Reconnect to the database. Optional database argument.     |
| exit        | \q                         | Exit.                                                      |
| help        | \?                         | Show this help.                                            |
| nopager     | \n                         | Disable pager, print to stdout.                            |
| notee       | notee                      | Stop writing results to an output file.                    |
| pager       | \P [command]               | Set PAGER. Print the query results via PAGER.              |
| prompt      | \R                         | Change prompt format.                                      |
| quit        | \q                         | Quit.                                                      |
| rehash      | \#                         | Refresh auto-completions.                                  |
| source      | \. filename                | Execute commands from file.                                |
| status      | \s                         | Get status information from the server.                    |
| system      | system [command]           | Execute a system shell commmand.                           |
| tableformat | \T                         | Change the table format used to output results.            |
| tee         | tee [-o] filename          | Append all results to an output file (overwrite using -o). |
| use         | \u                         | Change to a new database.                                  |
| watch       | watch [seconds] [-c] query | Executes the query every [seconds] seconds (by default 5). |
+-------------+----------------------------+------------------------------------------------------------+
```

 

