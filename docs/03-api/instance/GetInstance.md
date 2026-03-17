---
# 文档元数据
api: GetInstance
version: 2024-05-18
category: instance-management
request_type: GET

tags: [instance, query, status]
difficulty: beginner

requires:
  - instance_id

related_apis:
  - RunNode
  - ListInstances
  - KillInstance

use_cases:
  - 查询任务状态
  - 获取运行日志
  - 检查执行结果
---

# GetInstance 获取实例详情

## 概述

获取任务实例的详细信息，包括状态、开始时间、结束时间等。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |
| `instance_id` | string | query | 实例ID |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.GetInstanceRequest(
    project_identifier='cw_123456',
    instance_id='inst_123456'
)
response = client.get_instance(request)
instance = response.body.data

print(f"状态: {instance.status}")
print(f"开始时间: {instance.start_time}")
print(f"结束时间: {instance.end_time}")
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "instance_id": "inst_123456",
    "status": "SUCCESS",
    "start_time": "2025-01-01 02:00:00",
    "end_time": "2025-01-01 02:05:00",
    "biz_date": "20250101",
    "cycle_time": "2025-01-01 02:00:00",
    "dag_error_message": "",
    "file_id": "file_123456",
    "node_name": "my_sql_task"
  }
}
```

## 实例状态

| 状态 | 说明 |
|------|------|
| `WAIT_WAITING` | 等待调度时间到达 |
| `WAIT_RUNNING` | 等待上游节点完成 |
| `RUNNING` | 正在运行 |
| `SUCCESS` | 运行成功 |
| `FAILED` | 运行失败 |
| `CANCELLED` | 已取消 |

## 错误码

| 错误码 | 说明 |
|--------|------|
| `InstanceNotFound` | 实例不存在 |

## 相关API

- [RunNode](node/RunNode.md) - 运行节点
- [ListInstances](instance/ListInstances.md) - 获取实例列表
- [KillInstance](instance/KillInstance.md) - 终止实例
