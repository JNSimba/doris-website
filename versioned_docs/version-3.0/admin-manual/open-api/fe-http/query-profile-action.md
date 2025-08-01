---
{
    "title": "Query Profile Action",
    "language": "en"
}
---

## Request

`GET /rest/v2/manager/query/query_info`

`GET /rest/v2/manager/query/trace/{trace_id}`

`GET /rest/v2/manager/query/sql/{query_id}`

`GET /rest/v2/manager/query/profile/text/{query_id}`

`GET /rest/v2/manager/query/profile/graph/{query_id}`

`GET /rest/v2/manager/query/profile/json/{query_id}`

`GET /rest/v2/manager/query/profile/fragments/{query_id}`

`GET /rest/v2/manager/query/current_queries`

`GET /rest/v2/manager/query/kill/{query_id}`

## Get the query information

`GET /rest/v2/manager/query/query_info`

### Description

Gets information about select queries for all fe nodes in the cluster.

### Query parameters

* `query_id`

    Optional, specifies the query ID of the query to be returned, default returns information for all queries.
    
* `search`

    Optional, specifies that query information containing strings is returned, currently only string matches are performed.

* `is_all_node`
  
    Optional, if true, returns query information for all fe nodes, if false, returns query information for the current fe node. The default is true.


### Response

```json
{
   "msg": "success",  
    "code": 0,  
    "data": {  
        "column_names": [  
            "Query ID",  
            "FE Node",  
            "Query User",  
            "Execution Database",  
            "Sql",  
            "Query Type",  
            "Start Time",  
            "End Time",  
            "Execution Duration",  
            "Status"  
        ],  
        "rows": [  
            [  
                ...  
            ]  
        ]  
    },  
    "count": 0  
}
```

:::info Note

Since Doris Version 1.2, Admin and Root users can view all queries. Regular users can only view their own submitted queries.

:::



### Examples

```json
GET /rest/v2/manager/query/query_info

{
    "msg": "success",  
    "code": 0,  
    "data": {  
        "column_names": [  
            "Query ID",  
            "FE Node",  
            "Query User",  
            "Execution Database",  
            "Sql",  
            "Query Type",  
            "Start Time",  
            "End Time",  
            "Execution Duration",  
            "Status"  
        ],  
        "rows": [  
            [  
                "d7c93d9275334c35-9e6ac5f295a7134b",  
                "127.0.0.1:8030",  
                "root",  
                "default_cluster:testdb",  
                "select c.id, c.name, p.age, p.phone, c.date, c.cost from cost c join people p on c.id = p.id where p.age > 20 order by c.id",  
                "Query",  
                "2021-07-29 16:59:12",  
                "2021-07-29 16:59:12",  
                "109ms",  
                "EOF"  
            ]  
        ]  
    },  
    "count": 0  
}
```

## Get Query Id By Trace Id

`GET /rest/v2/manager/query/trace_id/{trace_id}`

### Description

Get query id by trance id.

Before executing a Query, set a unique trace id:

`set session_context="trace_id:your_trace_id";`

After executing the Query within the same Session, the query id can be obtained through the trace id.
    
### Path parameters

* `{trace_id}`

    User specific trace id.

### Query parameters

### Response

```json
{
    "msg": "success", 
    "code": 0, 
    "data": "fb1d9737de914af1-a498d5c5dec638d3", 
    "count": 0
}
```

:::note Info

Since Doris version 1.2, admin and root user can view all queries. Ordinary users can only view the Query sent by themselves. If the specified trace id does not exist or has no permission, it will return Bad Request:

```json
{
    "msg": "Bad Request", 
    "code": 403, 
    "data": "error messages",
    "count": 0
}
```
:::


## Get the sql and text profile for the specified query

`GET /rest/v2/manager/query/sql/{query_id}`

`GET /rest/v2/manager/query/profile/text/{query_id}`

### Description

Get the sql and profile text for the specified query id.
    
### Path parameters

* `query_id`

    The query id.

### Query parameters

* `is_all_node`
  
    Optional, if true then query for the specified query id in all fe nodes, if false then query for the specified query id in the currently connected fe nodes. The default is true.

### Response

```json
{
    "msg": "success",
    "code": 0,
    "data": {
        "sql": ""
    },
    "count": 0
}
```

```json
{
    "msg": "success",
    "code": 0,
    "data": {
        "profile": ""
    },
    "count": 0
}
```

:::note Info

Since Doris version 1.2, admin and root user can view all queries. Ordinary users can only view the Query sent by themselves. If the specified trace id does not exist or has no permission, it will return Bad Request:

```json
{
    "msg": "Bad Request", 
    "code": 403, 
    "data": "error messages",
    "count": 0
}
```

:::
    
### Examples

1. get sql.

    ```json
    GET /rest/v2/manager/query/sql/d7c93d9275334c35-9e6ac5f295a7134b
    
    Response:
    {
        "msg": "success",
        "code": 0,
        "data": {
            "sql": "select c.id, c.name, p.age, p.phone, c.date, c.cost from cost c join people p on c.id = p.id where p.age > 20 order by c.id"
        },
        "count": 0
    }
    ```

