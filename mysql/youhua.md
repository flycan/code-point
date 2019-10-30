

显示执行的次数
mysql>show status like "com_insert%";
mysql>show status like "com_select%";
mysql>show status like "com_update%";
mysql>show status like "com_delete%";

显示影响的行数
mysql>show status like "innodb_rows%";

连接次数（失败也计算）
mysql>show status like "connections%";

服务器开启时间(毫秒)
mysql>show status like "uptime";


mysql>show status like "handlder_read%";


慢查询处理步骤
慢查询日志  
desc查看慢SQL语句
优化

索引 
where
having
order by
group by

where中有and和or必须都有索引才会使用索引

%放在前面用不上索引

字段类型不同会放弃使用索引

check table t1;
修改表名
rename table t2 to t1;

表优化(空闲时间使用)
optimize table t1;

数据导入导出
mysqldump -uroot -p test >/tmp/test.sql
mysql -uroot -p test </tmp/test.sql

select * from t1 into outfile "/tmp/test"

导入优化
DISABLE KEYS 和ENABLE KEYS 用来打开或关闭MyISAM表非唯
一索引的更新，可以提高速度，注意：对InnoDB表无效
alter table t1 disable keys;
alter table t1 enable keys;

 针对于InnoDB类型表数据导入的优化
 1.InnoDB表的按照主键顺序保存将导入的数据主键的顺序排列，可以有效地提高导入数据的效率
 2.关闭唯一性效验
 set unique_checks=0
 mysql> load data infile ‘/home/mysql/test.txt’into table test
 set unique_checks=1
 3.关闭自动提交
 set autocommit=0
  mysql> load data infile ‘/home/mysql/test.txt’into table test
 set autocommit=1


load data infile "/tmp/test" into table t1

















