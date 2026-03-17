# 数据 ETL 操作全流程实践

## 概述

本指南介绍如何使用 DataWorks OpenAPI 完成端到端的数据 ETL（抽取、转换、加载）操作，涵盖从数据抽取、数据转换到数据加载的完整流程。

## 场景描述

假设我们有以下 ETL 需求：
1. **抽取（Extract）**：从 MySQL 数据源抽取用户行为数据
2. **转换（Transform）**：对数据进行清洗和转换
3. **加载（Load）**：将处理后的数据加载到 MaxCompute 分区表

## 架构设计

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   抽取节点   │────▶│   转换节点   │────▶│   加载节点   │
│  (MySQL)    │     │  (ODPS SQL) │     │ (ODPS SQL)  │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       ▼                   ▼                   ▼
  ods_user_log      dwd_user_fact      dws_user_daily
```

## 实现步骤

### 1. 准备工作

```python
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

# 初始化客户端
config = open_api_models.Config(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET',
    endpoint='dataworks.cn-hangzhou.aliyuncs.com'
)
client = Client(config)

# 配置参数
WORKSPACE_ID = 'your_workspace_id'
PROJECT_NAME = 'your_project'
```

### 2. 创建工作流

```python
def create_etl_workflow(client, workspace_id, name):
    """创建 ETL 工作流"""
    request = dataworks_models.CreateWorkflowRequest(
        project_identifier=workspace_id,
        workflow_name=name,
        description='用户数据 ETL 流程'
    )
    response = client.create_workflow(request)
    return response.body.data.workflow_id

# 创建工作流
workflow_id = create_etl_workflow(client, WORKSPACE_ID, 'user_data_etl')
print(f"工作流创建成功，ID: {workflow_id}")
```

### 3. 创建数据同步任务（抽取）

```python
def create_extract_node(client, workspace_id, workflow_id):
    """创建数据抽取节点"""
    sync_config = {
        "type": "mysql",
        "source": {
            "datasource": "mysql_user_db",
            "table": "user_behavior_log",
            "where": "create_time >= '${bizdate}'"
        },
        "target": {
            "datasource": "odps_project",
            "table": "ods_user_log",
            "partition": "dt=${bizdate}",
            "writeMode": "append"
        }
    }

    request = dataworks_models.CreateNodeRequest(
        project_identifier=workspace_id,
        node_name='extract_user_log',
        node_type='DI_OFFLINE',  # 离线同步节点
        workflow_id=workflow_id,
        content=json.dumps(sync_config)
    )
    response = client.create_node(request)
    return response.body.data.node_id

# 创建抽取节点
extract_node_id = create_extract_node(client, WORKSPACE_ID, workflow_id)
print(f"抽取节点创建成功，ID: {extract_node_id}")
```

### 4. 创建数据转换节点

```python
def create_transform_node(client, workspace_id, workflow_id):
    """创建数据转换节点"""
    transform_sql = """
-- 清洗和转换用户行为数据
INSERT OVERWRITE TABLE dwd_user_fact
PARTITION (dt='${bizdate}')
SELECT
    user_id,
    behavior_type,
    COUNT(*) AS behavior_count,
    SUM(CASE WHEN behavior_type = 'click' THEN 1 ELSE 0 END) AS click_count,
    SUM(CASE WHEN behavior_type = 'view' THEN 1 ELSE 0 END) AS view_count,
    SUM(CASE WHEN behavior_type = 'purchase' THEN 1 ELSE 0 END) AS purchase_count,
    '${bizdate}' AS data_date
FROM ods_user_log
WHERE dt = '${bizdate}'
  AND user_id IS NOT NULL
  AND behavior_type IN ('click', 'view', 'purchase')
GROUP BY user_id, behavior_type;
"""

    request = dataworks_models.CreateNodeRequest(
        project_identifier=workspace_id,
        node_name='transform_user_data',
        node_type='ODPS_SQL',
        workflow_id=workflow_id,
        code=transform_sql
    )
    response = client.create_node(request)
    return response.body.data.node_id