## Get the specified query fragment and instance information

`GET /rest/v2/manager/query/profile/fragments/{query_id}`

:::caution

Since 2.1.1, this API is deprecated. You can still download profile from http://<fe_ip>:<fe_http_port>/QueryProfile

:::

### Description

Get the fragment name, instance id, host ip/port and execution time for the specified query id.
    
### Path parameters

* `query_id`

    The query id.

### Query parameters

* `is_all_node`
  
    Optional, if true then query for the specified query id in all fe nodes, if false then query for the specified query id in the currently connected fe nodes. The default is true.

### Response

```json
{
    "msg": "success",
    "code": 0,
    "data": [
        {
            "fragment_id": "",
            "time": "",
            "instance_id": {
                "": {
                  "host": "",
                  "active_time": ""
                }
            }
        }
    ],
    "count": 0
}
```

:::note Info

Since Doris version 1.2, admin and root user can view all queries. Ordinary users can only view the Query sent by themselves. If the specified trace id does not exist or has no permission, it will return Bad Request:

```json
{
    "msg": "Bad Request", 
    "code": 403, 
    "data": "error messages",
    "count": 0
}
```
:::

    
### Examples

```json
GET /rest/v2/manager/query/profile/fragments/d7c93d9275334c35-9e6ac5f295a7134b

Response:
{
    "msg": "success",
    "code": 0,
    "data": [
        {
            "fragment_id": "0",
            "time": "36.169ms",
            "instance_id": {
                "d7c93d9275334c35-9e6ac5f295a7134e": {
                    "host": "172.19.0.4:9060",
                    "active_time": "36.169ms"
                }
            }
        },
        {
            "fragment_id": "1",
            "time": "20.710ms",
            "instance_id": {
                "d7c93d9275334c35-9e6ac5f295a7134c": {
                    "host": "172.19.0.5:9060",
                    "active_time": "20.710ms"
                }
            }
        },
        {
            "fragment_id": "2",
            "time": "7.83ms",
            "instance_id": {
                "d7c93d9275334c35-9e6ac5f295a7134d": {
                    "host": "172.19.0.6:9060",
                    "active_time": "7.83ms"
                },
                "d7c93d9275334c35-9e6ac5f295a7134f": {
                    "host": "172.19.0.7:9060",
                    "active_time": "10.873ms"
                }
            }
        }
    ],
    "count": 0
}
```

## Get the specified query id tree profile information

`GET /rest/v2/manager/query/profile/graph/{query_id}`

### Description

Get the tree profile information of the specified query id, same as `show query profile` command.
    
### Path parameters

* `query_id`

    The query id.

### Query parameters

* `fragment_id` and `instance_id`

    Optional, both parameters must be specified or not.  
    If both are not specified, a simple tree of profiles is returned, equivalent to `show query profile '/query_id'`;  
    If both are specified, a detailed profile tree is returned, which is equivalent to `show query profile '/query_id/fragment_id/instance_id'`.

* `is_all_node`
  
    Optional, if true then query information about the specified query id in all fe nodes, if false then query information about the specified query id in the currently connected fe nodes. The default is true.

### Response

```json
{
    "msg": "success",
    "code": 0,
    "data": {
        "graph":""
    },
    "count": 0
}
```

:::note Info

Since Doris version 1.2, admin and root user can view all queries. Ordinary users can only view the Query sent by themselves. If the specified trace id does not exist or has no permission, it will return Bad Request:

```json
{
    "msg": "Bad Request", 
    "code": 403, 
    "data": "error messages",
    "count": 0
}
```
:::



## Current running queries

`GET /rest/v2/manager/query/current_queries`

### Description

Same as `show proc "/current_query_stmts"`, return current running queries.
    
### Path parameters

### Query parameters

* `is_all_node`
  
    Optional. Return current running queries from all FE if set to true. Default is true.

### Response

```json
{
	"msg": "success",
	"code": 0,
	"data": {
		"columnNames": ["Frontend", "QueryId", "ConnectionId", "Database", "User", "ExecTime", "SqlHash", "Statement"],
		"rows": [
			["172.19.0.3", "108e47ab438a4560-ab1651d16c036491", "2", "", "root", "6074", "1a35f62f4b14b9d7961b057b77c3102f", "select sleep(60)"],
			["172.19.0.11", "3606cad4e34b49c6-867bf6862cacc645", "3", "", "root", "9306", "1a35f62f4b14b9d7961b057b77c3102f", "select sleep(60)"]
		]
	},
	"count": 0
}
```

## Cancel query

`POST /rest/v2/manager/query/kill/{query_id}`

### Description

Cancel query of specified connection.
    
### Path parameters

* `{query_id}`

    query id. You can get query id by `trance_id` api.

### Query parameters

### Response

```json
{
    "msg": "success",
    "code": 0,
    "data": null,
    "count": 0
}
```
