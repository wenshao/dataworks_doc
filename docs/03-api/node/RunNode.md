---
# 文档元数据
api: RunNode
version: 2024-05-18
category: instance-management
request_type: POST

tags: [node, run, execute, instance]
difficulty: beginner

requires:
  - file_id
  - bizdate

produces:
  - instance_id

related_apis:
  - GetInstance
  - KillInstance
  - ListInstances

use_cases:
  - 手动运行节点
  - 补数据
  - 测试节点
---

# RunNode 运行节点

## 概述

手动触发节点运行，生成任务实例。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `file_id` | string | body | 文件ID |
| `biz_date` | string | body | 业务日期(YYYYMMDD) |

### 可选参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `node_parameters` | object | body | 节点参数 |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.RunNodeRequest(
    project_identifier='cw_123456',
    file_id='file_123456',
    biz_date='20250101'
)
response = client.run_node(request)
instance_id = response.body.data.instance_id
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "instance_id": "inst_123456",
    "status": "WAIT_RUNNING"
  }
}
```

## bizdate 说明

`bizdate` 是数据的业务日期，与实际运行日期不同：

| 场景 | 运行时间 | bizdate | 说明 |
|------|----------|---------|------|
| T+1 日处理 | 2025-01-02 02:00 | 20250101 | 处理昨天的数据 |
| 当日处理 | 2025-01-01 23:59 | 20250101 | 处理今天的数据 |
| 补历史数据 | 2025-01-15 10:00 | 20241201 | 补12月1日的数据 |

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `FileNotFound` | 文件不存在 | 检查 file_id |
| `FileNotDeployed` | 文件未发布 | 先调用 DeployFile |
| `InvalidParameter.BizDate` | 日期格式错误 | 使用 YYYYMMDD 格式 |

## 相关API

- [GetInstance](../instance/GetInstance.md) - 获取实例状态
- [ListInstances](../instance/ListInstances.md) - 获取实例列表
- [KillInstance](../instance/KillInstance.md) - 终止实例
