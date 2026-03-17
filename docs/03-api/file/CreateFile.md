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
produces:
  - file_id
related_apis:
  - UpdateFile
  - DeleteFile
  - GetFile
  - DeployFile
  - RunNode

# 使用场景
use_cases:
  - 创建SQL节点
  - 创建同步节点
  - 创建Shell脚本
  - 创建Python脚本

# 文件类型映射
file_types:
  10: ODPS SQL
  21: Shell
  24: Spark
  27: 离线同步
  35: 虚拟节点
  36: Python
  42: 实时同步
---

# CreateFile 创建文件

## 概述

创建一个新的文件或节点。文件是节点的存储形式，创建后需要提交发布才能运行。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `file_name` | string | body | 文件名称 |
| `file_type` | integer | body | 文件类型（见下方表格） |

### 可选参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `code` | string | body | 节点代码 |
| `content` | string | body | 节点配置（JSON字符串） |
| `workflow_id` | string | body | 所属工作流ID |
| `description` | string | body | 文件描述 |
| `parent_id` | string | body | 父目录ID |

## file_type 值说明

| 值 | 类型 | 说明 | code/content |
|----|------|------|-------------|
| 10 | ODPS SQL | MaxCompute SQL 任务 | code |
| 21 | Shell | Shell 脚本任务 | code |
| 24 | Spark | Spark 计算任务 | code |
| 27 | 离线同步 | 数据同步任务 | content |
| 35 | 虚拟节点 | 不执行计算 | 无需代码 |
| 36 | Python | Python 脚本任务 | code |
| 42 | 实时同步 | 实时数据同步 | content |

## 请求示例

### 创建 SQL 节点

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.CreateFileRequest(
    project_identifier='cw_123456',
    file_name='my_sql_task',
    file_type=10,
    code='SELECT * FROM users WHERE dt="${bizdate}"'
)
response = client.create_file(request)
file_id = response.body.data.file_id
```

### 创建同步节点

```python
import json

sync_config = {
    "type": "mysql",
    "source": {"datasource": "mysql_src", "table": "t1"},
    "target": {"datasource": "odps_dst", "table": "t1"}
}

request = dataworks_models.CreateFileRequest(
    project_identifier='cw_123456',
    file_name='sync_mysql_to_odps',
    file_type=27,
    content=json.dumps(sync_config, ensure_ascii=False)
)
response = client.create_file(request)
```

### 创建虚拟节点

```python
request = dataworks_models.CreateFileRequest(
    project_identifier='cw_123456',
    file_name='start_virtual',
    file_type=35
)
response = client.create_file(request)
```

## 响应结果

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

## 错误码

| 错误码 | HTTP状态 | 说明 | 解决方案 |
|--------|---------|------|----------|
| `InvalidParameter.FileName` | 400 | 文件名格式错误 | 使用字母、数字、下划线，2-128字符 |
| `InvalidParameter.FileType` | 400 | 文件类型不支持 | 检查 file_type 值 |
| `FileAlreadyExists` | 409 | 文件已存在 | 使用不同的文件名 |
| `Forbidden.User.NotInWorkspace` | 403 | 用户不在工作空间 | 加入工作空间 |
| `MissingParameter.Code` | 400 | 缺少代码参数 | SQL/Shell/Python 节点需要 code |
| `MissingParameter.Content` | 400 | 缺少配置参数 | 同步节点需要 content |

## 使用流程

```
CreateFile → UpdateFile(可选) → DeployFile → RunNode
```

## 相关API

- [UpdateFile](UpdateFile.md) - 修改文件
- [GetFile](GetFile.md) - 获取文件详情
- [ListFiles](ListFiles.md) - 获取文件列表
- [DeleteFile](DeleteFile.md) - 删除文件
- [DeployFile](DeployFile.md) - 提交发布

## 相关任务

- [创建 SQL 节点](../../02-tasks/develop-node/sql-node.md)
- [创建同步节点](../../02-tasks/sync-data/mysql-to-odps.md)
- [创建工作流](../../02-tasks/develop-node/workflow.md)
