---
# 文档元数据
api: UpdateFile
version: 2024-05-18
category: file-management
request_type: PUT

tags: [file, update, modify]
difficulty: beginner

requires:
  - file_id
produces:
  - success_status

related_apis:
  - CreateFile
  - GetFile
  - DeleteFile

use_cases:
  - 修改节点代码
  - 配置调度参数
  - 修改文件描述
---

# UpdateFile 修改文件

## 概述

修改已创建文件的内容、调度配置等信息。

## 请求参数

### 必填参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `project_identifier` | string | body | 工作空间ID |
| `file_id` | string | body | 文件ID |

### 可选参数

| 参数 | 类型 | 位置 | 说明 |
|------|------|------|------|
| `code` | string | body | 新的节点代码 |
| `content` | string | body | 新的节点配置 |
| `description` | string | body | 文件描述 |
| `schedule_configuration` | object | body | 调度配置 |

## schedule_configuration 参数

| 参数 | 类型 | 说明 | 示例 |
|------|------|------|------|
| `cronExpression` | string | Cron表达式 | `00 02 * * * ? *` |
| `autoRerunTimes` | integer | 自动重试次数 | `3` |
| `autoRerunInterval` | integer | 重试间隔(秒) | `120` |
| `dependentType` | string | 依赖类型 | `USER_DEFINE` |

## 请求示例

### 修改节点代码

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.UpdateFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456',
    code='SELECT * FROM new_table WHERE dt="${bizdate}"'
)
response = client.update_file(request)
```

### 配置调度（每天凌晨2点）

```python
request = dataworks_models.UpdateFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456',
    schedule_configuration={
        "cronExpression": "00 02 * * * ? *",
        "autoRerunTimes": 3,
        "autoRerunInterval": 120
    }
)
response = client.update_file(request)
```

### 修改同步节点配置

```python
import json

new_config = {
    "type": "mysql",
    "source": {"datasource": "mysql_src", "table": "new_table"},
    "target": {"datasource": "odps_dst", "table": "new_table"}
}

request = dataworks_models.UpdateFileRequest(
    project_identifier='cw_123456',
    file_id='file_123456',
    content=json.dumps(new_config)
)
response = client.update_file(request)
```

## Cron 表达式格式

```
格式：秒 分 时 日 月 周 [年]

字段  范围    允许字符
秒    0-59    , - * /
分    0-59    , - * /
时    0-23    , - * /
日    1-31    , - * ? /
月    1-12    , - * /
周    1-7     , - * ? /
年    1970-2099  , - * /

示例：
00 02 * * * ? *      # 每天凌晨 2 点
00 */2 * * * ? *     # 每 2 小时
00 00 1 * * ? *      # 每月 1 号 0 点
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
| `InvalidParameter.CronExpression` | Cron表达式错误 | 检查格式 |
| `FileAlreadyDeployed` | 文件已发布无法修改 | 取消发布后修改 |

## 注意事项

1. 已发布的文件修改后需要重新提交
2. 调度配置修改后下个周期生效
3. 代码修改会影响后续运行

## 相关API

- [CreateFile](file/CreateFile.md) - 创建文件
- [GetFile](file/GetFile.md) - 获取文件详情
- [DeployFile](file/DeployFile.md) - 提交发布
