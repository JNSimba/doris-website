---
{
    "title": "Show Table Data Action",
    "language": "en"
}
---

# Show Table Data Action

## Request

`GET /api/show_table_data`

## Description

Used to get the data size of all tables in all databases under all internal catalog, or the data size of the specified database or table. Unit byte.
    
## Path parameters

NULL

## Query parameters

* `db`

    Optional. If specified, get the data size of the tables under the specified database.

* `table`

    Optional. If specified, get the data size of the specified table.

* `single_replica`

    Optional. If specified, get the data size of the single replica of the table.

## Request body

NULL

## Response

1. The data size of all tables in the specified database.

    ```
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":9024548244,
                "revenue0":0,
                "customer":1906421482
            }
        },
        "count":0
    }
    ```
    
2. The data size of the specified table of the specified db.

    ```
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":9024548244
            }
        },
        "count":0
    }
    ```

3. The data size of the single replica of the specified table of the specified db.

    ```
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":3008182748
            }
        },
        "count":0
    }
    ```
    
## Examples

1. The data size of all tables in the specified database.

    ```
    GET /api/show_table_data?db=tpch
    
    Response:
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":9024548244,
                "revenue0":0,
                "customer":1906421482
            }
        },
        "count":0
    }
    ```

2. The data size of the specified table of the specified db.

    ```
    GET /api/show_table_data?db=tpch&table=partsupp
        
    Response:
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":9024548244
            }
        },
        "count":0
    }
    ```
3. The data size of the single replica of the specified table of the specified db.

    ```
    GET /api/show_table_data?db=tpch&table=partsupp&single_replica=true
        
    Response:
    {
        "msg":"success",
        "code":0,
        "data":{
            "tpch":{
                "partsupp":3008182748
            }
        },
        "count":0
    }
    ```
