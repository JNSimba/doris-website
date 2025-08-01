---
{
    "title": "STR_TO_DATE",
    "language": "zh-CN"
}
---

## str_to_date
## 描述
## 语法

`DATETIME STR_TO_DATE(VARCHAR str, VARCHAR format)`

通过format指定的方式将str转化为DATE类型，如果转化结果不对返回NULL。注意format指定的是第一个参数的格式。

支持的format格式与[date_format](date-format.md)一致

## 举例

```
mysql> select str_to_date('2014-12-21 12:34:56', '%Y-%m-%d %H:%i:%s');
+---------------------------------------------------------+
| str_to_date('2014-12-21 12:34:56', '%Y-%m-%d %H:%i:%s') |
+---------------------------------------------------------+
| 2014-12-21 12:34:56                                     |
+---------------------------------------------------------+

mysql> select str_to_date('2014-12-21 12:34%3A56', '%Y-%m-%d %H:%i%%3A%s');
+--------------------------------------------------------------+
| str_to_date('2014-12-21 12:34%3A56', '%Y-%m-%d %H:%i%%3A%s') |
+--------------------------------------------------------------+
| 2014-12-21 12:34:56                                          |
+--------------------------------------------------------------+

mysql> select str_to_date('200442 Monday', '%X%V %W');
+-----------------------------------------+
| str_to_date('200442 Monday', '%X%V %W') |
+-----------------------------------------+
| 2004-10-18                              |
+-----------------------------------------+

mysql> select str_to_date("2020-09-01", "%Y-%m-%d %H:%i:%s");
+------------------------------------------------+
| str_to_date('2020-09-01', '%Y-%m-%d %H:%i:%s') |
+------------------------------------------------+
| 2020-09-01 00:00:00                            |
+------------------------------------------------+
1 row in set (0.01 sec)
```

### keywords

    STR_TO_DATE,STR,TO,DATE
