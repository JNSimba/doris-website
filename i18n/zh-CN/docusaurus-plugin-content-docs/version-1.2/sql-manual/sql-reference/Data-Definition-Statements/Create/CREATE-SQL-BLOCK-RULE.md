---
{
    "title": "CREATE-SQL-BLOCK-RULE",
    "language": "zh-CN"
}
---

## CREATE-SQL-BLOCK-RULE

### Name

CREATE SQL BLOCK RULE

## 描述

该语句创建SQL阻止规则，该功能仅用于限制查询语句，不会限制explian语句的执行。

支持按用户配置SQL黑名单：

- 通过正则匹配的方式拒绝指定SQL
- 通过设置partition_num, tablet_num, cardinality, 检查一个查询是否达到其中一个限制
  - partition_num, tablet_num, cardinality 可以一起设置，一旦一个查询达到其中一个限制，查询将会被拦截

语法：

```sql
CREATE SQL_BLOCK_RULE rule_name 
[PROPERTIES ("key"="value", ...)];
```

参数说明：

- sql：匹配规则(基于正则匹配,特殊字符需要转译,如`select *`使用`select \\*`)，可选，默认值为 "NULL", 最后不要带分号
- sqlHash: sql hash值，用于完全匹配，我们会在`fe.audit.log`打印这个值，可选，这个参数和sql只能二选一，默认值为 "NULL"
- partition_num: 一个扫描节点会扫描的最大partition数量，默认值为0L
- tablet_num: 一个扫描节点会扫描的最大tablet数量，默认值为0L
- cardinality: 一个扫描节点粗略的扫描行数，默认值为0L
- global：是否全局(所有用户)生效，默认为false
- enable：是否开启阻止规则，默认为true

## 举例

1. 创建一个名称为 test_rule 的阻止规则

     ```sql
     CREATE SQL_BLOCK_RULE test_rule 
     PROPERTIES(
       "sql"="select \\* from order_analysis;",
       "global"="false",
       "enable"="true"
     );
     ```

     

     当我们去执行刚才我们定义在规则里的sql时就会返回异常错误，示例如下：

     ```sql
     mysql> select * from order_analysis;
     ERROR 1064 (HY000): errCode = 2, detailMessage = sql match regex sql block rule: order_analysis_rule
     ```

2. 创建 test_rule2，将最大扫描的分区数量限制在30个，最大扫描基数限制在100亿行，示例如下：

    ```sql
    CREATE SQL_BLOCK_RULE test_rule2 
    PROPERTIES
    (
       "partition_num" = "30",
       "cardinality" = "10000000000",
       "global" = "false",
       "enable" = "true"
    );
   Query OK, 0 rows affected (0.01 sec)
   ```
   
3. 创建包含特殊字符的 SQL BLOCK RULE：

    ```sql
    CREATE SQL_BLOCK_RULE test_rule3
    PROPERTIES
    ( 
    "sql" = "select count\\(1\\) from db1.tbl1"
    );
    CREATE SQL_BLOCK_RULE test_rule4
    PROPERTIES
    ( 
    "sql" = "select \\* from db1.tbl1"
    );
    ```

### Keywords

```text
CREATE, SQL_BLCOK_RULE
```

### Best Practice

