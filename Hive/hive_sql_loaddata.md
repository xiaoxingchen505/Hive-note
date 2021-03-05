

# Hive 数据装载


## 1. 使用存储在HDFS中的文件装载数据

Hive支持从hadoop分布式系统(HDFS)上传文件。这是将数据迁移到Hive生态系统最基本的方法。

```sql
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename
```

|  LOAD DATA   | 向Hive装载数据的关键字  |
|----------|------------|
| LOCAL  | 如果包含该关键字，则支持用户从其本地文件装载数据,<br>如果省略该关键字，则从Hadoop配置变量 fs.default.name中设定的路径加载文件 |
| INPATH 'filepath' | 如果使用LOCAL: file://user/hive.example ,<br>如果忽略LOCAL: hdfs://namenode:9000/user/hive/example |
| OVERWRITE | 如果包含，支持用户将数据装载到一个早已建好的表中并且替换原来的数据,<br>如果省略，支持用户将数据装载到一个早已建好的表中并且将新数据追加到原来的数据后面｜
| INTO TABLE tablename| tablename是hive中已经存在的表的名称，使用create TABLE tablename｜

实例：

### 创建一个新数据库
```sql
CREATE DATABASE census;
```

### 使用该数据库
```sql
USE census;
```

### 创建一个新表
CREATE TABLE person {
    persid int,
    lastname string,
    firstname string
}
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';

### 将数据从csv 文件装载到该新表
```sql
LOAD DATA LOCAL INPATH 'file:///root/hive/example/person001' OVERWRITE INTO TABLE person.
```

### 最后查看表中是否有数据
```sql
SELECT persid, lastname, firstname
FROM person;
```


## 2. 使用查询装载数据
Hive支持将已有表查询到的数据装载到Hive生态系统中。

Hive语法如下
```sql
INSERT [OVERWRITE]
TABLE tablename [IF NOT EXISTS]
SELECT select_fields FROM from_statement;
```

|  INSERT   | 用于将数据装载到Hive表中的关键字 |
|----------|------------|
| OVERWRITE  | 如果包含该关键字，支持用户将数据装载到已经建好的表中，并且替换之前的数据 ,<br> 如果省略，支持用户将数据装载到已经建好的表中，并且新数据追加到以前的数据之后 |
| TABLE tablename | tablename 是Hive已有的表名。 使用CREATE TABLE tablename语句 |
| IF NOT EXISTS | 如果在命令中包含了 IF NOT EXISTS，那么Hive命令将在当前数据库中创建一个表,<br>如果省略，当该表不存在时将执行失败|
| SELECT | 这可以是针对Hive生态系统的任何Select命令｜
| select_fields|
| FROM | 
| from_statement| 


### 使用已有表创建新表

使用已有数据库
```sql
USE census;
```
创建新表
```sql
CREATE TABLE personhub(
    persid int
);
```
将数据插入新表，覆盖表中已有数据
```sql
INSERT OVERWRITE
TABLE personhub
SELECT DISTINCT personId FROM Person
```
检查数据是否已在表中

```sql
SELECT persid FROM personhub;
```

## 3. 将查询到的数据写入文件系统

Hive支持将查询到的数据装载到Hadoop分布式文件系统中。
Hive语法如下
```sql
INSERT [OVERWRITE]
DIRECTORY directoryname
SELECT select_fields FROM from_statement;
```
|  INSERT   | 用于将数据装载到Hive表中的关键字 |
|----------|------------|
| OVERWRITE  | 如果包含该关键字，支持用户将数据装载到已经建好的目录中，并且替换之前的数据 , <br> 如果省略，支持用户将数据装载到已经建好的目录中，并且新数据追加到之前的数据后面 |
| DIRECTORY directoryname | directoryname是Hadoop分布式文件系统中已有的目录名称, <br>这可以是任何针对Hive生态系统的SELECT命令 |
| SELECT | 这可以是针对Hive生态系统的任何Select命令｜
| select_fields|
| FROM | 
| from_statement| 

### 使用已有表创建输出目录
```
hadoop fs -mkdir 'exampleoutput'
```

```
USE census;
INSERT OVERWRITE DIRECTORY 'exampleoutput'
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
SELECT persid, firstname,lastname
FROM person;

exit;
```

测试一下是否所有数据都已经装载
```
hadoop fs -cat 'exampleoutput/000000_0'
```

## 4. 直接向表插入值
Hive支持一系列静态值直接将数据装载到表中

语法如下:
```sql
INSERT 
INTO TABLE tablename
VALUES (row_values1),(row_value2);
```

|  INSERT   | 用于将数据装载到Hive表中的关键字 |
|----------|------------|
| TABLE tablename | tablename是Hive中已有表的名称，使用CREATE TABLE tablename语句 |
| VALUES (row_values1),(row_value2) | 值row_values1 和row_values2是相同格式的单条记录，而不是表的记录 |

## 5. 直接更新表中数据
Hive支持直接将数据更新到表中
Hive语法如下：
```sql
UPDATE tablename
SET column = value
[WHERE expression];
```

|  INSERT   | 用于更新表中值的关键字 |
|----------|------------|
| tablename | tablename是Hive中已有表的名称，使用CREATE TABLE tablename语句 |
| SET column = value | SET命令更新该列的一个值 |
| [WHERE expression] | WHERE可用于为不同的查询挑选特定列的值 |

## 6. 在表中直接删除数据
Hive支持直接在表中删除数据
Hive语法如下：
```sql
DELETE tablename
[WHERE expression];
```

|  DELETE   | 用于删除表中值的关键字 |
|----------|------------|
| tablename | tablename是Hive中已有表的名称，使用CREATE TABLE tablename语句 |
| [WHERE expression] | WHERE可用于为不同的查询挑选特定列的值 |


## 7. 创建结构相同的表
Hive支持基于一个已有表的结构创建一个新表
Hive语法如下：
```sql
CREATE TABLE blank_tablename
LIKE tablename;
```

|  CREATE TABLE   | 创建表的关键字 |
|----------|------------|
|  blank_tablename   |  待创建表的名称 |
| tablename | 该表名是Hive中已有表的名称，使用CREATE TABLE tablename语句 |
| LIKE | 该表名是Hive中已有表的名称。使用CREATE TABLE tablename语句 |