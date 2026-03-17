---
# 文档元数据
api: ListTables
version: 2024-05-18
category: table-management
request_type: GET

tags: [table, list, query]
difficulty: beginner

related_apis:
  - GetTable

use_cases:
  - 查看工作空间所有表
  - 搜索表
---

# ListTables 获取表列表

## 概述

获取工作空间中的表列表，支持分页。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |

### 可选参数

| 参数 | 类型 | 位置 | 说明 | 默认值 |
|------|------|------|------|--------|
| `page_number` | integer | query | 页码 | 1 |
| `page_size` | integer | query | 每页数量 | 20 |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.ListTablesRequest(
    project_identifier='cw_123456',
    page_number=1,
    page_size=50
)
response = client.list_tables(request)

for table in response.body.data.tables:
    print(f"{table.table_name} - {table.table_comment}")
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "total_count": 200,
    "page_number": 1,
    "page_size": 50,
    "tables": [
      {
        "table_name": "user_info",
        "table_comment": "用户信息表",
        "table_type": "MANAGED_TABLE",
        "owner_id": "123456"
      }
    ]
  }
}
```

## 相关API

- [GetTable](GetTable.md) - 获取表详情
