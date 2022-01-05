
## Subject Introduction.

### 课程学习目标
- 对 Relational Database 关系型数据库有一个基本的认识.
- 能够写出基本的SQL查询语句 (SELECT FROM WHERE, ORDER BY)
- 理解如何使用DISTINCT子句
- 使用 equi-join 或 inner-join 语法，去联合表。

### 引入数据库 - 什么是关系型数据库.?
数据库一个有组织的机械装置，用于存储 管理 和取用信息。

#### 关系型数据库 的结构
- 数据库中所有的数据 以一个称为'表'的结构存储.
- 表是二维形式的。就像一个文字表。
- 数据库包含一系列装有数据的表

表的结构:
- 数据库内 表名唯一
- 表内 字段名唯一
- 每个表必须要一个主键
- ...

#### Nulls
当一个值不存在时，一个称为NULL的特殊值来了。

> 主键不可包含NULL

#### 创建 '报告(Reports)'
本学期我们将使用 Microsoft SQL Server

你能从关系型数据库中获取数据的唯一方式是 使用SQL  
 SQL, Structured Query Language.
通常读起来像 'SEQUEL'.

最热门的使用SQL的数据库
1. Oracle
2. MySQL
3. Microsoft SQL Server
4. PostgreSQL
5. IBM Db2
6. Redis
7. Microsoft Access
8. SQLite...



### SELECT 语句

使用SQL获取数据的基本方式是 使用SELECT语句.
```
SELECT <target_list>
FROM <table_list>
WHERE <search_condition>
GROUP BY <target_list>,
HAVING <search_condition>
ORDER BY <target_list>
```

