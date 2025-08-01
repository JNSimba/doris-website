---
{
    "title": "SHOW ROUTINE LOAD TASK",
    "language": "en"
}
---

## Description

This syntax is used to view the currently running subtasks of a specified Routine Load job.

## Syntax

```sql
SHOW ROUTINE LOAD TASK WHERE JobName = <job_name>;
```

## Required Parameters

**1. `<job_name>`**

> The name of the routine load job to view.

## Return Results

The return results include the following fields:

| Field Name           | Description                                                  |
| :------------------- | :---------------------------------------------------------- |
| TaskId               | Unique ID of the subtask                                     |
| TxnId                | Import transaction ID corresponding to the subtask           |
| TxnStatus            | Import transaction status of the subtask. Null indicates the subtask has not yet been scheduled |
| JobId                | Job ID corresponding to the subtask                          |
| CreateTime           | Creation time of the subtask                                 |
| ExecuteStartTime     | Time when the subtask was scheduled for execution, typically later than creation time |
| Timeout              | Subtask timeout, typically twice the `max_batch_interval` set in the job |
| BeId                 | BE node ID executing this subtask                            |
| DataSourceProperties | Starting offset of Kafka Partition that the subtask is preparing to consume. It's a Json format string. Key is Partition Id, Value is the starting offset for consumption |

## Access Control Requirements

Users executing this SQL command must have at least the following privileges:

| Privilege | Object | Notes |
| :-------- | :----- | :---- |
| LOAD_PRIV | Table | SHOW ROUTINE LOAD TASK requires LOAD privilege on the table |

## Notes

- A null TxnStatus doesn't indicate task error, it may mean the task hasn't been scheduled yet
- The offset information in DataSourceProperties can be used to track data consumption progress
- When Timeout is reached, the task will automatically end regardless of whether data consumption is complete

## Examples

- Show subtask information for a routine load task named test1.

    ```sql
    SHOW ROUTINE LOAD TASK WHERE JobName = "test1";
    ```