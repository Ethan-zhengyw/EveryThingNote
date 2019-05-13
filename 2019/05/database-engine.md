# 相关概念
**存储引擎**
* 存储引擎是数据库管理系统中负责从数据库中进行插入，读取，更新，删除（CRUD）操作的底层软件模块。
* 不同的存储引擎采用不同的存储介质、索引技术、锁定级别...，我们需要根据实际场景需要选用存储引擎以获得相应的功能


**索引**
数据库索引是一种采用能够高效查询的数据结构进行存储的数据，我们可以针对数据库表的一列或多列创建索引，在后续的数据库操作中通过额外存储空间和写操作维护索引数据结构，从而提高在数据库表上的查询操作的效率。

数据库的读操作（或者说查询操作）可能是带条件的，比如：查询test表中所有考试合格的学生姓名和分数，需要指定条件‘score >= 60’：
```
SELECT name, score FROM test WHERE score >= 60;
```
* 如果没有数据库索引，需要一条记录一条记录地扫描，比较score是否大于等于60，时间复杂度为O(N)；
* 如果针对score列创建了索引的话，就能够更快地定位到目标数据的存储位置，时间复杂度小于O(N)。

**索引算法/数据结构**
按照索引的定义“数据库索引是一种采用能够高效查询的数据结构进行存储的数据”， 所以描述索引时，就需要考虑它存储数据的结构。数据结构不外乎：数组、链表、栈、队列、树、哈希表。回想一下排序和查找算法，涉及的数据结构主要是：数组、树、哈希表。

选择索引数据结构时需要考虑的因素有：
* 在该数据结构上进行CRUD操作的效率
* 插入新的数据库记录或者对已有数据库记录进行修改、删除操作时，维护索引数据结构的效率

如果单纯只考虑查询的效率的话，完全可以使用有序数组作为索引数据结构，但是除了查询操作外，还有插入、删除和修改操作。虽然数组和树的查询效率都能优化到O(nlogn)，但是在数组中进行插入、删除操作的效率不如树，所以如果只能在数组和树中进行选择索引数据结构的话，树是更好的选择。

**非聚集索引**
数据库表中的数据行的物理存储不是有序的，而索引是有序的；数据存储在一个位置，索引存储在另一个位置，索引中包含特定行的地址指针（或者聚集索引值）。在MySQL的InnoDB引擎中，secondary index即非聚集索引，非聚集索引数据结构中存放的数据包括：非聚集索引列值（key），聚集索引/主键的值（value）
```
Create NonClustered Index IX_table1_col3 ON table1(col3);
```

**聚集索引**
数据库表中的数据行按照聚集索引顺序进行存储，一张表只能有一个聚集索引，一个聚集索引可以包含多个列。给定指定的值在索引中进行检索，检索到该值的位置时，目标记录就在该位置，不需要像非聚集索引一样通过特定行的地址指针再去访问目标记录，因此聚集索引较非聚集索引对查询性能提升更高。
```
Create Clustered Index IX_table1_col1_col2 ON table1(col1 DESC, col2 ASC);
```

**主键索引**
主键：能唯一标识一条记录的属性组(注意是组)即为主键
数据库表的主键在创建数据库时可以指定
```
mysql> create table test_primary_key_2(pkey char(32), pkey2 char(32), val char(32), primary key (pkey, pkey2));
Query OK, 0 rows affected (0.06 sec)

mysql> show create table test_primary_key_2;
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Table              | Create Table                                                                                                                                                                                                                  |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| test_primary_key_2 | CREATE TABLE `test_primary_key_2` (
  `pkey` char(32) NOT NULL,
  `pkey2` char(32) NOT NULL,
  `val` char(32) DEFAULT NULL,
  PRIMARY KEY (`pkey`,`pkey2`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci |
+--------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

```
数据库引擎在创建数据库表时会自动为主键创建索引
```
mysql> show index from test_primary_key_2;
+--------------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| Table              | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment | Visible | Expression |
+--------------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
| test_primary_key_2 |          0 | PRIMARY  |            1 | pkey        | A         |           0 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
| test_primary_key_2 |          0 | PRIMARY  |            2 | pkey2       | A         |           0 |     NULL |   NULL |      | BTREE      |         |               | YES     | NULL       |
+--------------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+---------+------------+
2 rows in set (0.01 sec)

```

**非主键索引**
参考非聚集索引（Secondary Index）

**全文索引**
全文索引适用于关键字搜索场景。它的基本原理是：先将内容进行分词，然后统计各个关键词在哪些文档中出现过，按照词频倒序进行排列得到“倒排表”，最后使用倒排表进行关键字搜索。全文索引的维护不会每次CRUD操作都进行更新，一般是定期进行。
```
Create Fulltext Index IX_table1_col1_col2 ON table1(col1 DESC, col2 ASC);
```

**堆表**
**静态表**
**动态表**
**压缩表**
**索引组织表**

**事务**
**ACID**
**锁**
**共享锁**
**排他锁**
**意向共享锁(表锁)**
**意向排他锁(表锁)**
**间隙锁**
**死锁**

**自动增长列**
**线性地址空间**


# 如何比较不同的存储引擎

* 分层存储（存储介质，性能与价格与易变性volatile的考量）
* 数据结构
* 数据方向和集群？
* 索引
* 为什么需要多个索引，多个索引如何工作

* 是否支持事务
* 是否支持外键
* 是否支持表锁
* 是否支持行级锁
* 存储目标介质（/dev/null、内存、磁盘）
* 存储格式（csv、archive）
* 实际存储文件
* 存储目标介质满了以后继续插入数据的响应
* 索引方法（顺序访问索引）
* 是否支持并发读同一张表
* 对一张表进行并发读时，如果写请求来临如何处理
* SQL语句查询 优先级
* 是否支持指定查询任务的优先级
* 内部行指针？
* merge表？
* 是否支持通过配置、转化节省存储空间
* 是否支持Full-Text搜索
* 索引优化（高字节优先）
* COUNT行为
* 性能

# 如何选择存储引擎

# 存储引擎种类
* MyISAM
* 
```
mysql> show engines;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.00 sec)
```

# 存储引擎详情

**MyISAM**：Indexed Sequential Access Method

```
mysql> show engines \G
*************************** 1. row ***************************
      Engine: FEDERATED
     Support: NO
     Comment: Federated MySQL storage engine
Transactions: NULL
          XA: NULL
  Savepoints: NULL
*************************** 2. row ***************************
      Engine: MEMORY
     Support: YES
     Comment: Hash based, stored in memory, useful for temporary tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 3. row ***************************
      Engine: InnoDB
     Support: DEFAULT
     Comment: Supports transactions, row-level locking, and foreign keys
Transactions: YES
          XA: YES
  Savepoints: YES
*************************** 4. row ***************************
      Engine: PERFORMANCE_SCHEMA
     Support: YES
     Comment: Performance Schema
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 5. row ***************************
      Engine: MyISAM
     Support: YES
     Comment: MyISAM storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 6. row ***************************
      Engine: MRG_MYISAM
     Support: YES
     Comment: Collection of identical MyISAM tables
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 7. row ***************************
      Engine: BLACKHOLE
     Support: YES
     Comment: /dev/null storage engine (anything you write to it disappears)
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 8. row ***************************
      Engine: CSV
     Support: YES
     Comment: CSV storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
*************************** 9. row ***************************
      Engine: ARCHIVE
     Support: YES
     Comment: Archive storage engine
Transactions: NO
          XA: NO
  Savepoints: NO
9 rows in set (0.00 sec)
```
