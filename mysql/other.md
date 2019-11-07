## mysql连接1251的问题

1521-Client does not support authentication  protocol requested by server；

连接的加密方式已经改变

```mysql
> mysql -u root -p
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'newpassword';
> flush privileges;

## mysql分页优化

```
DROP TABLE IF EXISTS `page_test`;
CREATE TABLE `page_test` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL DEFAULT '',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=UTF8;


create procedure p1(in n int)
begin
    declare num int default 0;
    declare name varchar(20) default "";
    while num < n do
		set num := num + 1;
        set name := concat("name-", cast(num as char));
        insert into page_test(`name`) value (name);
    end while;
end;

mysql> call p1(30000000);
```

1.业务优化

限制分页数，最多100页

2.使用软删除

第10000页直接查 10w+1到10w+10的数据

select * from page_test where id > 10w limit 10;

3.硬删除

select c.id,c.name from page_test as c inner join (select id from page_test limit 10w, 10) as t on c.id = t.id;

使用in查询慢很多
select id ,name from page_test where id in (select id from (select id from page_test limit 50000, 10) as t1)


## sql分析

mysql> set profiling=on;
mysql> show profiles;
mysql> show profile for query 4;

mysql> explain select * from test  where id = 1;

## 主从复制加速

sync_binlog = 0

    sync_binlog=0，表示MySQL不控制binlog的刷新，由文件系统自己控制它的缓存的刷新 性能是最好的 风险也是最大
    sync_binlog>0，表示每sync_binlog次事务提交，MySQL调用文件系统的刷新操作将缓存刷下去 最安全但是性能损耗最大

innodb_flush_log_at_trx_commit = 2

    设成1 的意思是每一次事务提交或事务外的指令都需要把日志写入（flush）硬盘
    设成2 不写入硬盘而是写入系统缓存 只会在整个操作系统 挂了时才可能丢数据
    设成0 会更快一点，但安全方面比较差 MySQL挂了也可能会丢失事务的数据。

innodb_flushlog 