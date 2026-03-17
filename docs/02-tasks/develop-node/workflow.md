---
title: 创建工作流
id: task-create-workflow
category: 节点开发
difficulty: 初级
time: 10分钟
prerequisites:
  - 已有工作空间
apis:
  - CreateWorkflow
  - UpdateNode
related:
  - 创建 SQL 节点
  - 配置节点依赖
tags: [workflow, node, development]
---

# 创建工作流

本教程演示如何创建工作流并组织多个节点。

## 什么是工作流

工作流（Workflow）是将多个相关节点组织在一起的方式，可以：

- 管理节点间的依赖关系
- 整体发布和版本管理
- 可视化展示数据流向

## 步骤一：创建工作流

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def create_workflow(workspace_id, name, description=''):
    """创建工作流"""
    request = dataworks_models.CreateWorkflowRequest(
        project_identifier=workspace_id,
        workflow_name=name,
        description=description
    )
    response = client.create_workflow(request)
    return response.body.data

# 使用
workflow = create_workflow(
    workspace_id='your_workspace_id',
    name='daily_data_pipeline',
    description='每日数据处理流程'
)

workflow_id = workflow.workflow_id
print(f"工作流创建成功，ID: {workflow_id}")
```

## 步骤二：向工作流添加节点

```python
def add_sql_node_to_workflow(workspace_id, workflow_id, name, sql):
    """创建 SQL 节点并添加到工作流"""
    request = dataworks_models.CreateFileRequest(
        project_identifier=workspace_id,
        file_name=name,
        file_type='10',  # ODPS SQL
        workflow_id=workflow_id,  # 指定工作流
        code=sql
    )
    response = client.create_file(request)
    return response.body.data.file_id

# 创建多个节点
extract_id = add_sql_node_to_workflow(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='extract_data',
    sql='INSERT OVERWRITE TABLE tmp_extract SELECT * FROM source_table WHERE dt="${bizdate}"'
)

transform_id = add_sql_node_to_workflow(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='transform_data',
    sql='INSERT OVERWRITE TABLE tmp_transform SELECT * FROM tmp_extract'
)

load_id = add_sql_node_to_workflow(
    workspace_id='your_workspace_id',
    workflow_id=workflow_id,
    name='load_data',
    sql='INSERT OVERWRITE TABLE target_table SELECT * FROM tmp_transform'
)
```

## 步骤三：配置节点依赖

```python
def set_dependency(workspace_id, child_node_id, parent_node_ids):
    """设置节点依赖关系"""
    # 注意：需要使用 UpdateNode 而不是 UpdateFile
    # 先获取 node_id（通常与 file_id 相同）
    request = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=child_node_id,
        parent_node_ids=parent_node_ids if isinstance(parent_node_ids, list) else [parent_node_ids]
    )
    client.update_node(request)

# 配置依赖：extract → transform → load
set_dependency('your_workspace_id', transform_id, extract_id)
set_dependency('your_workspace_id', load_id, transform_id)
```

## 工作流结构示例

```
daily_data_pipeline
├── extract_data      (起始节点，无上游依赖)
│   └── ↓
├── transform_data    (依赖 extract_data)
│   └── ↓
└── load_data         (依赖 transform_data)
```

## 完整工具类

```python
from typing import List, Optional
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class WorkflowManager:
    """工作流管理工具"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)
        self.nodes = {}  # name -> file_id

    def create(self, workspace_id: str, name: str, description: str = '') -> str:
        """创建工作流"""
        request = dataworks_models.CreateWorkflowRequest(
            project_identifier=workspace_id,
            workflow_name=name,
            description=description
        )
        response = self.client.create_workflow(request)
        return response.body.data.workflow_id

    def add_sql_node(self, workspace_id: str, workflow_id: str,
                     name: str, sql: str) -> str:
        """添加 SQL 节点到工作流"""
        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=name,
            file_type='10',
            workflow_id=workflow_id,
            code=sql
        )
        response = self.client.create_file(request)
        file_id = response.body.data.file_id
        self.nodes[name] = file_id
        return file_id

    def add_dependency(self, workspace_id: str, child: str, parent: str):
        """添加节点依赖"""
        request = dataworks_models.UpdateNodeRequest(
            project_identifier=workspace_id,
            node_id=self.nodes[child],
            parent_node_ids=[self.nodes[parent]]
        )
        self.client.update_node(request)

    def add_chain(self, workspace_id: str, node_names: List[str]):
        """将节点按顺序串联（第一个依赖第二个，第二个依赖第三个...）"""
        for i in range(len(node_names) - 1):
            self.add_dependency(workspace_id, node_names[i + 1], node_names[i])

    def schedule(self, workspace_id: str, node_name: str, cron: str):
        """配置节点调度"""
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
        """提交工作流中的所有节点"""
        for file_id in self.nodes.values():
            request = dataworks_models.DeployFileRequest(
                project_identifier=workspace_id,
                file_id=file_id
            )
            self.client.deploy_file(request)

# 使用示例
manager = WorkflowManager(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 创建工作流
workflow_id = manager.create('your_workspace_id', 'my_etl_flow', '我的ETL流程')

# 添加节点
manager.add_sql_node('your_workspace_id', workflow_id, 'step1', 'SELECT 1')
manager.add_sql_node('your_workspace_id', workflow_id, 'step2', 'SELECT 2')
manager.add_sql_node('your_workspace_id', workflow_id, 'step3', 'SELECT 3')

# 配置依赖链：step1 → step2 → step3
manager.add_chain('your_workspace_id', ['step1', 'step2', 'step3'])

# 配置调度（起始节点）
manager.schedule('your_workspace_id', 'step1', '00 02 * * * ? *')

# 提交
manager.deploy('your_workspace_id')
```

## 依赖关系最佳实践

1. **避免循环依赖**：会导致无法运行
2. **合理分层**：ODS → DWD → DWS → ADS
3. **使用虚拟节点**：组织复杂依赖关系

```python
# 创建虚拟节点
request = dataworks_models.CreateFileRequest(
    project_identifier=workspace_id,
    file_name='start_virtual',
    file_type='35',  # 虚拟节点
    workflow_id=workflow_id,
    code=''  # 虚拟节点不需要代码
)
```

## 相关教程

- [创建 SQL 节点](sql-node.md)
- [创建同步节点](../sync-data/mysql-to-odps.md)
- [运行节点](../manage-instance/run-node.md)
