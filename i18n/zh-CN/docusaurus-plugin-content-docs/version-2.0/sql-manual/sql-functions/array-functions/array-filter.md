---
{
    "title": "ARRAY_FILTER",
    "language": "zh-CN"
}
---

## array_filter

array_filter(lambda,array)

:::tip 提示
该功能自 Apache Doris  2.0.2 版本起支持
:::

array array_filter(array arr, array_bool filter_column)



## 描述

## 语法
```sql
ARRAY<T> array_filter(lambda, ARRAY<T> arr)
ARRAY<T> array_filter(ARRAY<T> arr, ARRAY<Bool> filter_column)
```

使用 lambda 表达式作为输入参数，计算筛选另外的输入参数 ARRAY 列的数据。
并过滤掉在结果中 0 和 NULL 的值。

```
array_filter(x->x>0, array1);
array_filter(x->(x+2)=10, array1);
array_filter(x->(abs(x)-2)>0, array1);
array_filter(c_array,[0,1,0]);
```

## 举例

```shell
mysql [test]>select c_array,array_filter(c_array,[0,1,0]) from array_test;
+-----------------+----------------------------------------------------+
| c_array         | array_filter(`c_array`, ARRAY(FALSE, TRUE, FALSE)) |
+-----------------+----------------------------------------------------+
| [1, 2, 3, 4, 5] | [2]                                                |
| [6, 7, 8]       | [7]                                                |
| []              | []                                                 |
| NULL            | NULL                                               |
+-----------------+----------------------------------------------------+

mysql [test]>select array_filter(x->(x > 1),[1,2,3,0,null]);
+----------------------------------------------------------------------------------------------+
| array_filter(ARRAY(1, 2, 3, 0, NULL), array_map([x] -> (x(0) > 1), ARRAY(1, 2, 3, 0, NULL))) |
+----------------------------------------------------------------------------------------------+
| [2, 3]                                                                                       |
+----------------------------------------------------------------------------------------------+

mysql [test]>select *, array_filter(x->x>0,c_array2) from array_test2;
+------+-----------------+-------------------------+------------------------------------------------------------------+
| id   | c_array1        | c_array2                | array_filter(`c_array2`, array_map([x] -> x(0) > 0, `c_array2`)) |
+------+-----------------+-------------------------+------------------------------------------------------------------+
|    1 | [1, 2, 3, 4, 5] | [10, 20, -40, 80, -100] | [10, 20, 80]                                                     |
|    2 | [6, 7, 8]       | [10, 12, 13]            | [10, 12, 13]                                                     |
|    3 | [1]             | [-100]                  | []                                                               |
|    4 | NULL            | NULL                    | NULL                                                             |
+------+-----------------+-------------------------+------------------------------------------------------------------+
4 rows in set (0.01 sec)

mysql [test]>select *, array_filter(x->x%2=0,c_array2) from array_test2;
+------+-----------------+-------------------------+----------------------------------------------------------------------+
| id   | c_array1        | c_array2                | array_filter(`c_array2`, array_map([x] -> x(0) % 2 = 0, `c_array2`)) |
+------+-----------------+-------------------------+----------------------------------------------------------------------+
|    1 | [1, 2, 3, 4, 5] | [10, 20, -40, 80, -100] | [10, 20, -40, 80, -100]                                              |
|    2 | [6, 7, 8]       | [10, 12, 13]            | [10, 12]                                                             |
|    3 | [1]             | [-100]                  | [-100]                                                               |
|    4 | NULL            | NULL                    | NULL                                                                 |
+------+-----------------+-------------------------+----------------------------------------------------------------------+

mysql [test]>select *, array_filter(x->(x*(-10)>0),c_array2) from array_test2;
+------+-----------------+-------------------------+----------------------------------------------------------------------------+
| id   | c_array1        | c_array2                | array_filter(`c_array2`, array_map([x] -> (x(0) * (-10) > 0), `c_array2`)) |
+------+-----------------+-------------------------+----------------------------------------------------------------------------+
|    1 | [1, 2, 3, 4, 5] | [10, 20, -40, 80, -100] | [-40, -100]                                                                |
|    2 | [6, 7, 8]       | [10, 12, 13]            | []                                                                         |
|    3 | [1]             | [-100]                  | [-100]                                                                     |
|    4 | NULL            | NULL                    | NULL                                                                       |
+------+-----------------+-------------------------+----------------------------------------------------------------------------+

mysql [test]>select *, array_filter(x->x>0, array_map((x,y)->(x>y), c_array1,c_array2)) as res from array_test2;
+------+-----------------+-------------------------+--------+
| id   | c_array1        | c_array2                | res    |
+------+-----------------+-------------------------+--------+
|    1 | [1, 2, 3, 4, 5] | [10, 20, -40, 80, -100] | [1, 1] |
|    2 | [6, 7, 8]       | [10, 12, 13]            | []     |
|    3 | [1]             | [-100]                  | [1]    |
|    4 | NULL            | NULL                    | NULL   |
+------+-----------------+-------------------------+--------+
```

### keywords

ARRAY,FILTER,ARRAY_FILTER

