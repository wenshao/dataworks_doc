---
# 文档元数据
api: DeployFile
version: 2024-05-18
category: file-management
request_type: POST

tags: [file, deploy, publish]
difficulty: beginner

requires:
  - file_id

related_apis:
  - CreateFile
  - UpdateFile
  - RunNode

use_cases:
  - 提交节点到生产环境
  - 发布工作流
---

# DeployFile 提交发布

## 概述

将开发环境的文件提交发布到生产环境。发布后才能运行。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `file_id` | string | body | 文件ID |

## 请求示例

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.DeployFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456'
)
response = client.deploy_file(request)
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
| `FileNotFound` | 文件不存在 | 检查 file_id |
| `ValidationError` | 文件校验失败 | 检查代码语法 |
| `Forbidden.NoPermission` | 没有权限 | 确认有发布权限 |

## 使用流程

```
CreateFile → UpdateFile(可选) → DeployFile → RunNode
```

## 注意事项

1. 发布前会进行语法校验
2. 发布后需要重新发布才能更新
3. 发布操作不可逆

## 相关API

- [CreateFile](file/CreateFile.md) - 创建文件
- [RunNode](node/RunNode.md) - 运行节点
