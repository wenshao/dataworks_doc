---
# 文档元数据
id: api-index
category: API参考
---

# DataWorks OpenAPI 参考

## API 分类

### 文件管理 (File)

| API | 说明 | 操作 |
|-----|------|------|
| [CreateFile](file/CreateFile.md) | 创建文件/节点 | POST |
| [UpdateFile](file/UpdateFile.md) | 修改文件 | PUT |
| [GetFile](file/GetFile.md) | 获取文件详情 | GET |
| [ListFiles](file/ListFiles.md) | 获取文件列表 | GET |
| [DeleteFile](file/DeleteFile.md) | 删除文件 | DELETE |
| [DeployFile](file/DeployFile.md) | 提交发布 | POST |

### 节点管理 (Node)

| API | 说明 | 操作 |
|-----|------|------|
| [RunNode](node/RunNode.md) | 运行节点 | POST |

### 工作流管理 (Workflow)

| API | 说明 | 操作 |
|-----|------|------|
| [CreateWorkflow](workflow/CreateWorkflow.md) | 创建工作流 | POST |

### 实例管理 (Instance)

| API | 说明 | 操作 |
|-----|------|------|
| [GetInstance](instance/GetInstance.md) | 获取实例详情 | GET |
| [ListInstances](instance/ListInstances.md) | 获取实例列表 | GET |
| [KillInstance](instance/KillInstance.md) | 终止实例 | POST |

### 表管理 (Table)

| API | 说明 | 操作 |
|-----|------|------|
| [GetTable](table/GetTable.md) | 获取表详情 | GET |
| [ListTables](table/ListTables.md) | 获取表列表 | GET |

## 文件类型 (file_type)

| 值 | 类型 | 说明 |
|----|------|------|
| 10 | ODPS SQL | MaxCompute SQL |
| 21 | Shell | Shell 脚本 |
| 24 | Spark | Spark 任务 |
| 27 | 离线同步 | 数据同步 |
| 35 | 虚拟节点 | 依赖控制 |
| 36 | Python | Python 脚本 |
| 42 | 实时同步 | 实时同步 |

## 实例状态

| 状态 | 说明 |
|------|------|
| `WAIT_WAITING` | 等待调度时间 |
| `WAIT_RUNNING` | 等待上游完成 |
| `RUNNING` | 正在运行 |
| `SUCCESS` | 运行成功 |
| `FAILED` | 运行失败 |
| `CANCELLED` | 已取消 |

## 错误码速查

| 类别 | 常见错误码 |
|------|------------|
| 认证 | `InvalidAccessKey.NotFound`, `Forbidden.AccessKey` |
| 权限 | `Forbidden.RAM`, `Forbidden.User.NotInWorkspace` |
| 资源 | `FileNotFound`, `TableNotFound`, `Workspace.NotFound` |
| 参数 | `InvalidParameter`, `MissingParameter` |
| 限流 | `Throttling`, `Throttling.User`, `Throttling.Api` |

详细错误码见：[错误码速查](../05-reference/error-codes.md)

## 快速导航

| 我想... | 相关API |
|---------|---------|
| 创建节点 | [CreateFile](file/CreateFile.md) |
| 运行节点 | [RunNode](node/RunNode.md) |
| 查询状态 | [GetInstance](instance/GetInstance.md) |
| 查询表 | [GetTable](table/GetTable.md) |

## 使用流程

```
1. CreateFile     创建文件
2. UpdateFile     配置调度（可选）
3. DeployFile     提交发布
4. RunNode        运行节点
5. GetInstance    查询状态
```

## 外部资源

- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [官方文档](https://help.aliyun.com/zh/dataworks/developer-reference)
