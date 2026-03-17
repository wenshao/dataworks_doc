---
title: 第一个 API 调用
id: first-api-call
category: 快速入门
difficulty: 初级
time: 3分钟
prerequisites:
  - 已有阿里云账号
  - 已开通 DataWorks
apis:
  - ListWorkspaces
tags: [入门, API调用]
related:
  - 核心概念
  - 错误码速查
---

# 第一个 API 调用

本教程将通过一个完整的示例，展示如何调用 DataWorks OpenAPI。

## 完整示例

```python
#!/usr/bin/env python3
"""
DataWorks OpenAPI 第一个调用示例
功能：查询工作空间列表
"""

import os
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class DataWorksClient:
    """DataWorks API 客户端封装"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def list_workspaces(self, page_num: int = 1, page_size: int = 10):
        """查询工作空间列表"""
        request = dataworks_models.ListWorkspacesRequest(
            page_number=page_num,
            page_size=page_size
        )
        response = self.client.list_workspaces(request)
        return response.body.data

def main():
    # 从环境变量获取认证信息（推荐方式）
    access_key_id = os.getenv('ALIYUN_ACCESS_KEY_ID')
    access_key_secret = os.getenv('ALIYUN_ACCESS_KEY_SECRET')

    if not access_key_id or not access_key_secret:
        print("错误：请设置环境变量 ALIYUN_ACCESS_KEY_ID 和 ALIYUN_ACCESS_KEY_SECRET")
        return

    # 创建客户端
    dw = DataWorksClient(access_key_id, access_key_secret)

    # 调用 API
    try:
        result = dw.list_workspaces()

        print(f"找到 {len(result.workspaces)} 个工作空间：\n")
        for ws in result.workspaces:
            print(f"  名称: {ws.name}")
            print(f"  ID: {ws.project_identifier}")
            print(f"  状态: {ws.status}")
            print()

    except Exception as e:
        print(f"API 调用失败: {e}")

if __name__ == '__main__':
    main()
```

## 运行示例

```bash
# 设置环境变量
export ALIYUN_ACCESS_KEY_ID="your_access_key_id"
export ALIYUN_ACCESS_KEY_SECRET="your_access_key_secret"

# 运行脚本
python first_api_call.py
```

## 预期输出

```
找到 3 个工作空间：

  名称: my_workspace
  ID: cw_123456
  状态: NORMAL

  名称: data_warehouse
  ID: cw_789012
  状态: NORMAL

  名称: test_env
  ID: cw_345678
  状态: NORMAL
```

## 错误处理

```python
from alibabacloud_tea_util import models as util_models

try:
    result = dw.list_workspaces()
except util_models.TeaException as e:
    # API 返回的业务错误
    print(f"错误码: {e.code}")
    print(f"错误信息: {e.message}")
    print(f"请求ID: {e.data.get('RequestId', '')}")
except Exception as e:
    # 其他异常（网络错误等）
    print(f"系统错误: {str(e)}")
```

## 常见错误

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| InvalidAccessKey.NotFound | AccessKey 不存在 | 检查 AccessKey 是否正确 |
| Forbidden.AccessKey | AccessKey 被禁用 | 检查账号状态 |
| Forbidden.RAM | 没有权限 | 需要授予 `AliyunDataWorksFullAccess` 权限 |
| Throttling | 触发限流 | 降低调用频率 |

## 下一步

掌握了基本调用方式后，可以尝试：

- [创建同步节点](../02-tasks/sync-data/mysql-to-odps.md)
- [查询数据表](../02-tasks/manage-table/query-table.md)
- [运行任务实例](../02-tasks/manage-instance/run-node.md)
