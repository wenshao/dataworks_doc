---
title: MySQL 数据同步到 MaxCompute
id: task-sync-mysql-odps
category: 数据同步
difficulty: 初级
time: 10分钟
prerequisites:
  - 已创建工作空间
  - 已配置 MySQL 数据源
  - 已配置 MaxCompute 数据源
apis:
  - CreateFile
  - UpdateFile
  - DeployFile
  - RunNode
related:
  - Oracle 同步到 MaxCompute
  - 实时数据同步
tags: [sync, mysql, odps]
---

# MySQL 数据同步到 MaxCompute

本教程演示如何创建一个离线同步任务，将数据从 MySQL 同步到 MaxCompute。

## 场景说明

| 项目 | 说明 |
|------|------|
| **源** | MySQL 数据库 |
| **目标** | MaxCompute 表 |
| **同步方式** | 离线全量/增量 |
| **执行频率** | 每日一次 |

## 前置条件

- [ ] 已有 MySQL 数据源，名称为 `mysql_source`
- [ ] 已有 MaxCompute 项目，名称为 `odps_project`
- [ ] MySQL 源表：`user_log`
- [ ] MaxCompute 目标表：`ods_user_log` (分区表，分区字段 `dt`)

## 步骤一：创建同步节点

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models
import json

# 构建同步配置
sync_config = {
    "type": "mysql",
    "source": {
        "datasource": "mysql_source",
        "table": "user_log",
        "where": "create_time >= '${bizdate}'"  # 增量条件
    },
    "target": {
        "datasource": "odps_project",
        "table": "ods_user_log",
        "partition": "dt='${bizdate}'",
        "writeMode": "append"
    },
    "speed": {
        "mb": "10"  # 限速 10MB/s
    }
}

# 创建同步节点
request = dataworks_models.CreateFileRequest(
    project_identifier='your_workspace_id',
    file_name='sync_mysql_to_odps',
    file_type='27',  # 27 = 离线同步节点
    content=json.dumps(sync_config, ensure_ascii=False)
)

response = client.create_file(request)
file_id = response.body.data.file_id
print(f"节点创建成功，文件ID: {file_id}")
```

## 步骤二：配置调度

```python
# 配置每天凌晨 2 点执行
request = dataworks_models.UpdateFileRequest(
    project_identifier='your_workspace_id',
    file_id=file_id,
    schedule_configuration={
        "cronExpression": "00 02 * * * ? *",
        "autoRerunTimes": 3,
        "autoRerunInterval": 120,
        "dependentType": "USER_DEFINE"
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

## 步骤四：手动运行测试

```python
# 运行节点，指定业务日期
request = dataworks_models.RunNodeRequest(
    project_identifier='your_workspace_id',
    file_id=file_id,
    biz_date='20250101'  # 格式：YYYYMMDD
)

response = client.run_node(request)
instance_id = response.body.data.instance_id
print(f"任务已提交，实例ID: {instance_id}")
```

## 步骤五：查看运行状态

```python
import time

def wait_for_complete(client, workspace_id, instance_id, timeout=3600):
    """等待任务完成"""
    start_time = time.time()

    while time.time() - start_time < timeout:
        request = dataworks_models.GetInstanceRequest(
            project_identifier=workspace_id,
            instance_id=instance_id
        )
        response = client.get_instance(request)
        status = response.body.data.status

        if status == 'SUCCESS':
            print("任务执行成功！")
            return True
        elif status == 'FAILED':
            print(f"任务执行失败: {response.body.data.dag_error_message}")
            return False

        print(f"任务运行中... 状态: {status}")
        time.sleep(30)  # 每 30 秒检查一次

    print("任务超时")
    return False

# 使用
wait_for_complete(client, 'your_workspace_id', instance_id)
```

## 完整代码

```python
import json
import time
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class MySQLToODPSSync:
    """MySQL 到 MaxCompute 同步任务管理"""

    def __init__(self, workspace_id, mysql_ds, odps_ds):
        self.workspace_id = workspace_id
        self.mysql_ds = mysql_ds
        self.odps_ds = odps_ds
        self.client = self._create_client()

    def _create_client(self):
        """创建 API 客户端"""
        # 从环境变量读取
        import os
        config = open_api_models.Config(
            access_key_id=os.getenv('ALIYUN_ACCESS_KEY_ID'),
            access_key_secret=os.getenv('ALIYUN_ACCESS_KEY_SECRET'),
            endpoint='dataworks.cn-hangzhou.aliyuncs.com'
        )
        return Client(config)

    def create(self, name, source_table, target_table):
        """创建同步节点"""
        config = {
            "type": "mysql",
            "source": {"datasource": self.mysql_ds, "table": source_table},
            "target": {
                "datasource": self.odps_ds,
                "table": target_table,
                "partition": "dt='${bizdate}'"
            }
        }

        request = dataworks_models.CreateFileRequest(
            project_identifier=self.workspace_id,
            file_name=name,
            file_type='27',
            content=json.dumps(config, ensure_ascii=False)
        )
        response = self.client.create_file(request)
        return response.body.data.file_id

    def schedule(self, file_id, cron="00 02 * * * ? *"):
        """配置调度"""
        request = dataworks_models.UpdateFileRequest(
            project_identifier=self.workspace_id,
            file_id=file_id,
            schedule_configuration={"cronExpression": cron}
        )
        self.client.update_file(request)

    def deploy(self, file_id):
        """提交发布"""
        request = dataworks_models.DeployFileRequest(
            project_identifier=self.workspace_id,
            file_id=file_id
        )
        self.client.deploy_file(request)

    def run(self, file_id, bizdate):
        """运行任务"""
        request = dataworks_models.RunNodeRequest(
            project_identifier=self.workspace_id,
            file_id=file_id,
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data.instance_id

# 使用示例
sync = MySQLToODPSSync(
    workspace_id='your_workspace_id',
    mysql_ds='mysql_source',
    odps_ds='odps_project'
)

# 创建、配置、提交、运行
file_id = sync.create('sync_user', 'user_log', 'ods_user_log')
sync.schedule(file_id)
sync.deploy(file_id)
instance_id = sync.run(file_id, '20250101')
```

## 常见问题

**Q: 如何只同步增量数据？**
A: 在 `source.where` 中配置时间条件，如 `where`: `"update_time >= '${bizdate}'"`

**Q: 同步速度慢怎么办？**
A: 调整 `speed.mb` 参数，或使用并发配置 `concurrent`

**Q: 目标表已存在数据会怎样？**
A: 取决于 `writeMode`：`append` 追加，`overwrite` 覆盖，`nonconflict` 冲突报错

## 相关教程

- [运行节点](../manage-instance/run-node.md) - 手动运行同步任务
- [查询实例状态](../../03-api/instance/GetInstance.md) - 查看任务运行结果
