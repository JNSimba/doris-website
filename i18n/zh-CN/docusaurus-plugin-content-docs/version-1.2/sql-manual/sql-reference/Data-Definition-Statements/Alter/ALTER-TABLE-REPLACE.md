---
{
    "title": "ALTER-TABLE-REPLACE",
    "language": "zh-CN"
}
---

## ALTER-TABLE-REPLACE

### Name

ALTER TABLE REPLACE

## 描述

对两个表进行原子的替换操作。 该操作仅适用于 OLAP 表。

```sql
ALTER TABLE [db.]tbl1 REPLACE WITH TABLE tbl2
[PROPERTIES('swap' = 'true')];
```

将表 tbl1 替换为表 tbl2。

如果 `swap` 参数为 `true`，则替换后，名称为 `tbl1` 表中的数据为原 `tbl2` 表中的数据。而名称为 `tbl2` 表中的数据为原 `tbl1` 表中的数据。即两张表数据发生了互换。

如果 `swap` 参数为 `false`，则替换后，名称为 `tbl1` 表中的数据为原 `tbl2` 表中的数据。而名称为 `tbl2` 表被删除。

#### 原理

替换表功能，实际上是将以下操作集合变成一个原子操作。

假设要将表 A 替换为表 B，且 `swap` 为 `true`，则操作如下：

1. 将表 B 重名为表 A。
2. 将表 A 重名为表 B。

如果 `swap` 为 `false`，则操作如下：

1. 删除表 A。
2. 将表 B 重名为表 A。

#### 注意事项
1. `swap` 参数默认为 `true`。即替换表操作相当于将两张表数据进行交换。
2. 如果设置 `swap` 参数为 `false`，则被替换的表（表A）将被删除，且无法恢复。
3. 替换操作仅能发生在两张 OLAP 表之间，且不会检查两张表的表结构是否一致。
4. 替换操作不会改变原有的权限设置。因为权限检查以表名称为准。

## 举例

1. 将 `tbl1` 与 `tbl2` 进行交换，不删除 `tbl1` 表

```sql
ALTER TABLE tbl1 REPLACE WITH TABLE tbl2
    [PROPERTIES('swap' = 'true')];
```

### Keywords

```text
ALTER, TABLE, REPLACE, ALTER TABLE
```

### Best Practice
1. 原子的覆盖写操作

   某些情况下，用户希望能够重写某张表的数据，但如果采用先删除再导入的方式进行，在中间会有一段时间无法查看数据。这时，用户可以先使用 `CREATE TABLE LIKE` 语句创建一个相同结构的新表，将新的数据导入到新表后，通过替换操作，原子的替换旧表，以达到目的。分区级别的原子覆盖写操作，请参阅 [临时分区文档](../../../../advanced/partition/table-temp-partition.md)。
