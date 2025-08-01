---
{
    "title": "TO_ISO8601",
    "language": "zh-CN"
}
---

## 描述

将日期时间值转换为 ISO8601 格式的字符串。

## 语法

```sql
TO_ISO8601(<dt>)
```

## 参数

| 参数 | 说明 |
| ---- | ---- |
| `<dt>` | 输入的日期时间值，类型为 DATETIME 或 DATE |

## 返回值

返回类型为 VARCHAR，表示 ISO8601 格式的日期时间字符串。

## 举例

```sql
SELECT TO_ISO8601('2020-01-01 12:30:45');
```

```text
+-------------------------------------+
| to_iso8601('2020-01-01 12:30:45.0') |
+-------------------------------------+
| 2020-01-01T12:30:45                 |
+-------------------------------------+
```
