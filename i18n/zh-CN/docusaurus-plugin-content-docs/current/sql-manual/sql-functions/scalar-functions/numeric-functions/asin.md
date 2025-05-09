---
{
    "title": "ASIN",
    "language": "zh-CN"
}
---

<!-- 
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at
  http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

## 描述

返回`x`的反正弦值，若 `x`不在`-1`到 `1`的范围之内，则返回 `nan`.

## 语法

```sql
ASIN(<x>)
```

## 参数

| 参数 | 说明 |
| -- | -- |
| `<x>` | 需要被计算反正弦的值 |

## 返回值

参数 x 的反正弦值


## 举例

```sql
select asin(0.5);
```

```text
+---------------------+
| asin(0.5)           |
+---------------------+
| 0.52359877559829893 |
+---------------------+
```

```sql
select asin(2);
```

```text
+-----------+
| asin(2.0) |
+-----------+
|       nan |
+-----------+
```