# 创建转换节点
transform_node_id = create_transform_node(client, WORKSPACE_ID, workflow_id)
print(f"转换节点创建成功，ID: {transform_node_id}")
```

### 5. 创建数据聚合节点

```python
def create_aggregate_node(client, workspace_id, workflow_id):
    """创建数据聚合节点"""
    aggregate_sql = """
-- 聚合用户日维度数据
INSERT OVERWRITE TABLE dws_user_daily
PARTITION (dt='${bizdate}')
SELECT
    user_id,
    SUM(behavior_count) AS total_behavior,
    SUM(click_count) AS total_click,
    SUM(view_count) AS total_view,
    SUM(purchase_count) AS total_purchase,
    COUNT(DISTINCT behavior_type) AS behavior_variety,
    '${bizdate}' AS data_date
FROM dwd_user_fact
WHERE dt = '${bizdate}'
GROUP BY user_id;
"""

    request = dataworks_models.CreateNodeRequest(
        project_identifier=workspace_id,
        node_name='aggregate_user_daily',
        node_type='ODPS_SQL',
        workflow_id=workflow_id,
        code=aggregate_sql
    )
    response = client.create_node(request)
    return response.body.data.node_id

# 创建聚合节点
aggregate_node_id = create_aggregate_node(client, WORKSPACE_ID, workflow_id)
print(f"聚合节点创建成功，ID: {aggregate_node_id}")
```

### 6. 配置节点依赖关系

```python
def set_dependencies(client, workspace_id, transform_id, extract_id, aggregate_id):
    """配置节点依赖"""
    # 转换节点依赖抽取节点
    request1 = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=transform_id,
        parent_node_ids=[extract_id]
    )
    client.update_node(request1)

    # 聚合节点依赖转换节点
    request2 = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=aggregate_id,
        parent_node_ids=[transform_id]
    )
    client.update_node(request2)

# 配置依赖关系
set_dependencies(client, WORKSPACE_ID, transform_node_id, extract_node_id, aggregate_node_id)
print("依赖关系配置成功")
```

### 7. 配置调度

```python
def configure_schedule(client, workspace_id, node_id, cron='00 01 * * * ? *'):
    """配置节点调度（每天凌晨1点执行）"""
    request = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=node_id,
        schedule_configuration={
            'cronExpression': cron,
            'autoRerunTimes': 3,
            'autoRerunInterval': 120,
            'dependentType': 'USER_DEFINE'
        }
    )
    client.update_node(request)

# 为起始节点配置调度
configure_schedule(client, WORKSPACE_ID, extract_node_id)
print("调度配置成功")
```

### 8. 提交工作流

```python
def submit_workflow_nodes(client, workspace_id, node_ids):
    """提交工作流中的所有节点"""
    for node_id in node_ids:
        # 获取节点的 file_id
        get_request = dataworks_models.GetNodeRequest(
            project_identifier=workspace_id,
            node_id=node_id
        )
        node_info = client.get_node(get_request).body.data

        # 提交节点
        deploy_request = dataworks_models.DeployFileRequest(
            project_identifier=workspace_id,
            file_id=node_info.file_id
        )
        client.deploy_file(deploy_request)
        print(f"节点 {node_info.node_name} 提交成功")

