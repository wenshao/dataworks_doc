---
# 文档元数据
api: DeleteFile
version: 2024-05-18
category: file-management
request_type: DELETE

tags: [file, delete]
difficulty: beginner

requires:
  - file_id

related_apis:
  - CreateFile
  - GetFile

use_cases:
  - 删除未使用的节点
  - 清理测试文件
---

# DeleteFile 删除文件

## 概述

删除指定的文件。已发布到生产的文件无法直接删除。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |
| `file_id` | string | query | 文件ID |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.DeleteFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456'
)
response = client.delete_file(request)
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "status": "OK"
  }
}
```

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `FileNotFound` | 文件不存在 | 检查 file_id |
| `FileAlreadyDeployed` | 文件已发布 | 先取消发布再删除 |
| `Forbidden.NoPermission` | 没有权限 | 确认有删除权限 |

## 注意事项

1. 已发布的文件需要先取消发布
2. 删除操作不可逆，请谨慎操作
3. 被其他节点依赖的文件需先解除依赖

## 相关API

- [CreateFile](CreateFile.md) - 创建文件
- [GetFile](GetFile.md) - 获取文件详情
