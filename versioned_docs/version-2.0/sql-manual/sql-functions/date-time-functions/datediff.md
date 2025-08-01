---
{
    "title": "DATEDIFF",
    "language": "en"
}
---

## datediff
### Description
#### Syntax

`INT DATEDIFF (DATETIME expr1, DATETIME expr2)`


Calculate expr1 - expr2, the result is accurate to the unit of a day.

Expr1 and expr2 parameters are valid date or date/time expressions.

Note: Only the date part of the value participates in the calculation.

#### example

```
mysql> select datediff(CAST('2007-12-31 23:59:59' AS DATETIME), CAST('2007-12-30' AS DATETIME));
+-----------------------------------------------------------------------------------+
| datediff(CAST('2007-12-31 23:59:59' AS DATETIME), CAST('2007-12-30' AS DATETIME)) |
+-----------------------------------------------------------------------------------+
|                                                                                 1 |
+-----------------------------------------------------------------------------------+

mysql> select datediff(CAST('2010-11-30 23:59:59' AS DATETIME), CAST('2010-12-31' AS DATETIME));
+-----------------------------------------------------------------------------------+
| datediff(CAST('2010-11-30 23:59:59' AS DATETIME), CAST('2010-12-31' AS DATETIME)) |
+-----------------------------------------------------------------------------------+
|                                                                               -31 |
+-----------------------------------------------------------------------------------+
```
### keywords
    DATEDIFF
