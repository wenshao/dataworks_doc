---
title: 创建 SQL 节点
id: task-create-sql-node
category: 节点开发
difficulty: 初级
time: 10分钟
prerequisites:
  - 已有工作空间
  - 熟悉 SQL 语法
apis:
  - CreateFile
  - UpdateFile
  - DeployFile
related:
  - 创建 Shell 节点
  - 创建工作流
tags: [node, sql, development]
---

# 创建 SQL 节点

本教程演示如何创建一个 ODPS SQL 节点。

## 场景说明

| 项目 | 说明 |
|------|------|
| **节点类型** | ODPS SQL |
| **功能** | 数据清洗和转换 |
| **调度** | 每日执行一次 |

## 步骤一：创建 SQL 节点

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

# SQL 代码
sql_code = """
-- 清洗用户行为数据
INSERT OVERWRITE TABLE dwd_user_log_di
PARTITION (dt='${bizdate}')
SELECT
    user_id,
    behavior_type,
    COUNT(*) AS behavior_count,
    MAX(device_type) AS device_type,
    '${bizdate}' AS data_date
FROM ods_user_log
WHERE dt = '${bizdate}'
  AND user_id IS NOT NULL
GROUP BY user_id, behavior_type;
"""

# 创建节点
request = dataworks_models.CreateFileRequest(
    project_identifier='your_workspace_id',
    file_name='clean_user_log',
    file_type='10',  # 10 = ODPS SQL
    code=sql_code
)

response = client.create_file(request)
file_id = response.body.data.file_id
print(f"节点创建成功，文件ID: {file_id}")
```

## 步骤二：配置调度参数

```python
# 配置每天凌晨 3 点执行
request = dataworks_models.UpdateFileRequest(
    project_identifier='your_workspace_id',
    file_id=file_id,
    schedule_configuration={
        "cronExpression": "00 03 * * * ? *",  # 每天 03:00
        "autoRerunTimes": 3,
        "autoRerunInterval": 120,
        "dependentType": "USER_DEFINE",
        "parameters": {
            "bizdate": "$bizdate"  # 业务日期参数
        }
    }
)

response = client.update_file(request)
print("调度配置成功")
```

## 步骤三：提交发布

```python
request = dataworks_models.DeployFileRequest(
    project_identifier='your_workspace_id',
    file_id=file_id
)

response = client.deploy_file(request)
print("节点已提交到生产环境")
```

## 常用调度参数

| 参数 | 说明 | 示例值 |
|------|------|--------|
| `${bizdate}` | 业务日期 | 20250101 |
| `${cyctime}` | 调度时间 | 2025-01-01 00:00:00 |
| `${node_id}` | 节点ID | 节点的唯一标识 |

## 调度 Cron 表达式

```
格式：秒 分 时 日 月 周 [年]

示例：
00 02 * * * ? *      # 每天凌晨 2 点
00 */2 * * * ? *     # 每 2 小时执行一次
00 00 1 * * ? *      # 每月 1 号 0 点
```

## 完整代码

```python
from typing import Optional
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class SQLNodeManager:
    """SQL 节点管理"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def create(self, workspace_id: str, name: str, sql: str,
               cron: str = "00 02 * * * ? *") -> str:
        """创建 SQL 节点"""
        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=name,
            file_type='10',  # ODPS SQL
            code=sql
        )
        response = self.client.create_file(request)
        file_id = response.body.data.file_id

        # 配置调度
        self._schedule(workspace_id, file_id, cron)

        return file_id

    def _schedule(self, workspace_id: str, file_id: str, cron: str):
        """配置调度"""
        request = dataworks_models.UpdateFileRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            schedule_configuration={
                "cronExpression": cron,
                "autoRerunTimes": 3,
                "autoRerunInterval": 120
            }
        )
        self.client.update_file(request)

    def deploy(self, workspace_id: str, file_id: str):
        """提交节点"""
        request = dataworks_models.DeployFileRequest(
            project_identifier=workspace_id,
            file_id=file_id
        )
        self.client.deploy_file(request)

    def run(self, workspace_id: str, file_id: str, bizdate: str) -> str:
        """运行节点"""
        request = dataworks_models.RunNodeRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data.instance_id

# 使用示例
manager = SQLNodeManager(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 创建节点
sql = """
INSERT OVERWRITE TABLE target_table
PARTITION (dt='${bizdate}')
SELECT * FROM source_table WHERE dt = '${bizdate}'
"""

file_id = manager.create(
    workspace_id='your_workspace_id',
    name='my_sql_task',
    sql=sql,
    cron='00 03 * * * ? *'
)

# 提交
manager.deploy('your_workspace_id', file_id)

# 运行
instance_id = manager.run('your_workspace_id', file_id, '20250101')
```

## 相关教程

- [创建 Shell 节点](shell-node.md)
- [创建工作流](workflow.md)
- [运行节点](../manage-instance/run-node.md)
