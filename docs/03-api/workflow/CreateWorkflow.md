---
# 文档元数据
api: CreateWorkflow
version: 2024-05-18
category: workflow-management
request_type: POST

tags: [workflow, create]
difficulty: beginner

requires:
  - workspace_id

produces:
  - workflow_id

related_apis:
  - UpdateWorkflow
  - GetWorkflow
  - DeleteWorkflow
  - ListWorkflows

use_cases:
  - 创建数据处理流程
  - 组织多个节点
---

# CreateWorkflow 创建工作流

## 概述

创建一个新的工作流，用于组织多个相关节点。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `workflow_name` | string | body | 工作流名称 |

### 可选参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `description` | string | body | 工作流描述 |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.CreateWorkflowRequest(
    project_identifier='cw_123456',
    workflow_name='daily_data_pipeline',
    description='每日数据处理流程'
)
response = client.create_workflow(request)
workflow_id = response.body.data.workflow_id
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "workflow_id": "wf_123456",
    "workflow_name": "daily_data_pipeline",
    "description": "每日数据处理流程",
    "create_time": "2025-01-01 00:00:00"
  }
}
```

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `WorkflowAlreadyExists` | 工作流已存在 | 使用不同的名称 |
| `InvalidParameter.WorkflowName` | 名称格式错误 | 2-128字符 |

## 相关API

- [CreateFile](../file/CreateFile.md) - 创建节点
- [UpdateNode](../node/UpdateNode.md) - 配置节点依赖
