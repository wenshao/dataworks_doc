---
# 文档元数据
api: ListInstances
version: 2024-05-18
category: instance-management
request_type: GET

tags: [instance, list, query]
difficulty: beginner

related_apis:
  - GetInstance
  - RunNode

use_cases:
  - 查看节点运行历史
  - 批量查询实例状态
---

# ListInstances 获取实例列表

## 概述

获取指定节点的运行实例列表。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |
| `node_id` | string | query | 节点ID |

### 可选参数

| 参数 | 类型 | 位置 | 说明 | 默认值 |
|------|------|------|------|--------|
| `page_number` | integer | query | 页码 | 1 |
| `page_size` | integer | query | 每页数量 | 20 |
| `start_date` | string | query | 开始日期 | - |
| `end_date` | string | query | 结束日期 | - |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.ListInstancesRequest(
    project_identifier='cw_123456',
    node_id='node_123456',
    page_number=1,
    page_size=10
)
response = client.list_instances(request)
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "total_count": 100,
    "instances": [
      {
        "instance_id": "inst_001",
        "status": "SUCCESS",
        "biz_date": "20250101",
        "start_time": "2025-01-01 02:00:00"
      }
    ]
  }
}
```

## 相关API

- [GetInstance](instance/GetInstance.md) - 获取实例详情
- [RunNode](node/RunNode.md) - 运行节点
