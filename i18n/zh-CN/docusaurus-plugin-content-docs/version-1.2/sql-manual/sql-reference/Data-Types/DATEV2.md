---
{
    "title": "DATEV2",
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

## DATEV2

### name

DATEV2

## 描述
    DATEV2类型
        日期类型，目前的取值范围是['0000-01-01', '9999-12-31'], 默认的打印形式是'yyyy-MM-dd'

### note
    DATEV2类型相比DATE类型更加高效，在计算时，DATEV2相比DATE可以节省一半的内存使用量。
    为了和mysql保持一致的行为，不存在0000-02-29这个日期。

## 举例
    mysql> SELECT CAST('2003-12-31 01:02:03' as DATEV2);
        -> '2003-12-31'

### keywords

    DATEV2
