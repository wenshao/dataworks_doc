# 通过 OpenAPI 创建、修改、删除离线同步任务

## 概述

本指南介绍如何使用 DataWorks OpenAPI 管理离线同步任务，包括创建、修改、删除和运行同步任务。

## 前提条件

1. 已开通 DataWorks 服务
2. 已创建数据源
3. 已创建 AccessKey ID 和 AccessKey Secret
4. 账号具有 `AliyunDataWorksFullAccess` 权限

## 1. 创建离线同步任务

使用 `CreateFile` API 创建离线同步节点。

```python
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models
import json

def create_sync_task(client, workspace_id, task_name, source_config, target_config):
    """创建离线同步任务"""
    # 构建同步任务配置
    sync_config = {
        "type": "mysql",
        "source": source_config,
        "target": target_config
    }

    request = dataworks_models.CreateFileRequest(
        project_identifier=workspace_id,
        file_name=task_name,
        file_type='27',  # 27 表示离线同步节点
        content=json.dumps(sync_config)
    )
    response = client.create_file(request)
    return response.body.data

# 示例：创建从 MySQL 到 MaxCompute 的同步任务
source_config = {
    "datasource": "mysql_source",
    "table": "source_table",
    "column": [
        {"name": "id", "type": "bigint"},
        {"name": "name", "type": "string"},
        {"name": "create_time", "type": "datetime"}
    ]
}

target_config = {
    "datasource": "odps_target",
    "table": "target_table",
    "partition": "pt=${bizdate}"
}

task_info = create_sync_task(
    client=client,
    workspace_id='your_workspace_id',
    task_name='mysql_to_odps_sync',
    source_config=source_config,
    target_config=target_config
)
print(f"同步任务创建成功，任务ID: {task_info.file_id}")
```

## 2. 配置同步任务调度

使用 `UpdateFile` API 配置任务的调度属性。

```python
def configure_schedule(client, workspace_id, file_id, cron_expression):
    """配置任务调度"""
    schedule_config = {
        "cronExpression": cron_expression,
        "autoRerunTimes": 3,
        "autoRerunInterval": 120,
        "dependentType": "USER_DEFINE"
    }

    request = dataworks_models.UpdateFileRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        schedule_configuration=json.dumps(schedule_config)
    )
    response = client.update_file(request)
    return response.body.data

# 示例：配置每天凌晨 2 点执行
configure_schedule(
    client=client,
    workspace_id='your_workspace_id',
    file_id='file_id',
    cron_expression='00 02 * * * ? *'
)
print("调度配置成功")
```

## 3. 提交同步任务

使用 `DeployFile` API 提交任务到生产环境。

```python
def submit_task(client, workspace_id, file_id):
    """提交任务"""
    request = dataworks_models.DeployFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.deploy_file(request)
    return response.body.data

# 示例：提交任务
submit_task(client, 'your_workspace_id', 'file_id')
print("任务提交成功")
```

## 4. 运行同步任务

使用 `RunNode` API 手动运行同步任务。

```python
def run_sync_task(client, workspace_id, file_id, bizdate):
    """运行同步任务"""
    request = dataworks_models.RunNodeRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        biz_date=bizdate  # 业务日期，格式：YYYYMMDD
    )
    response = client.run_node(request)
    return response.body.data

# 示例：运行同步任务
run_info = run_sync_task(
    client=client,
    workspace_id='your_workspace_id',
    file_id='file_id',
    bizdate='20250101'
)
print(f"任务运行实例ID: {run_info.instance_id}")
```

## 5. 查询同步任务状态

使用 `GetInstance` API 查询任务运行状态。

```python
def get_task_status(client, workspace_id, instance_id):
    """查询任务状态"""
    request = dataworks_models.GetInstanceRequest(
        project_identifier=workspace_id,
        instance_id=instance_id
    )
    response = client.get_instance(request)
    return response.body.data

# 示例：查询任务状态
instance_info = get_task_status(client, 'your_workspace_id', 'instance_id')
print(f"任务状态: {instance_info.status}")  # RUNNING, SUCCESS, FAILED
print(f"开始时间: {instance_info.start_time}")
print(f"结束时间: {instance_info.end_time}")
```

## 6. 修改同步任务

使用 `UpdateFile` API 修改同步任务配置。

