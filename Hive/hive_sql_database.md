
# HIVE SQL 数据库常用语句

## 1.在hive中创建一个名为shopping的新命名空间。

```sql
CREATE DATABASE IF NOT EXISTS shopping
COMMENT ‘STORES ALL SHOPPING BASKET DATA’
LOCATION ‘/user/retail/hive/SHOPPING.db’
WITH DEPROPERTIES (‘purpose’= ‘testing’)
```

## 2.更改数据库
```sql
ALTER DATABASE shopping
SET DBPROPERTIES('department'='SALES')
```

## 3. 删除数据库
tips: CASCADE 的使用是可选的，允许你删除数据库时将已有的表一起删除，该命令将删除属于shopping数据库的所有内部表和外部表
DROP DATABASE 命令的默认行为是 RESTRICT， 如果数据库中有任何表，则该命令将失败。
```sql
DROP DATABASE shopping CASCADE (或者 RESTRICT)
```

## 4.列出数据库
```sql
SHOW DATABASES [LIKE 'identifier_with_wildcards']
```
