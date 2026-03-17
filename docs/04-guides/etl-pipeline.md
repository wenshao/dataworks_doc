---
title: ETL 数据处理全流程
id: guide-etl-pipeline
category: 实战指南
difficulty: 中级
time: 30分钟
prerequisites:
  - 完成 [快速入门](../00-quick-start/)
  - 了解 [核心概念](../01-concepts/)
tags: [etl, pipeline, tutorial]
related:
  - 搭建数据仓库
  - 数据质量保障
---

# ETL 数据处理全流程

本教程演示如何使用 DataWorks OpenAPI 构建一个完整的 ETL 数据处理流程。

## 业务场景

构建一个用户行为数据处理流程：

```
MySQL 原始日志 → ODS 原始层 → DWD 明细层 → DWS 汇总层
```

## 架构设计

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   抽取节点   │────▶│   转换节点   │────▶│   加载节点   │
│  (MySQL)    │     │  (ODPS SQL) │     │  (ODPS SQL) │
└─────────────┘     └─────────────┘     └─────────────┘
       │                   │                   │
       ▼                   ▼                   ▼
   ods_user_log      dwd_user_fact      dws_user_daily
```

## 前置条件

- [ ] 已创建工作空间
- [ ] 已配置 MySQL 数据源
- [ ] 已创建目标表（ods_user_log, dwd_user_fact, dws_user_daily）

## 完整实现

```python
"""
用户行为 ETL 流程
"""
import json
import time
from typing import List, Dict
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class ETLFlowBuilder:
    """ETL 流程构建器"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)
        self.nodes: Dict[str, str] = {}  # 节点名 -> 文件ID

    def create_workflow(self, workspace_id: str, name: str) -> str:
        """创建工作流"""
        request = dataworks_models.CreateWorkflowRequest(
            project_identifier=workspace_id,
            workflow_name=name,
            description='用户行为 ETL 流程'
        )
        response = self.client.create_workflow(request)
        return response.body.data.workflow_id

    def add_extract_node(self, workspace_id: str, workflow_id: str,
                         name: str, source_ds: str, source_table: str,
                         target_ds: str, target_table: str) -> str:
        """添加抽取节点（数据同步）"""
        config = {
            "type": "mysql",
            "source": {
                "datasource": source_ds,
                "table": source_table,
                "where": "create_time >= '${bizdate}'"
            },
            "target": {
                "datasource": target_ds,
                "table": target_table,
                "partition": "dt='${bizdate}'"
            }
        }

        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=name,
            file_type='27',  # 离线同步
            workflow_id=workflow_id,
            content=json.dumps(config, ensure_ascii=False)
        )
        response = self.client.create_file(request)
        file_id = response.body.data.file_id
        self.nodes[name] = file_id
        return file_id

    def add_transform_node(self, workspace_id: str, workflow_id: str,
                          name: str, sql: str) -> str:
        """添加转换节点（SQL）"""
        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=name,
            file_type='10',  # ODPS SQL
            workflow_id=workflow_id,
            code=sql
        )
        response = self.client.create_file(request)
        file_id = response.body.data.file_id
        self.nodes[name] = file_id
        return file_id

    def add_load_node(self, workspace_id: str, workflow_id: str,
                      name: str, sql: str) -> str:
        """添加加载节点（SQL）"""
        return self.add_transform_node(workspace_id, workflow_id, name, sql)

    def set_dependency(self, workspace_id: str, child_name: str, parent_name: str):
        """设置节点依赖"""
        request = dataworks_models.UpdateNodeRequest(
            project_identifier=workspace_id,
            node_id=self.nodes[child_name],
            parent_node_ids=[self.nodes[parent_name]]
        )
        self.client.update_node(request)

    def schedule(self, workspace_id: str, node_name: str, cron: str):
        """配置调度"""
        request = dataworks_models.UpdateFileRequest(
            project_identifier=workspace_id,
            file_id=self.nodes[node_name],
            schedule_configuration={
                "cronExpression": cron,
                "autoRerunTimes": 3
            }
        )
        self.client.update_file(request)

    def deploy(self, workspace_id: str):
        """提交所有节点"""
        for node_id in self.nodes.values():
            request = dataworks_models.DeployFileRequest(
                project_identifier=workspace_id,
                file_id=node_id
            )
            self.client.deploy_file(request)

    def run(self, workspace_id: str, node_name: str, bizdate: str) -> str:
        """运行节点"""
        request = dataworks_models.RunNodeRequest(
            project_identifier=workspace_id,
            file_id=self.nodes[node_name],
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data.instance_id


# ===== 使用示例 =====

# 1. 初始化
builder = ETLFlowBuilder(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 2. 创建工作流
workflow_id = builder.create_workflow(
    workspace_id='your_workspace_id',
    name='user_behavior_etl'
)

# 3. 添加抽取节点
extract_id = builder.add_extract_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='extract_user_log',
    source_ds='mysql_user_db',
    source_table='user_behavior_log',
    target_ds='odps_project',
    target_table='ods_user_log'
)

# 4. 添加转换节点
transform_sql = """
INSERT OVERWRITE TABLE dwd_user_fact
PARTITION (dt='${bizdate}')
SELECT
    user_id,
    behavior_type,
    device_type,
    COUNT(*) AS behavior_count,
    SUM(CASE WHEN behavior_type = 'click' THEN 1 ELSE 0 END) AS click_count,
    '${bizdate}' AS data_date
