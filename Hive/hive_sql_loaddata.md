

# Hive数据装载


## 1.使用存储在HDFS中的文件装载数据

Hive支持从hadoop分布式系统(HDFS)上传文件。这是将数据迁移到Hive生态系统最基本的方法。

```sql
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename
```

|  LOAD DATA   | 向Hive装载数据的关键字  |
|  ----  | ----  |
| LOCAL  | 如果包含该关键字，则支持用户从其本地文件装载数据 |
|        |  如果省略该关键字，则从Hadoop配置变量 fs.default.name中设定的路径加载文件 |
| INPATH 'filepath' | 如果使用LOCAL: file://user/hive.example |
|                   | 如果忽略LOCAL: hdfs://namenode:9000/user/hive/example |
| OVERWRITE | 如果包含，支持用户将数据装载到一个早已建好的表中并且替换原来的数据|
|           | 如果省略，支持用户将数据装载到一个早已建好的表中并且将新数据追加到原来的数据后面｜
| INTO TABLE tablename| tablename是hive中已经存在的表的名称，使用create TABLE tablename｜

实例：

## 创建一个新数据库
```sql
CREATE DATABASE census;
```

## 使用该数据库
```sql
use census;
```

## 创建一个新表
CREATE TABLE person {
    persid int,
    lastname string,
    firstname string
}
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';

## 将数据从csv 文件装载到该新表
```sql
LOAD DATA LOCAL INPATH 'file:///root/hive/example/person001' OVERWRITE INTO TABLE person.
```

## 最后查看表中是否有数据
```sql
SELECT persid, lastname, firstname
FROM person;
```


## 2.使用查询装载数据