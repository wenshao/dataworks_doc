---
# 文档元数据
api: KillInstance
version: 2024-05-18
category: instance-management
request_type: POST

tags: [instance, kill, stop]
difficulty: beginner

requires:
  - instance_id

related_apis:
  - GetInstance
  - RunNode

use_cases:
  - 终止运行中的任务
  - 取消等待的任务
---

# KillInstance 终止实例

## 概述

终止正在运行或等待中的任务实例。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `instance_id` | string | body | 实例ID |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.KillInstanceRequest(
    project_identifier='cw_123456',
    instance_id='inst_123456'
)
response = client.kill_instance(request)
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "status": "SUCCESS"
  }
}
```

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InstanceNotFound` | 实例不存在 | 检查 instance_id |
| `InvalidStatus.Instance` | 实例状态不允许终止 | 已完成或已取消的实例 |

## 注意事项

1. 终止后不可恢复
2. 下游依赖实例也会被终止
3. 可手动重新运行

## 相关API

- [GetInstance](instance/GetInstance.md) - 获取实例状态
- [RunNode](node/RunNode.md) - 重新运行