```python
def update_sync_task(client, workspace_id, file_id, new_source_config=None, new_target_config=None):
    """修改同步任务"""
    update_config = {}

    if new_source_config:
        update_config['source'] = new_source_config
    if new_target_config:
        update_config['target'] = new_target_config

    request = dataworks_models.UpdateFileRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        content=json.dumps(update_config)
    )
    response = client.update_file(request)
    return response.body.data

# 示例：修改目标表
new_target_config = {
    "datasource": "odps_target",
    "table": "new_target_table",
    "partition": "pt=${bizdate}"
}

update_sync_task(
    client=client,
    workspace_id='your_workspace_id',
    file_id='file_id',
    new_target_config=new_target_config
)
print("同步任务修改成功")
```

## 7. 删除同步任务

使用 `DeleteFile` API 删除同步任务。

```python
def delete_sync_task(client, workspace_id, file_id):
    """删除同步任务"""
    request = dataworks_models.DeleteFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.delete_file(request)
    return response.body.data

# 示例：删除同步任务
delete_sync_task(client, 'your_workspace_id', 'file_id')
print("同步任务删除成功")
```

## 完整示例：同步任务管理类

```python
from typing import Dict, Optional
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models
import json

class SyncTaskManager:
    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def create_task(self, workspace_id: str, task_name: str,
                    source_ds: str, source_table: str,
                    target_ds: str, target_table: str):
        """创建离线同步任务"""
        config = {
            "type": "mysql",
            "source": {
                "datasource": source_ds,
                "table": source_table
            },
            "target": {
                "datasource": target_ds,
                "table": target_table,
                "partition": "pt=${bizdate}"
            }
        }

        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=task_name,
            file_type='27',
            content=json.dumps(config)
        )
        response = self.client.create_file(request)
        return response.body.data

    def update_task(self, workspace_id: str, file_id: str,
                    source_ds: Optional[str] = None,
                    source_table: Optional[str] = None,
                    target_ds: Optional[str] = None,
                    target_table: Optional[str] = None):
        """更新同步任务"""
        # 先获取当前配置
        get_request = dataworks_models.GetFileRequest(
            project_identifier=workspace_id,
            file_id=file_id
        )
        current = self.client.get_file(get_request).body.data
        config = json.loads(current.content)

        # 更新配置
        if source_ds:
            config['source']['datasource'] = source_ds
        if source_table:
            config['source']['table'] = source_table
        if target_ds:
            config['target']['datasource'] = target_ds
        if target_table:
            config['target']['table'] = target_table

        request = dataworks_models.UpdateFileRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            content=json.dumps(config)
        )
        response = self.client.update_file(request)
        return response.body.data

    def delete_task(self, workspace_id: str, file_id: str):
        """删除同步任务"""
        request = dataworks_models.DeleteFileRequest(
            project_identifier=workspace_id,
            file_id=file_id
        )
        response = self.client.delete_file(request)
        return response.body.data

    def run_task(self, workspace_id: str, file_id: str, bizdate: str):
        """运行同步任务"""
        request = dataworks_models.RunNodeRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data
```

## 常见同步场景配置

### MySQL 到 MaxCompute

```python
mysql_to_odps = {
    "type": "mysql",
    "source": {
        "datasource": "mysql_source",
        "table": "user_table",
        "where": "create_time >= '${bizdate}'"
    },
    "target": {
        "datasource": "odps_target",
        "table": "ods_user",
        "partition": "pt=${bizdate}",
        "writeMode": "overwrite"
    }
}
```

### Oracle 到 MaxCompute

```python
oracle_to_odps = {
    "type": "oracle",
    "source": {
        "datasource": "oracle_source",
        "table": "ORDER_TABLE",
        "where": "ORDER_DATE >= TO_DATE('${bizdate}', 'YYYYMMDD')"
    },
    "target": {
        "datasource": "odps_target",
        "table": "ods_order",
        "partition": "pt=${bizdate}",
        "writeMode": "append"
    }
}
```

## 注意事项

1. **数据源连接**：确保数据源连接配置正确
2. **表结构匹配**：源表和目标表的字段类型需要匹配
3. **分区配置**：目标表分区字段配置正确
4. **调度时间**：避免多个大任务同时运行
5. **错误处理**：做好异常捕获和日志记录

## 参考链接

- [API 目录](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [文件管理 OpenAPI 实践](file-management.md)
- [数据开发 OpenAPI 实践](data-development.md)
