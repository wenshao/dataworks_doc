---
title: API 参考
id: api-index
category: API参考
tags: [api, reference]
---

# DataWorks OpenAPI 参考

## API 分类

### 文件管理

| API | 说明 | 请求方式 |
|-----|------|----------|
| [CreateFile](file/CreateFile.md) | 创建文件/节点 | POST |
| [UpdateFile](file/UpdateFile.md) | 修改文件/节点 | PUT |
| [DeleteFile](file/DeleteFile.md) | 删除文件/节点 | DELETE |
| [GetFile](file/GetFile.md) | 获取文件详情 | GET |
| [ListFiles](file/ListFiles.md) | 获取文件列表 | GET |
| [DeployFile](file/DeployFile.md) | 提交发布 | POST |

### 节点管理

| API | 说明 | 请求方式 |
|-----|------|----------|
| [CreateNode](node/CreateNode.md) | 创建节点 | POST |
| [UpdateNode](node/UpdateNode.md) | 修改节点 | PUT |
| [DeleteNode](node/DeleteNode.md) | 删除节点 | DELETE |
| [GetNode](node/GetNode.md) | 获取节点详情 | GET |
| [ListNodes](node/ListNodes.md) | 获取节点列表 | GET |
| [RunNode](node/RunNode.md) | 运行节点 | POST |

### 工作流管理

| API | 说明 | 请求方式 |
|-----|------|----------|
| [CreateWorkflow](workflow/CreateWorkflow.md) | 创建工作流 | POST |
| [UpdateWorkflow](workflow/UpdateWorkflow.md) | 修改工作流 | PUT |
| [DeleteWorkflow](workflow/DeleteWorkflow.md) | 删除工作流 | DELETE |
| [GetWorkflow](workflow/GetWorkflow.md) | 获取工作流详情 | GET |
| [ListWorkflows](workflow/ListWorkflows.md) | 获取工作流列表 | GET |

### 实例管理

| API | 说明 | 请求方式 |
|-----|------|----------|
| [GetInstance](instance/GetInstance.md) | 获取实例详情 | GET |
| [ListInstances](instance/ListInstances.md) | 获取实例列表 | GET |
| [KillInstance](instance/KillInstance.md) | 终止实例 | POST |
| [SuspendInstance](instance/SuspendInstance.md) | 暂停实例 | POST |
| [ResumeInstance](instance/ResumeInstance.md) | 恢复实例 | POST |

### 表管理

| API | 说明 | 请求方式 |
|-----|------|----------|
| [GetTable](table/GetTable.md) | 获取表详情 | GET |
| [ListTables](table/ListTables.md) | 获取表列表 | GET |
| [CreateTable](table/CreateTable.md) | 创建表 | POST |

### 同步任务

| API | 说明 | 请求方式 |
|-----|------|----------|
| [CreateSyncTask](sync/CreateSyncTask.md) | 创建同步任务 | POST |
| [UpdateSyncTask](sync/UpdateSyncTask.md) | 修改同步任务 | PUT |
| [DeleteSyncTask](sync/DeleteSyncTask.md) | 删除同步任务 | DELETE |
| [RunSyncTask](sync/RunSyncTask.md) | 运行同步任务 | POST |

## 请求格式

所有 API 遵循 RPC 风格，请求格式如下：

```python
# 统一请求格式
request = dataworks_models.{ApiName}Request(
    project_identifier='workspace_id',  # 工作空间ID
    # ... 其他参数
)

response = client.{api_name}(request)
result = response.body.data
```

## 通用响应结构

```python
{
    "request_id": "xxx",      # 请求ID，用于问题排查
    "data": {                 # 返回数据
        # 具体业务数据
    }
}
```

## 错误码

详见 [错误码速查](../05-reference/error-codes.md)

## 快速导航

- 按任务查找：[任务教程](../02-tasks/)
- 按场景查找：[实战指南](../04-guides/)
- 错误排查：[错误码速查](../05-reference/error-codes.md)
