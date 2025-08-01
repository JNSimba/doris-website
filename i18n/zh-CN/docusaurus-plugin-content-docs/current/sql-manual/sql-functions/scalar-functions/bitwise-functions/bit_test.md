---
{
"title": "BIT_TEST",
"language": "zh-CN"
}
---

## 描述
将`<x>`的值转换为二进制的形式，返回指定位置`<bits>`的值，`<bits>`从 0 开始，从右到左。

如果`<bits>` 有多个值，则将多个`<bits>`位置上的值用与运算符结合起来，返回最终结果。

如果`<bits>` 的取值为负数或者超过`<x>`的 bit 位总数，则会返回结果为 0。

整数`<x>`范围：TINYINT、SMALLINT、INT、BIGINT、LARGEINT。

## 别名
- BIT_TEST_ALL

## 语法
```sql
BIT_TEST( <x>, <bits>[, <bits> ... ])
```

## 参数
| 参数      | 说明     |
|---------|--------|
| `<x>`   | 需计算的整数 |
| `<bits>` | 指定位置的值 |

## 返回值

返回指定位置的值

## 举例

```sql
select BIT_TEST(43, 1), BIT_TEST(43, -1), BIT_TEST(43, 0, 1, 3, 5,2);
```

```text
+-----------------+------------------+-----------------------------+
| bit_test(43, 1) | bit_test(43, -1) | bit_test(43, 0, 1, 3, 5, 2) |
+-----------------+------------------+-----------------------------+
|               1 |                0 |                           0 |
+-----------------+------------------+-----------------------------+
```

