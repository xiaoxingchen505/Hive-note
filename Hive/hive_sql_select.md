 
# HIVE SQL 查询语句

hive分区对于非常特别的查询子集而言可以改进其性能，可以对不需要检索查询结果的分区进行剪枝。这个过程叫做分区消除。分区也是一种支持用户在hdfs上以更加分片化的方式组织数据的方式，这样可以改进可维护性。

## 1.对日期列进行高效分区
对日期列进行高效分区

### 查询选择特定日期
```sql
select * FROM TableA WHERE DateStamp IN ('2015-01-01','2015-02-03','2016-01-01');
```

### 查询一年中所有的日期
```sql
select * FROM TableA WHERE DateStamp LIKE '2015-%';
```

### 查询一年中特定月的所有日期
```sql
select * FROM TableA WHERE DateStamp LIKE '2015-02-%';
```

### 查询所有以5开始/结束的日子
```sql
select * FROM TableA WHERE Datestamp LIKE '%-%-%5';
```

### 查询2015年1月1日到2015年3月1日的所有日子
```sql
select * FROM TableA WHERE Datestamp LIKE '2015-01-01' AND '2015-03-01';
```

## 2.使用UDF查询json

你可以使用内置的UDF(用户定义函数)，也可以使用内置的或可公开获得的JSON SerDe. 你所选定的JSON访问方法决定了存储数据的方式和应用于数据的模式。
第一步式创建表来存储json函数。此表仅有一个字符串型的列组成，它表示整个JSON数据。我们创建了一个名为Json_data的数据库。

```sql
CREATE TABLE json_table(
    json string
);
```
下一步是将json数据装载到这个表中，并且将所有数据存储到一个字符串型的列中。

```sql
LOAD DATA INPATH '/tmp/json_data/json1' INTO TABLE json_table;
```

### 查询所有数据
```sql
select get_json_object(json_table.json,'$') from json_table;
```
### 查询特定数据
```sql
select get_json_object(json_table.json,'$.balance') as balance,
    get_json_object(json_table.json,'$.gender') as gender,
    get_json_object(json_table.json,'$.phone') as phone,
    get_json_object(json_table.json,'$.friends.name') as friendname,
from json_udf;
```



## 3.条件查询,分组，排序

可以参考一下这篇博文[转载](https://www.cnblogs.com/duoduotouhenying/p/10145478.html#_label1_0)