# 提交所有节点
submit_workflow_nodes(client, WORKSPACE_ID, [
    extract_node_id,
    transform_node_id,
    aggregate_node_id
])
```

## 完整实现类

```python
import json
from typing import List, Dict
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class ETLWorkflowBuilder:
    """ETL 工作流构建器"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)
        self.nodes: Dict[str, str] = {}  # 节点名称 -> 节点ID

    def create_workflow(self, workspace_id: str, name: str, description: str = '') -> str:
        """创建工作流"""
        request = dataworks_models.CreateWorkflowRequest(
            project_identifier=workspace_id,
            workflow_name=name,
            description=description
        )
        response = self.client.create_workflow(request)
        return response.body.data.workflow_id

    def add_sync_node(self, workspace_id: str, workflow_id: str, name: str,
                      source_ds: str, source_table: str,
                      target_ds: str, target_table: str) -> str:
        """添加数据同步节点"""
        config = {
            "type": "mysql",
            "source": {
                "datasource": source_ds,
                "table": source_table
            },
            "target": {
                "datasource": target_ds,
                "table": target_table,
                "partition": "dt=${bizdate}"
            }
        }
        request = dataworks_models.CreateNodeRequest(
            project_identifier=workspace_id,
            node_name=name,
            node_type='DI_OFFLINE',
            workflow_id=workflow_id,
            content=json.dumps(config)
        )
        response = self.client.create_node(request)
        node_id = response.body.data.node_id
        self.nodes[name] = node_id
        return node_id

    def add_sql_node(self, workspace_id: str, workflow_id: str, name: str, sql: str) -> str:
        """添加 SQL 节点"""
        request = dataworks_models.CreateNodeRequest(
            project_identifier=workspace_id,
            node_name=name,
            node_type='ODPS_SQL',
            workflow_id=workflow_id,
            code=sql
        )
        response = self.client.create_node(request)
        node_id = response.body.data.node_id
        self.nodes[name] = node_id
        return node_id

    def add_dependency(self, workspace_id: str, child_name: str, parent_name: str):
        """添加节点依赖"""
        request = dataworks_models.UpdateNodeRequest(
            project_identifier=workspace_id,
            node_id=self.nodes[child_name],
            parent_node_ids=[self.nodes[parent_name]]
        )
        self.client.update_node(request)

    def configure_schedule(self, workspace_id: str, node_name: str, cron: str):
        """配置调度"""
        request = dataworks_models.UpdateNodeRequest(
            project_identifier=workspace_id,
            node_id=self.nodes[node_name],
            schedule_configuration={
                'cronExpression': cron,
                'autoRerunTimes': 3
            }
        )
        self.client.update_node(request)

    def submit(self, workspace_id: str):
        """提交所有节点"""
        for node_id in self.nodes.values():
            get_request = dataworks_models.GetNodeRequest(
                project_identifier=workspace_id,
                node_id=node_id
            )
            node_info = self.client.get_node(get_request).body.data

            deploy_request = dataworks_models.DeployFileRequest(
                project_identifier=workspace_id,
                file_id=node_info.file_id
            )
            self.client.deploy_file(deploy_request)

    def run(self, workspace_id: str, node_name: str, bizdate: str):
        """运行节点"""
        get_request = dataworks_models.GetNodeRequest(
            project_identifier=workspace_id,
            node_id=self.nodes[node_name]
        )
        node_info = self.client.get_node(get_request).body.data

        request = dataworks_models.RunNodeRequest(
            project_identifier=workspace_id,
            file_id=node_info.file_id,
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data.instance_id
```

## 使用示例

```python
# 初始化构建器
builder = ETLWorkflowBuilder(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 创建工作流
workflow_id = builder.create_workflow(
    workspace_id='your_workspace_id',
    name='user_etl_workflow',
    description='用户数据ETL流程'
)

# 添加节点
extract_id = builder.add_sync_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='extract',
    source_ds='mysql_source',
    source_table='user_log',
    target_ds='odps_target',
    target_table='ods_user_log'
)

transform_id = builder.add_sql_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='transform',
    sql='INSERT OVERWRITE TABLE dwd_user_fact SELECT * FROM ods_user_log'
)

load_id = builder.add_sql_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='load',
    sql='INSERT OVERWRITE TABLE dws_user_daily SELECT * FROM dwd_user_fact'
)

# 配置依赖
builder.add_dependency('your_workspace_id', 'transform', 'extract')
builder.add_dependency('your_workspace_id', 'load', 'transform')

# 配置调度
builder.configure_schedule('your_workspace_id', 'extract', '00 01 * * * ? *')

# 提交
builder.submit('your_workspace_id')

# 运行
instance_id = builder.run('your_workspace_id', 'extract', '20250101')
print(f"任务运行实例ID: {instance_id}")
```

## 最佳实践

1. **节点命名规范**：使用有意义的命名，如 `extract_source_table`、`transform_clean_data`
2. **分阶段提交**：先测试单个节点，再组合成完整流程
3. **错误处理**：配置自动重试次数和间隔
4. **监控告警**：结合 OpenEvent 实现任务状态监控
5. **参数化设计**：使用 `${bizdate}` 等调度参数

## 参考链接

- [API 目录](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [文件管理 OpenAPI 实践](file-management.md)
- [数据开发 OpenAPI 实践](data-development.md)
