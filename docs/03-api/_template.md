---
# 文档元数据（大模型解析用）
api: CreateFile
version: 2024-05-18
category: file-management
request_type: POST

# 分类标签
tags: [file, create, node]
difficulty: beginner

# 关联关系
requires:
  - workspace_id
  - workflow_id (optional)
produces:
  - file_id
related_apis:
  - UpdateFile
  - DeleteFile
  - GetFile
  - DeployFile

# 使用场景
use_cases:
  - 创建SQL节点
  - 创建同步节点
  - 创建Shell脚本
  - 创建Python脚本

# 变更历史
changes:
  - version: 2024-05-18
    change: 初始版本
    deprecated: false
---

# CreateFile

## 一句话描述

创建一个新的文件或节点，支持多种类型的开发任务。

## 使用场景

| 场景 | 描述 | file_type |
|------|------|-----------|
| 创建 SQL 节点 | 开发 MaxCompute SQL 任务 | 10 |
| 创建同步节点 | 配置数据同步任务 | 27 |
| 创建 Shell 节点 | 编写 Shell 脚本 | 21 |
| 创建 Python 节点 | 编写 Python 脚本 | 36 |
| 创建虚拟节点 | 用于依赖控制 | 35 |

## 请求参数

### 必填参数

| 参数 | 类型 | 说明 | 示例值 |
|------|------|------|--------|
| `project_identifier` | string | 工作空间ID | `cw_123456` |
| `file_name` | string | 文件名称 | `my_sql_task` |
| `file_type` | integer | 文件类型，详见上方表格 | `10` |

### 可选参数

| 参数 | 类型 | 说明 | 示例值 |
|------|------|------|--------|
| `code` | string | 节点代码（file_type=10/21/36 时必填） | `SELECT 1` |
| `content` | string | 节点配置（file_type=27 时必填） | `{"source": {...}}` |
| `workflow_id` | string | 所属工作流ID | `wf_123` |
| `description` | string | 文件描述 | `我的SQL任务` |
| `parent_id` | string | 父目录ID | `root` |

## 响应结果

### 成功响应

```json
{
  "request_id": "abc123",
  "data": {
    "file_id": "file_123456",
    "file_name": "my_sql_task",
    "file_type": 10,
    "status": "OK"
  }
}
```

### 字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `file_id` | string | 文件ID，后续操作的唯一标识 |
| `file_name` | string | 文件名称 |
| `file_type` | integer | 文件类型 |
| `status` | string | 创建状态 |

## 代码示例

### Python

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def create_sql_node(workspace_id: str, node_name: str, sql: str) -> str:
    """创建 SQL 节点

    Args:
        workspace_id: 工作空间ID
        node_name: 节点名称
        sql: SQL代码

    Returns:
        文件ID
    """
    request = dataworks_models.CreateFileRequest(
        project_identifier=workspace_id,
        file_name=node_name,
        file_type=10,  # ODPS SQL
        code=sql
    )
    response = client.create_file(request)
    return response.body.data.file_id

# 使用示例
file_id = create_sql_node(
    workspace_id='cw_123456',
    node_name='daily_report',
    sql='SELECT * FROM users WHERE dt="${bizdate}"'
)
print(f"节点创建成功: {file_id}")
```

### cURL

```bash
curl -X POST "https://dataworks.cn-hangzhou.aliyuncs.com/" \\
  -H "Content-Type: application/json" \\
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \\
  -d '{
    "action": "CreateFile",
    "version": "2024-05-18",
    "project_identifier": "cw_123456",
    "file_name": "my_sql_task",
    "file_type": 10,
    "code": "SELECT 1"
  }'
```

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidParameter.FileName` | 文件名格式错误 | 使用字母、数字、下划线 |
| `InvalidParameter.FileType` | 文件类型不支持 | 检查 file_type 值 |
| `FileAlreadyExists` | 文件已存在 | 使用不同的文件名 |
| `Forbidden.User.NotInWorkspace` | 用户不在工作空间 | 加入工作空间 |
| `MissingParameter.Code` | 缺少代码参数 | 提供 code 或 content |

## 最佳实践

1. **命名规范**
   - 使用小写字母、数字、下划线
   - 建议格式：`{层}_{操作}_{表名}`
   - 示例：`ods_extract_user`, `dwd_clean_log`

2. **代码注释**
   - 在 SQL 中添加业务逻辑说明
   - 标注重要计算逻辑

3. **参数使用**
   - 使用 `${bizdate}` 表示业务日期
   - 使用 `${node_id}` 引用节点自身

## 注意事项

1. `file_type` 值必须正确，否则无法创建
2. SQL 节点需要提供 `code`，同步节点需要提供 `content`
3. 文件名在同一工作流内不能重复
4. 创建后需要调用 `DeployFile` 才能运行

## 相关API

- [UpdateFile](file/UpdateFile.md) - 修改文件
- [GetFile](file/GetFile.md) - 获取文件详情
- [DeleteFile](file/DeleteFile.md) - 删除文件
- [DeployFile](file/DeployFile.md) - 提交发布
- [RunNode](node/RunNode.md) - 运行节点

## 相关任务

- [创建 SQL 节点](../02-tasks/develop-node/sql-node.md)
- [创建工作流](../02-tasks/develop-node/workflow.md)
- [创建同步任务](../02-tasks/sync-data/mysql-to-odps.md)
