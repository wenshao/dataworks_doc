---
# 文档元数据
api: ListFiles
version: 2024-05-18
category: file-management
request_type: GET

tags: [file, list, query]
difficulty: beginner

related_apis:
  - GetFile
  - CreateFile

use_cases:
  - 查看工作空间所有节点
  - 搜索特定类型的文件
---

# ListFiles 获取文件列表

## 概述

获取工作空间中的文件列表，支持分页和筛选。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | query | 工作空间ID |

### 可选参数

| 参数 | 类型 | 位置 | 说明 | 默认值 |
|------|------|------|------|--------|
| `page_number` | integer | query | 页码 | 1 |
| `page_size` | integer | query | 每页数量 | 20 |
| `file_type` | integer | query | 文件类型筛选 | - |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

# 获取第一页
request = dataworks_models.ListFilesRequest(
    project_identifier='cw_123456',
    page_number=1,
    page_size=50
)
response = client.list_files(request)

files = response.body.data.files
for f in files:
    print(f"{f.file_name} ({f.file_type})")
```

## 响应结果

```json
{
  "request_id": "abc123",
  "data": {
    "total_count": 150,
    "page_number": 1,
    "page_size": 50,
    "files": [
      {
        "file_id": "file_001",
        "file_name": "task1",
        "file_type": 10,
        "create_time": "2025-01-01 00:00:00"
      }
    ]
  }
}
```

## 响应字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| `total_count` | integer | 总文件数 |
| `page_number` | integer | 当前页码 |
| `page_size` | integer | 每页数量 |
| `files` | array | 文件列表 |

## 错误码

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidParameter.PageNumber` | 页码超出范围 | 检查页码 |

## 相关API

- [GetFile](GetFile.md) - 获取文件详情
- [CreateFile](CreateFile.md) - 创建文件
