
# HIVE SQL 表常用语句


## 1.创建表
假设retail是一个数据库
customers位置也可以写retail.customers，这样代表在retail数据库中创建一个表，如果不加，就代表在default数据库中创建一个表。

```sql
CREATE EXTERNAL TABLE customers(
    fname   STRING,
    lname   STRING,
    address STRUCT <HOUSENO:STRING, STREET:SPRING, CITY:SPRING, ZIPCODE:INT, STATE:SPRING, COUNTRY: SPRING>,
    active BOOLEAN,
    created DATE
LOCATION '/user/demo/customers');
```
```sql
CREATE EXTERNAL TABLE customers(
    fname   STRING,
    lname   STRING,
    address STRUCT <HOUSENO:STRING, STREET:SPRING, CITY:SPRING, ZIPCODE:INT, STATE:SPRING, COUNTRY: SPRING>,
    active BOOLEAN,
    created DATE)
LOCATION '/user/demo/customers';
```

### CTAS命令
```sql
CREATE TABLE AS SELECT 
```
例如： CREATE TABLE AS SELECT * FROM retail.transaction WHERE custid < 101;

### CREATE TABLE LIKE 命令
```sql
CREATE TABLE transactions_test LIKE transactions;
```


## 2.列出表

```sql
SHOW TABLES IN retail;
```

## 3.内部表/外部表
hive表可以创建为内部或者外部，hive表的类型决定了hive如何加载，存储和控制数据。

通过在CREATE TABLE语句中使用EXTERNAL关键字可以创建外部表。这是hadoop所有生产部署中推荐的表类型。这是因为在大多数情况下，底层数据将被用于多个用例。即使并非如此，也不应该在删除表定义时删除底层数据。因此，对于外部表来说，hive不会将数据从文件系统中删除，因为它无法控制这些数据。


## 4.更改表
该命令和标准sql中的ALTER TABLE命令相似，只是在hive中稍有不同。ALTER TABLE中的所有选项都支持你修改表的结构，但是它不能随便修改数据。

### 重命名表,把states表重命名为states_old。

```sql
ALTER TABLE states RENAME TO states_old;
```
打印出来看下

```sql
DESCRIBE FORMATTED states_old;
```

### 删除表
可以使用DROP TABLE命令删除hive中的表。运行DROP TABLE命令时，表的元数据总会被删除。然而，hive仅仅删除受控表中的数据。该表的数据文件就会被移动到/user/$user/.trash文件夹下。可以通过在/etc/hadoop/conf/core-site.xml中设置fs.trash.interval参数来使该特性可用。

```sql
DROP TABLE <TABLE_NAME>;
```

如果你还想从trash中删除它，那么可以假如PURGE关键字，如下所示。

```sql
DROP TABLE <TABLE_NAME> PURGE;
```