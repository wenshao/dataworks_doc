---
# 文档元数据
api: GetTable
version: 2024-05-18
category: table-management
request_type: GET

tags: [table, query, metadata]
difficulty: beginner

requires:
  - workspace_id
  - table_name

produces:
  - table_schema
  - column_list
  - partition_info

related_apis:
  - ListTables

use_cases:
  - 查询表结构
  - 获取列信息
  - 查看分区信息
---

# GetTable 获取表详情

## 概述

获取 MaxCompute 表的详细信息，包括列、分区、生命周期等。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |
| `table_name` | string | query | 表名 |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.GetTableRequest(
    project_identifier='cw_123456',
    table_name='user_info'
)
response = client.get_table(request)
table = response.body.data

print(f"表名: {table.table_name}")
print(f"列数: {len(table.columns)}")
for col in table.columns:
    print(f"  {col.name} ({col.type})")
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "table_name": "user_info",
    "table_comment": "用户信息表",
    "table_type": "MANAGED_TABLE",
    "table_size": 1024000,
    "row_count": 10000,
    "create_time": "2025-01-01 00:00:00",
    "modified_time": "2025-01-02 00:00:00",
    "owner_id": "123456",
    "life_cycle": 365,
    "is_partition": true,
    "partition_keys": [
      {"name": "dt", "type": "string", "comment": "日期分区"}
    ],
    "columns": [
      {"name": "user_id", "type": "bigint", "comment": "用户ID"},
      {"name": "user_name", "type": "string", "comment": "用户名"}
    ]
  }
}
```

## 响应字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `table_name` | string | 表名 |
| `table_comment` | string | 表注释 |
| `table_type` | string | 表类型 |
| `table_size` | long | 表大小(字节) |
| `row_count` | long | 行数 |
| `life_cycle` | integer | 生命周期(天) |
| `is_partition` | boolean | 是否分区表 |
| `partition_keys` | array | 分区键列表 |
| `columns` | array | 列信息列表 |

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `TableNotFound` | 表不存在 | 检查表名 |
| `Forbidden.NoPermission` | 没有权限 | 确认有表读取权限 |

## 相关API

- [ListTables](table/ListTables.md) - 获取表列表
