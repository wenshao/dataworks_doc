---
# 文档元数据
api: GetFile
version: 2024-05-18
category: file-management
request_type: GET

tags: [file, query, get]
difficulty: beginner

requires:
  - file_id

related_apis:
  - CreateFile
  - UpdateFile
  - ListFiles

use_cases:
  - 查询节点代码
  - 查询节点配置
  - 获取调度信息
---

# GetFile 获取文件详情

## 概述

获取指定文件的详细信息，包括代码、配置、调度等。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |
| `file_id` | string | query | 文件ID |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.GetFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456'
)
response = client.get_file(request)
file = response.body.data

print(f"文件名: {file.file_name}")
print(f"文件类型: {file.file_type}")
print(f"代码: {file.code}")
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "file_id": "file_123456",
    "file_name": "my_sql_task",
    "file_type": 10,
    "code": "SELECT * FROM users WHERE dt=\"${bizdate}\"",
    "content": null,
    "workflow_id": "wf_123",
    "description": "每日用户报表",
    "creator": "123456789",
    "create_time": "2025-01-01 00:00:00",
    "modify_time": "2025-01-02 00:00:00",
    "status": "OK"
  }
}
```

## 响应字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `file_id` | string | 文件ID |
| `file_name` | string | 文件名称 |
| `file_type` | integer | 文件类型 |
| `code` | string | 节点代码 |
| `content` | string | 节点配置（JSON） |
| `workflow_id` | string | 所属工作流ID |
| `description` | string | 文件描述 |
| `creator` | string | 创建者ID |
| `create_time` | string | 创建时间 |
| `modify_time` | string | 修改时间 |
| `status` | string | 文件状态 |

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `FileNotFound` | 文件不存在 | 检查 file_id |
| `Forbidden.NoPermission` | 没有权限 | 确认有文件读取权限 |

## 相关API

- [CreateFile](CreateFile.md) - 创建文件
- [UpdateFile](UpdateFile.md) - 修改文件
- [ListFiles](ListFiles.md) - 获取文件列表
- [DeleteFile](DeleteFile.md) - 删除文件
