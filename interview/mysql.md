
### 取3列最大的值

    select greatest(a, b ,c) from test

### 索引使用

```
CREATE TABLE `test` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `a` int(10) unsigned NOT NULL,
  `b` int(10) unsigned NOT NULL,
  `c` int(10) unsigned NOT NULL,
  `d` int(10) unsigned NOT NULL,
  PRIMARY KEY (`id`),
  KEY `idx_abc` (`a`,`b`,`c`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4

EXPLAIN select * from  test where id =1 and a = 1
EXPLAIN select * from  test where id =1 and a > 1

possible_keys: PRIMARY  key: PRIMARY key_len: 4

一个范围，一个精确
EXPLAIN select * from  test where id in (1,2) and a = 1
EXPLAIN select * from  test where id > 1 and a = 1
EXPLAIN select * from  test where id BETWEEN 1 and 2 and a = 1 

possible_keys: PRIMARY,idx_abc  key: idx_abc key_len: 4

两个都是范围，选择最优的
EXPLAIN select * from  test where id in (1,2) and a > 1
possible_keys: PRIMARY,idx_abc  key: PRIMARY key_len: 4

EXPLAIN select * from  test where id > 1 and a = 1 and b > 1
possible_keys: PRIMARY,idx_abc  key: idx_abc key_len: 4

// 复合索引，使用索引必须第一列用到索引
EXPLAIN select * from  test where a > 1 and b = 1
possible_keys: idx_abc  key:  key_len: 

```

### AUTO_INCREMENT

```
当前auto_increment=6，修改为2不会重置，依然为6
alter TABLE test auto_increment=2
select auto_increment from information_schema.tables where table_schema='test' and table_name='test';
```