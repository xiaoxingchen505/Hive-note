

# Hive SQL 连接


## 1. 使用等值连接来整合表
Hive支持表之间的等值连接，使你能够整个来自两个表的数据

Hive语法如下:
```sql
SELECT table_fields
FROM table_one
JOIN table_two
ON (table_one.key_one = table_two.key_one AND table_one.key_two = table_two.key_two);
```

## 2. 使用外连接

Hive 支持采用LEFT,RIGHT和FULL OUTER等连接方式实现表之间的等值连接，这其中无匹配的键。

Hive语法如下:
```sql
SELECT table_fields
FROM table_one
[LEFT,RIGHT,FULL OUTER] JOIN table_two
ON (table_one.key_one = table_two.key_one AND table_one.key_two = table_two.key_two);
```


### 使用左连接方式连接Hive中的表
```sql
SELECT personname.firstname,personname.lastname,address,postname
FROM census.personname
LEFT JOIN census.address
ON (personname.persid = address.persid);
```

### 使用右连接方式连接Hive中的表


```sql
SELECT personname.firstname,personname.lastname,address,postname
FROM census.personname
RIGHT JOIN census.address
ON (personname.persid = address.persid);
```


### 使用完全外连接的方式连接hive中的表

```sql
SELECT personname.firstname,personname.lastname,address,postname
FROM census.personname
FULL OUTER JOIN census.address
ON (personname.persid = address.persid);
```


## 3.使用左半连接
```sql
SELECT table_fields FROM table_one
LEFT SEMI JOIN table_two
ON (table_one.key_one = table_two.key_one);
```

### 执行半连接

Hive支持表之间的半连接操作，使你能够对两个表中的数据进行整合

```sql
USE census;

SELECT personname.firstname,personname.lastname FROM census.personname
LEFT SEMI JOIN census.address ON (personname.persid = address.persid)
```

## 4. 用单次MapReduce实现连接

如果在连接链中使用了公共键，Hive还支持通过一次MapReduce来连接多个表
```sql
SELECT table_one.key_one, table_two.key_one, table_three.key_one
FROM table_one JOIN table_two
ON (table_one.key_one = table_two.key_one)
JOIN table_three
ON (table_three.key_one = table_two.key_one);
```


## 5. 最后使用最大的表
Hive在实施连接时可先缓存前几个要连接的表，然后再针对他们映射最后一个表。总是将最大的表放在后面是一种比较好的实践方法，因为这样做会加速处理过程。

```sql
SELECT table_one.key_one, table_two.key_one, table_three.key_one
FROM table_one JOIN table_two
ON (table_one.key_one = table_two.key_one)
JOIN table_three
ON (table_three.key_one = table_two.key_one);

```

```sql
SELECT table_one.key_one, table_two.key_one, table_three.key_one
FROM table_one JOIN table_three
ON (table_one.key_one = table_three.key_one)
JOIN table_three
ON (table_two.key_one = table_three.key_one);

```