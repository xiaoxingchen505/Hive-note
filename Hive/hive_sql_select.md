
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