FROM ods_user_log
WHERE dt = '${bizdate}'
  AND user_id IS NOT NULL
GROUP BY user_id, behavior_type, device_type;
"""

transform_id = builder.add_transform_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='transform_user_data',
    sql=transform_sql
)

# 5. 添加加载节点
load_sql = """
INSERT OVERWRITE TABLE dws_user_daily
PARTITION (dt='${bizdate}')
SELECT
    user_id,
    SUM(behavior_count) AS total_behavior,
    AVG(click_count) AS avg_click,
    COUNT(DISTINCT behavior_type) AS behavior_variety,
    '${bizdate}' AS data_date
FROM dwd_user_fact
WHERE dt = '${bizdate}'
GROUP BY user_id;
"""

load_id = builder.add_load_node(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='load_user_daily',
    sql=load_sql
)

# 6. 配置依赖关系
builder.set_dependency('your_workspace_id', 'transform_user_data', 'extract_user_log')
builder.set_dependency('your_workspace_id', 'load_user_daily', 'transform_user_data')

# 7. 配置调度（每天凌晨1点开始）
builder.schedule('your_workspace_id', 'extract_user_log', '00 01 * * * ? *')

# 8. 提交发布
builder.deploy('your_workspace_id')

# 9. 运行测试
instance_id = builder.run('your_workspace_id', 'extract_user_log', '20250101')
print(f"流程已启动，实例ID: {instance_id}")
```

## 监控运行状态

```python
class ETLFlowMonitor:
    """ETL 流程监控"""

    def __init__(self, client):
        self.client = client

    def wait_for_flow(self, workspace_id: str, start_instance_id: str,
                      timeout: int = 7200) -> bool:
        """等待整个流程完成"""
        start_time = time.time()
        completed_nodes = set()

        while time.time() - start_time < timeout:
            # 查询下游实例
            instances = self._get_downstream_instances(workspace_id, start_instance_id)

            all_done = True
            for inst in instances:
                if inst.status not in ['SUCCESS', 'FAILED']:
                    all_done = False
                    if inst.node_name not in completed_nodes:
                        print(f"  {inst.node_name}: {inst.status}")

                if inst.status == 'SUCCESS' and inst.node_name not in completed_nodes:
                    completed_nodes.add(inst.node_name)
                    print(f"✓ {inst.node_name} 完成")

                elif inst.status == 'FAILED':
                    print(f"✗ {inst.node_name} 失败")
                    return False

            if all_done:
                print("\n✓ 整个流程执行完成！")
                return True

            time.sleep(30)

        print("\n✗ 流程执行超时")
        return False
```

## 最佳实践

1. **命名规范**
   - 节点名：`{层}_{操作}_{表名}`，如 `ods_extract_user`
   - 工作流名：`{业务}_etl`

2. **分区设计**
   - 统一使用 `dt='${bizdate}'` 格式
   - 分区字段类型统一为 string

3. **调度配置**
   - 抽取节点：凌晨 1 点
   - 转换节点：依赖抽取节点完成
   - 加载节点：依赖转换节点完成

4. **错误处理**
   - 配置自动重试 3 次
   - 设置告警通知

## 相关教程

- [创建工作流](../02-tasks/develop-node/workflow.md)
- [MySQL 同步到 MaxCompute](../02-tasks/sync-data/mysql-to-odps.md)
