---
{
    "title": "COUNTEQUAL",
    "language": "zh-CN"
}
---

## countequal

countequal

## 描述

## 语法

`BIGINT countequal(ARRAY<T> arr, T value)`

判断数组中包含value元素的个数。返回结果如下：

```
num      - value在array中的数量；
0        - value不存在数组arr中；
NULL     - 如果数组为NULL。
```

## 举例

```
mysql> select *, countEqual(c_array,5) from array_test;
+------+-----------------+--------------------------+
| id   | c_array         | countequal(`c_array`, 5) |
+------+-----------------+--------------------------+
|    1 | [1, 2, 3, 4, 5] |                        1 |
|    2 | [6, 7, 8]       |                        0 |
|    3 | []              |                        0 |
|    4 | NULL            |                     NULL |
+------+-----------------+--------------------------+

mysql> select *,countEqual(c_array, 1),countEqual(c_array, 5),countEqual(c_array, NULL) from array_test;
+------+-----------------------+--------------------------+--------------------------+-----------------------------+
| id   | c_array               | countequal(`c_array`, 1) | countequal(`c_array`, 5) | countequal(`c_array`, NULL) |
+------+-----------------------+--------------------------+--------------------------+-----------------------------+
|    1 | [1, 2, 3, 4, 5]       |                        1 |                        1 |                           0 |
|    2 | [6, 7, 8]             |                        0 |                        0 |                           0 |
|    3 | []                    |                        0 |                        0 |                           0 |
|    4 | NULL                  |                     NULL |                     NULL |                        NULL |
|    5 | [66, 77]              |                        0 |                        0 |                           0 |
|    5 | [66, 77]              |                        0 |                        0 |                           0 |
|    6 | NULL                  |                     NULL |                     NULL |                        NULL |
|    7 | [NULL, NULL, NULL]    |                        0 |                        0 |                           3 |
|    8 | [1, 2, 3, 4, 5, 5, 5] |                        1 |                        3 |                           0 |
+------+-----------------------+--------------------------+--------------------------+-----------------------------+
```

### keywords

ARRAY,COUNTEQUAL
