---
{
    "title": "CREATE ROUTINE LOAD",
    "language": "zh-CN"
}

---

## 描述

例行导入（Routine Load）功能支持用户提交一个常驻的导入任务，通过不断地从指定的数据源读取数据，将数据导入到 Doris 中。

目前仅支持通过无认证或者 SSL 认证方式，从 Kafka 导入 CSV 或 Json 格式的数据。 [导入 Json 格式数据使用示例](../../../../data-operate/import/import-way/routine-load-manual.md#导入Json格式数据使用示例)

## 语法

```sql
CREATE ROUTINE LOAD [<db>.]<job_name> [ON <tbl_name>]
[<merge_type>]
[<load_properties>]
[<job_properties>]
FROM <data_source> [<data_source_properties>]
[COMMENT "<comment>"]
```

## 必选参数

**1. `[<db>.]<job_name>`**

> 导入作业的名称，在同一个 database 内，相同名称只能有一个 job 在运行。

**2. `FROM <data_source>`**

> 数据源的类型。当前支持：KAFKA

**3. `<data_source_properties>`**

> 1. `<kafka_broker_list>`
> 
>    Kafka 的 broker 连接信息。格式为 ip:host。多个 broker 之间以逗号分隔。
>    
>    ```text
>    "kafka_broker_list" = "broker1:9092,broker2:9092"
>    ```

> 2. `<kafka_topic>`
> 
>    指定要订阅的 Kafka 的 topic。
>    ```text
>    "kafka_topic" = "my_topic"
>    ```

## 可选参数

**1. `<tbl_name>`**

> 指定需要导入的表的名称，可选参数，如果不指定，则采用动态表的方式，这个时候需要 Kafka 中的数据包含表名的信息。
> 
> 目前仅支持从 Kafka 的 Value 中获取表名，且需要符合这种格式：以 json 为例：`table_name|{"col1": "val1", "col2": "val2"}`, 
> 其中 `tbl_name` 为表名，以 `|` 作为表名和表数据的分隔符。

> csv 格式的数据也是类似的，如：`table_name|val1,val2,val3`。注意，这里的 `table_name` 必须和 Doris 中的表名一致，否则会导致导入失败。
>
> tips: 动态表不支持 `columns_mapping` 参数。如果你的表结构和 Doris 中的表结构一致，且存在大量的表信息需要导入，那么这种方式将是不二选择。


**2. `<merge_type>`**

> 数据合并类型。默认为 APPEND，表示导入的数据都是普通的追加写操作。MERGE 和 DELETE 类型仅适用于 Unique Key 模型表。其中 MERGE 类型需要配合 [DELETE ON] 语句使用，以标注 Delete Flag 列。而 DELETE 类型则表示导入的所有数据皆为删除数据。
>
> tips: 当使用动态多表的时候，请注意此参数应该符合每张动态表的类型，否则会导致导入失败。

**3. `<load_properties>`**

> 用于描述导入数据。组成如下：
>
> ```SQL
> [column_separator],
> [columns_mapping],
> [preceding_filter],
> [where_predicates],
> [partitions],
> [DELETE ON],
> [ORDER BY]
> ```
>
> 1. `<column_separator>`
>
>    指定列分隔符，默认为 `\t`
>
>    `COLUMNS TERMINATED BY ","`

> 2. `<columns_mapping>`
>
>    用于指定文件列和表中列的映射关系，以及各种列转换等。关于这部分详细介绍，可以参阅 [列的映射，转换与过滤] 文档。
>
>    `(k1, k2, tmpk1, k3 = tmpk1 + 1)`
>
>    tips: 动态表不支持此参数。

> 3. `<preceding_filter>`
>
>    过滤原始数据。关于这部分详细介绍，可以参阅 [列的映射，转换与过滤] 文档。
>
>    `WHERE k1 > 100 and k2 = 1000`
>
>    tips: 动态表不支持此参数。  
>
> 4. `<where_predicates>`
>
>    根据条件对导入的数据进行过滤。关于这部分详细介绍，可以参阅 [列的映射，转换与过滤] 文档。
>
>    `WHERE k1 > 100 and k2 = 1000`
>
>    tips: 当使用动态多表的时候，请注意此参数应该符合每张动态表的列，否则会导致导入失败。通常在使用动态多表的时候，我们仅建议通用公共列使用此参数。  

> 5. `<partitions>`
>
>    指定导入目的表的哪些 partition 中。如果不指定，则会自动导入到对应的 partition 中。
>
>    `PARTITION(p1, p2, p3)`
>
>    tips: 当使用动态多表的时候，请注意此参数应该符合每张动态表，否则会导致导入失败。

> 6. `<DELETE ON>`
>
>    需配合 MEREGE 导入模式一起使用，仅针对 Unique Key 模型的表。用于指定导入数据中表示 Delete Flag 的列和计算关系。
>
>    `DELETE ON v3 >100`
>
>    tips: 当使用动态多表的时候，请注意此参数应该符合每张动态表，否则会导致导入失败。

> 7. `<ORDER BY>`
>
>    仅针对 Unique Key 模型的表。用于指定导入数据中表示 Sequence Col 的列。主要用于导入时保证数据顺序。
>
>    tips: 当使用动态多表的时候，请注意此参数应该符合每张动态表，否则会导致导入失败。

**4. `<job_properties>`**

> 用于指定例行导入作业的通用参数。
>
>    ```text
>    PROPERTIES (
>        "key1" = "val1",
>        "key2" = "val2"
>    )
>  ```
>
> 目前我们支持以下参数：
>
> 1. `<desired_concurrent_number>`
>
>     期望的并发度。一个例行导入作业会被分成多个子任务执行。这个参数指定一个作业最多有多少任务可以同时执行。必须大于 0。默认为 5。
>
>    这个并发度并不是实际的并发度，实际的并发度，会通过集群的节点数、负载情况，以及数据源的情况综合考虑。
>
>    `"desired_concurrent_number" = "3"`
>
> 2. `<max_batch_interval>/<max_batch_rows>/<max_batch_size>`
>
>    这三个参数分别表示：
>
>     1. 每个子任务最大执行时间，单位是秒。必须大于等于 1。默认为 10。
>     2. 每个子任务最多读取的行数。必须大于等于 200000。默认是 20000000。
>     3. 每个子任务最多读取的字节数。单位是字节，范围是 100MB 到 10GB。默认是 1G。
>
>     这三个参数，用于控制一个子任务的执行时间和处理量。当任意一个达到阈值，则任务结束。
>
>     ```text
>     "max_batch_interval" = "20",
>     "max_batch_rows" = "300000",
>     "max_batch_size" = "209715200"
>     ```
>
> 3. `<max_error_number>`
>
>     采样窗口内，允许的最大错误行数。必须大于等于 0。默认是 0，即不允许有错误行。
>
>     采样窗口为 `max_batch_rows * 10`。即如果在采样窗口内，错误行数大于 `max_error_number`，则会导致例行作业被暂停，需要人工介入检查数据质量问题。
>
>     被 where 条件过滤掉的行不算错误行。
>
> 4. `<strict_mode>`
>
>     是否开启严格模式，默认为关闭。如果开启后，非空原始数据的列类型变换如果结果为 NULL，则会被过滤。指定方式为：
>
>     `"strict_mode" = "true"`
>
>     strict mode 模式的意思是：对于导入过程中的列类型转换进行严格过滤。严格过滤的策略如下：
>
>     1. 对于列类型转换来说，如果 strict mode 为 true，则错误的数据将被 filter。这里的错误数据是指：原始数据并不为空值，在参与列类型转换后结果为空值的这一类数据。
>     2. 对于导入的某列由函数变换生成时，strict mode 对其不产生影响。
>     3. 对于导入的某列类型包含范围限制的，如果原始数据能正常通过类型转换，但无法通过范围限制的，strict mode 对其也不产生影响。例如：如果类型是 decimal(1,0), 原始数据为 10，则属于可以通过类型转换但不在列声明的范围内。这种数据 strict 对其不产生影响。
>
>     **strict mode 与 source data 的导入关系**
>
>     这里以列类型为 TinyInt 来举例
>
>     注：当表中的列允许导入空值时
>
>     | source data | source data example | string to int | strict_mode   | result                 |
>     | ----------- | ------------------- | ------------- | ------------- | ---------------------- |
>     | 空值        | `\N`                  | N/A           | true or false | NULL                   |
>     | not null    | aaa or 2000         | NULL          | true          | invalid data(filtered) |
>     | not null    | aaa                 | NULL          | false         | NULL                   |
>     | not null    | 1                   | 1             | true or false | correct data           |
>
>     这里以列类型为 Decimal(1,0) 举例
>
>     注：当表中的列允许导入空值时
>
>     | source data | source data example | string to int | strict_mode   | result                 |
>     | ----------- | ------------------- | ------------- | ------------- | ---------------------- |
>     | 空值        | `\N`                  | N/A           | true or false | NULL                   |
>     | not null    | aaa                 | NULL          | true          | invalid data(filtered) |
>     | not null    | aaa                 | NULL          | false         | NULL                   |
>     | not null    | 1 or 10             | 1             | true or false | correct data           |
>
>     注意：10 虽然是一个超过范围的值，但是因为其类型符合 decimal 的要求，所以 strict mode 对其不产生影响。10 最后会在其他 ETL 处理流程中被过滤。但不会被 strict mode 过滤。
>
> 5. `<timezone>`
>
>     指定导入作业所使用的时区。默认为使用 Session 的 timezone 参数。该参数会影响所有导入涉及的和时区有关的函数结果。
>
>     `"timezone" = "Asia/Shanghai"`
>
> 6. `<format>`
>
>     指定导入数据格式，默认是 csv，支持 json 格式。
>
>     `"format" = "json"`
>
> 7. `<jsonpaths>`
>
>     当导入数据格式为 json 时，可以通过 jsonpaths 指定抽取 Json 数据中的字段。
>
>     `-H "jsonpaths: [\"$.k2\", \"$.k1\"]"`
>
> 8. `<strip_outer_array>`
>
>     当导入数据格式为 json 时，strip_outer_array 为 true 表示 Json 数据以数组的形式展现，数据中的每一个元素将被视为一行数据。默认值是 false。
>
>     `-H "strip_outer_array: true"`
>
> 9. `<json_root>`
>
>     当导入数据格式为 json 时，可以通过 json_root 指定 Json 数据的根节点。Doris 将通过 json_root 抽取根节点的元素进行解析。默认为空。
>
>     `-H "json_root: $.RECORDS"`
>  
> 10. `<send_batch_parallelism>`
>
>     整型，用于设置发送批处理数据的并行度，如果并行度的值超过 BE 配置中的 `max_send_batch_parallelism_per_job`，那么作为协调点的 BE 将使用 `max_send_batch_parallelism_per_job` 的值。 
>
>     `"send_batch_parallelism" = "10"`
>
> 11. `<load_to_single_tablet>`
>
>     布尔类型，为 true 表示支持一个任务只导入数据到对应分区的一个 tablet，默认值为 false，该参数只允许在对带有 random 分桶的 olap 表导数的时候设置。
>
>     `"load_to_single_tablet" = "true"`
>
> 12. `<partial_columns>`
>
>     布尔类型，为 true 表示使用部分列更新，默认值为 false，该参数只允许在表模型为 Unique 且采用 Merge on Write 时设置。一流多表不支持此参数。
>
>     `"partial_columns" = "true"`
>
> 13. `<max_filter_ratio>`
>
>     采样窗口内，允许的最大过滤率。必须在大于等于0到小于等于1之间。默认值是 0。
>
>     采样窗口为 `max_batch_rows * 10`。即如果在采样窗口内，错误行数/总行数大于 `max_filter_ratio`，则会导致例行作业被暂停，需要人工介入检查数据质量问题。
>
>     被 where 条件过滤掉的行不算错误行。
>
> 14. `<enclose>`
>
>     包围符。当 csv 数据字段中含有行分隔符或列分隔符时，为防止意外截断，可指定单字节字符作为包围符起到保护作用。例如列分隔符为","，包围符为"'"，数据为"a,'b,c'",则"b,c"会被解析为一个字段。
>
>     注意：当 enclose 设置为`"`时，trim_double_quotes 一定要设置为 true。
>
> 15. `<escape>`
>
>     转义符。用于转义在csv字段中出现的与包围符相同的字符。例如数据为"a,'b,'c'"，包围符为"'"，希望"b,'c被作为一个字段解析，则需要指定单字节转义符，例如 `\`，然后将数据修改为 `a,'b,\'c'`。
>
**5. `<data_source_properties>` 中的可选属性**

> 1. `<kafka_partitions>/<kafka_offsets>`
>
>     指定需要订阅的 kafka partition，以及对应的每个 partition 的起始 offset。如果指定时间，则会从大于等于该时间的最近一个 offset 处开始消费。
>
>     offset 可以指定从大于等于 0 的具体 offset，或者：
>
>     - `OFFSET_BEGINNING`: 从有数据的位置开始订阅。
>     - `OFFSET_END`: 从末尾开始订阅。
>     - 时间格式，如："2021-05-22 11:00:00"
>
>     如果没有指定，则默认从 `OFFSET_END` 开始订阅 topic 下的所有 partition。
>
>     ```text
>     "kafka_partitions" = "0,1,2,3",
>     "kafka_offsets" = "101,0,OFFSET_BEGINNING,OFFSET_END"
>     ```
>
>     ```text
>     "kafka_partitions" = "0,1,2,3",
>     "kafka_offsets" = "2021-05-22 11:00:00,2021-05-22 11:00:00,2021-05-22 11:00:00"
>     ```
>
>    注意，时间格式不能和 OFFSET 格式混用。
>
> 2. `<property>`
>
>     指定自定义 kafka 参数。功能等同于 kafka shell 中 "--property" 参数。
>
>     当参数的 value 为一个文件时，需要在 value 前加上关键词："FILE:"。
>
>     关于如何创建文件，请参阅 [CREATE FILE](../../security/CREATE-FILE) 命令文档。
>
>     更多支持的自定义参数，请参阅 librdkafka 的官方 CONFIGURATION 文档中，client 端的配置项。如：
>
>     ```text
>     "property.client.id" = "12345",
>     "property.ssl.ca.location" = "FILE:ca.pem"
>     ```
>
>     2.1 使用 SSL 连接 Kafka 时，需要指定以下参数：
>
>        ```text
>        "property.security.protocol" = "ssl",
>        "property.ssl.ca.location" = "FILE:ca.pem",
>        "property.ssl.certificate.location" = "FILE:client.pem",
>        "property.ssl.key.location" = "FILE:client.key",
>        "property.ssl.key.password" = "abcdefg"
>        ```
>
>        其中：
>
>        `property.security.protocol` 和 `property.ssl.ca.location` 为必须，用于指明连接方式为 SSL，以及 CA 证书的位置。
>
>        如果 Kafka server 端开启了 client 认证，则还需设置：
>
>        ```text
>        "property.ssl.certificate.location"
>        "property.ssl.key.location"
>        "property.ssl.key.password"
>        ```
>
>        分别用于指定 client 的 public key，private key 以及 private key 的密码。
>
>     2.2 指定 kafka partition 的默认起始 offset
>
>     如果没有指定 `kafka_partitions/kafka_offsets`，默认消费所有分区。
>
>     此时可以指定 `kafka_default_offsets` 指定起始 offset。默认为 `OFFSET_END`，即从末尾开始订阅。
>
>     示例：
>
>     ```text
>     "property.kafka_default_offsets" = "OFFSET_BEGINNING"
>     ```

**6. `<COMMENT>`**

>     例行导入任务的注释信息。

## 权限控制

执行此 SQL 命令的用户必须至少具有以下权限：

| 权限（Privilege） | 对象（Object） | 说明（Notes）                 |
| :---------------- | :------------- | :---------------------------- |
| LOAD_PRIV       | 表（Table）    | CREATE ROUTINE LOAD 属于表 LOAD 操作 |

## 注意事项

- 动态表不支持 `columns_mapping` 参数
- 使用动态多表时，merge_type、where_predicates 等参数需要符合每张动态表的要求
- 时间格式不能和 OFFSET 格式混用
- `kafka_partitions` 和 `kafka_offsets` 必须一一对应
- 当 `enclose` 设置为`"`时，`trim_double_quotes` 一定要设置为 true。

## 示例

- 为 example_db 的 example_tbl 创建一个名为 test1 的 Kafka 例行导入任务。指定列分隔符和 group.id 和 client.id，并且自动默认消费所有分区，且从有数据的位置（OFFSET_BEGINNING）开始订阅

   ```sql
   CREATE ROUTINE LOAD example_db.test1 ON example_tbl
   COLUMNS TERMINATED BY ",",
   COLUMNS(k1, k2, k3, v1, v2, v3 = k1 * 100)
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "property.group.id" = "xxx",
       "property.client.id" = "xxx",
       "property.kafka_default_offsets" = "OFFSET_BEGINNING"
   );
   ```

- 为 example_db 创建一个名为 test1 的 Kafka 例行动态多表导入任务。指定列分隔符和 group.id 和 client.id，并且自动默认消费所有分区， 
   且从有数据的位置（OFFSET_BEGINNING）开始订阅

  我们假设需要将 Kafka 中的数据导入到 example_db 中的 test1 以及 test2 表中，我们创建了一个名为 test1 的例行导入任务，同时将 test1 和 
  test2 中的数据写到一个名为 `my_topic` 的 Kafka 的 topic 中，这样就可以通过一个例行导入任务将 Kafka 中的数据导入到两个表中。

   ```sql
   CREATE ROUTINE LOAD example_db.test1
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "property.group.id" = "xxx",
       "property.client.id" = "xxx",
       "property.kafka_default_offsets" = "OFFSET_BEGINNING"
   );
   ```

- 为 example_db 的 example_tbl 创建一个名为 test1 的 Kafka 例行导入任务。导入任务为严格模式。

   ```sql
   CREATE ROUTINE LOAD example_db.test1 ON example_tbl
   COLUMNS(k1, k2, k3, v1, v2, v3 = k1 * 100),
   PRECEDING FILTER k1 = 1,
   WHERE k1 > 100 and k2 like "%doris%"
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "true"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "kafka_partitions" = "0,1,2,3",
       "kafka_offsets" = "101,0,0,200"
   );
   ```

-  通过 SSL 认证方式，从 Kafka 集群导入数据。同时设置 client.id 参数。导入任务为非严格模式，时区为 Africa/Abidjan

   ```sql
   CREATE ROUTINE LOAD example_db.test1 ON example_tbl
   COLUMNS(k1, k2, k3, v1, v2, v3 = k1 * 100),
   WHERE k1 > 100 and k2 like "%doris%"
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false",
       "timezone" = "Africa/Abidjan"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "property.security.protocol" = "ssl",
       "property.ssl.ca.location" = "FILE:ca.pem",
       "property.ssl.certificate.location" = "FILE:client.pem",
       "property.ssl.key.location" = "FILE:client.key",
       "property.ssl.key.password" = "abcdefg",
       "property.client.id" = "my_client_id"
   );
   ```

-  导入 Json 格式数据。默认使用 Json 中的字段名作为列名映射。指定导入 0,1,2 三个分区，起始 offset 都为 0

   ```sql
   CREATE ROUTINE LOAD example_db.test_json_label_1 ON table1
   COLUMNS(category,price,author)
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false",
       "format" = "json"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "kafka_partitions" = "0,1,2",
       "kafka_offsets" = "0,0,0"
   );
   ```

- 导入 Json 数据，并通过 Jsonpaths 抽取字段，并指定 Json 文档根节点

   ```sql
   CREATE ROUTINE LOAD example_db.test1 ON example_tbl
   COLUMNS(category, author, price, timestamp, dt=from_unixtime(timestamp, '%Y%m%d'))
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false",
       "format" = "json",
       "jsonpaths" = "[\"$.category\",\"$.author\",\"$.price\",\"$.timestamp\"]",
       "json_root" = "$.RECORDS"
       "strip_outer_array" = "true"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "kafka_partitions" = "0,1,2",
       "kafka_offsets" = "0,0,0"
   );
   ```

- 为 example_db 的 example_tbl 创建一个名为 test1 的 Kafka 例行导入任务。并且使用条件过滤。

   ```sql
   CREATE ROUTINE LOAD example_db.test1 ON example_tbl
   WITH MERGE
   COLUMNS(k1, k2, k3, v1, v2, v3),
   WHERE k1 > 100 and k2 like "%doris%",
   DELETE ON v3 >100
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "20",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200",
       "strict_mode" = "false"
   )
   FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "kafka_partitions" = "0,1,2,3",
       "kafka_offsets" = "101,0,0,200"
   );
   ```

- 导入数据到含有 sequence 列的 Unique Key 模型表中

   ```sql
   CREATE ROUTINE LOAD example_db.test_job ON example_tbl
   COLUMNS TERMINATED BY ",",
   COLUMNS(k1,k2,source_sequence,v1,v2),
   ORDER BY source_sequence
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "30",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200"
   ) FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092,broker3:9092",
       "kafka_topic" = "my_topic",
       "kafka_partitions" = "0,1,2,3",
       "kafka_offsets" = "101,0,0,200"
   );
   ```

- 从指定的时间点开始消费

   ```sql
   CREATE ROUTINE LOAD example_db.test_job ON example_tbl
   PROPERTIES
   (
       "desired_concurrent_number"="3",
       "max_batch_interval" = "30",
       "max_batch_rows" = "300000",
       "max_batch_size" = "209715200"
   ) FROM KAFKA
   (
       "kafka_broker_list" = "broker1:9092,broker2:9092",
       "kafka_topic" = "my_topic",
       "kafka_default_offsets" = "2021-05-21 10:00:00"
   );
   ```