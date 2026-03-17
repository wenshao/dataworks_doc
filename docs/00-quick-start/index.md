---
title: DataWorks 快速入门
id: quick-start
category: 快速入门
difficulty: 初级
time: 5分钟
tags: [入门, 快速开始]
---

# DataWorks 快速入门

欢迎！本指南将帮助你在 5 分钟内了解 DataWorks OpenAPI 的核心概念，并完成第一个 API 调用。

## 你将学到

- DataWorks OpenAPI 是什么
- 如何配置开发环境
- 如何调用第一个 API

## 核心概念（30秒）

| 概念 | 说明 | 类比 |
|------|------|------|
| **工作空间** | DataWorks 的基本组织单元 | 一个"项目" |
| **节点** | 数据开发的最小执行单元 | 一个"任务" |
| **工作流** | 节点的逻辑组织 | 一组"相关任务" |
| **实例** | 节点的一次执行记录 | 一次"运行记录" |

## 第一步：安装 SDK

```bash
# Python SDK
pip install alibabacloud-dataworks-public20240518
```

## 第二步：配置认证

```python
import os
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models

# 从环境变量读取（推荐）
config = open_api_models.Config(
    access_key_id=os.getenv('ALIYUN_ACCESS_KEY_ID'),
    access_key_secret=os.getenv('ALIYUN_ACCESS_KEY_SECRET'),
    endpoint='dataworks.cn-hangzhou.aliyuncs.com'  # 根据地域调整
)
client = Client(config)
```

## 第三步：第一个 API 调用

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

# 查询工作空间列表
request = dataworks_models.ListWorkspacesRequest(
    page_number=1,
    page_size=10
)

try:
    response = client.list_workspaces(request)
    for workspace in response.body.data.workspaces:
        print(f"工作空间: {workspace.name}, ID: {workspace.project_identifier}")
except Exception as e:
    print(f"调用失败: {e}")
```

## 下一步

| 任务 | 说明 | 链接 |
|------|------|------|
| 创建同步节点 | 将数据从 MySQL 同步到 MaxCompute | [教程](../02-tasks/sync-data/mysql-to-odps.md) |
| 查询数据表 | 获取表结构和元数据 | [教程](../02-tasks/manage-table/query-table.md) |
| 运行任务 | 手动运行已发布的节点 | [教程](../02-tasks/manage-instance/run-node.md) |

## 常见问题

**Q: 如何获取 AccessKey？**
A: 登录阿里云控制台 → 头像 → AccessKey 管理 → 创建 AccessKey

**Q: 调用失败怎么办？**
A: 检查：1) AccessKey 是否正确 2) 地域是否匹配 3) 权限是否足够

**Q: 免费额度是多少？**
A: 详见 [使用限制与配额](../05-reference/limits.md)

## 相关资源

- [完整 API 参考](../03-api/index.md)
- [错误码速查](../05-reference/error-codes.md)
- [核心概念详解](../01-concepts/index.md)
