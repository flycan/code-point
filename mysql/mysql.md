**SQL 分类：**

**SQL 语句主要可以划分为以下 4 个类别。**

**DDL（Data Definition Languages）语句：**数据定义语言，这些语句定义了不同的数据段、数据库、表、列、索引等数据库对象的定义。常用的语句关键字主要包括 create、drop、alter等。

```sql
CREATE <![TEMPORARY|TEMP]> TABLE [IF NOT EXISTS] tbl_name (
        column type
        [ NULL | NOT NULL ] [ UNIQUE ] [ DEFAULT value ]
        [column_constraint_clause | PRIMARY KEY } [ ... ] ]
        [, PRIMARY KEY ( column [, ...] ) ]
        [, CHECK ( condition ) ]
        [, table constraint ]
      )

DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]

ALTER TABLE table [ * ]
        ADD [<!COLUMN>] column type
ALTER TABLE table [ * ]
        DROP [ COLUMN ] column
ALTER TABLE table [ * ]
        MODIFY [<!COLUMN>] column { <!SET> DEFAULT value | DROP DEFAULT }
ALTER TABLE table [ * ]
        MODIFY [<!COLUMN>] column column_constraint
ALTER TABLE table [ * ]
        RENAME [<!COLUMN>] column TO newcolumn
ALTER TABLE table
        RENAME TO newtable
ALTER TABLE table
        ADD table_constraint
          ALTER INDEX index_name {VISIBLE | INVISIBLE}
```

**DQL（Data Query Language SELECT ）数据查询语言，select语句。**

```sql
SELECT
    [ALL | DISTINCT | DISTINCTROW ]
      [HIGH_PRIORITY]
      [STRAIGHT_JOIN]
      [SQL_SMALL_RESULT] [SQL_BIG_RESULT] [SQL_BUFFER_RESULT]
      [SQL_CACHE | SQL_NO_CACHE] [SQL_CALC_FOUND_ROWS]
    select_expr [, select_expr ...]
    [FROM table_references
      [PARTITION partition_list]
    [WHERE where_condition]
    [GROUP BY {col_name | expr | position}
      [ASC | DESC], ... [WITH ROLLUP]]
    [HAVING where_condition]
    [WINDOW window_name AS (window_spec)
        [, window_name AS (window_spec)] ...]
    [ORDER BY {col_name | expr | position}
      [ASC | DESC], ...]
    [LIMIT {[offset,] row_count | row_count OFFSET offset}]
    [INTO OUTFILE 'file_name'
        [CHARACTER SET charset_name]
        export_options
      | INTO DUMPFILE 'file_name'
      | INTO var_name [, var_name]]
    [FOR UPDATE | LOCK IN SHARE MODE]]
    [FOR {UPDATE | SHARE} [OF tbl_name [, tbl_name] ...] [NOWAIT | SKIP LOCKED] 
      | LOCK IN SHARE MODE]]
```

**DML（Data Manipulation Language）语句：**数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据完整性，常用的语句关键字主要包括 insert、delete、udpate 。(增添改）

```sql
INSERT INTO tbl_name [ ( column [, ...] ) ]
        { VALUES ( expression [, ...] ) | SELECT query }
//demo
INSERT INTO tbl_name (col1,col2) VALUES(15,col1*2);
```

```sql
DELETE FROM table 
  [ WHERE condition ] 
  [ORDER BY ...]
  [LIMIT row_count]
```

```sql
UPDATE table SET col = expression [,...]
    [ FROM fromlist ]
    [ WHERE condition ]
    [ORDER BY ...]
    [LIMIT row_count]
```

**DCL（Data Control Language）语句：**数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括 grant、revoke 等。

```sql
GRANT
    priv_type [(column_list)]
      [, priv_type [(column_list)]] ...
    ON [object_type] priv_level
    TO user_or_role [, user_or_role] ...
    [WITH GRANT OPTION]

GRANT PROXY ON user_or_role
    TO user_or_role [, user_or_role] ...
    [WITH GRANT OPTION]

GRANT role [, role] ...
    TO user_or_role [, user_or_role] ...
    [WITH ADMIN OPTION]

object_type: {
    TABLE
  | FUNCTION
  | PROCEDURE
}

priv_level: {

  | db_name.*
  | db_name.tbl_name
  | tbl_name
  | db_name.routine_name
}

user_or_role: {
    user
  | role
}
GRANT ALL ON db1.* TO 'jeffrey'@'localhost';
```

### mysql三范式

### 第一范式：属性原子性不可分割

第一范式是最基本的范式。如果数据库表中的所有字段值都是不可分解的原子值。

比如 在一个用户表 存在一个字段班级叫3年级2班。这个属性就不符合第一范式。这个字段可以分成3年级、2班

### 第二范式：非主键字段和主键直接关联

第二范式在第一范式的基础之上更进一层。第二范式需要确保数据库表中的每一列都和主键相关，而不能只与主键的某一部分相关（主要针对联合主键而言）。也就是说在一个数据库表中，一个表中只能保存一种数据，不可以把多种数据保存在同一张数据库表中。

比如订单表

![](https://pic002.cnblogs.com/images/2012/270324/2012040114063976.png)

在这个表中，存在订单信息和用户信息，和订单不是直接相关的

### 第三范式：消除依赖传递

如果某一属性依赖于其他非主键属性，而其他非主键属性又依赖于主键，那么这个属性就是间接依赖于主键，这被称作传递依赖于主属性。 **通俗解释就是一张表最多只存两层同类型信息**



### 反三范式

没有冗余的数据库未必是最好的数据库，有时为了提高运行效率，提高读性能，就必须降低范式标准，适当保留冗余数据。具体做法是： 在概念数据模型设计时遵守第三范式，降低范式标准的工作放到物理数据模型设计时考虑。降低范式就是增加字段，减少了查询时的关联，提高查询效率，因为在数据库的操作中查询的比例要远远大于DML的比例。但是反范式化一定要适度，并且在原本已满足三范式的基础上再做调整的




参考资料：

SQL92 http://owen.sj.ca.us/rkowen/howto/sql92F.html

MySql文档 https://dev.mysql.com/doc/refman/8.0/en/sql-syntax.html

### 阅读资料

- [MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html) 

- [MySQL索引原理及慢查询优化](https://tech.meituan.com/mysql-index.html)

- [InnoDB备忘录 - Next-Key Lock](http://zhongmingmao.me/2017/05/19/innodb-next-key-lock/)

- [MySQL主从复制与读写分离](https://www.cnblogs.com/luckcs/articles/2543607.html)
