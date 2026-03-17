---
title: 10 个常见任务速查
id: common-tasks
category: 快速入门
difficulty: 初级
time: 5分钟
tags: [入门, 速查]
---

# 10 个常见任务速查

## 快速索引

| # | 任务 | 说明 | 难度 |
|---|------|------|------|
| 1 | [查询工作空间](#1-查询工作空间) | 获取我有权限的工作空间列表 | ⭐ |
| 2 | [查询数据表](#2-查询数据表) | 获取表结构和元数据 | ⭐ |
| 3 | [创建同步节点](#3-创建同步节点) | 将数据从源同步到目标 | ⭐⭐ |
| 4 | [运行节点](#4-运行节点) | 手动触发节点执行 | ⭐⭐ |
| 5 | [查询实例状态](#5-查询实例状态) | 查看任务运行结果 | ⭐⭐ |
| 6 | [创建 SQL 节点](#6-创建-sql-节点) | 开发数据处理任务 | ⭐⭐ |
| 7 | [创建工作流](#7-创建工作流) | 组织多个节点 | ⭐⭐ |
| 8 | [提交节点](#8-提交节点) | 将节点发布到生产 | ⭐⭐ |
| 9 | [补数据](#9-补数据) | 重新计算历史数据 | ⭐⭐⭐ |
| 10 | [查询质量规则](#10-查询质量规则) | 查看数据质量配置 | ⭐⭐ |

---

## 1. 查询工作空间

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

request = dataworks_models.ListWorkspacesRequest(
    page_number=1,
    page_size=20
)
response = client.list_workspaces(request)
```

[→ 详细教程](../02-tasks/manage-instance/query-workspace.md)

---

## 2. 查询数据表

```python
request = dataworks_models.GetTableRequest(
    project_identifier='workspace_id',
    table_name='table_name'
)
response = client.get_table(request)
```

[→ 详细教程](../02-tasks/manage-table/query-table.md)

---

## 3. 创建同步节点

```python
request = dataworks_models.CreateFileRequest(
    project_identifier='workspace_id',
    file_name='mysql_to_odps',
    file_type='27',  # 离线同步
    content=json.dumps({
        "source": {"datasource": "mysql_src", "table": "t1"},
        "target": {"datasource": "odps_dst", "table": "t1"}
    })
)
response = client.create_file(request)
```

[→ 详细教程](../02-tasks/sync-data/mysql-to-odps.md)

---

## 4. 运行节点

```python
request = dataworks_models.RunNodeRequest(
    project_identifier='workspace_id',
    file_id='file_id',
    biz_date='20250101'
)
response = client.run_node(request)
```

[→ 详细教程](../02-tasks/manage-instance/run-node.md)

---

## 5. 查询实例状态

```python
request = dataworks_models.GetInstanceRequest(
    project_identifier='workspace_id',
    instance_id='instance_id'
)
response = client.get_instance(request)
status = response.body.data.status  # SUCCESS/FAILED/RUNNING
```

[→ 详细教程](../02-tasks/manage-instance/check-status.md)

---

## 6. 创建 SQL 节点

```python
request = dataworks_models.CreateFileRequest(
    project_identifier='workspace_id',
    file_name='my_sql_task',
    file_type='10',  # ODPS SQL
    content='SELECT * FROM my_table WHERE dt="${bizdate}"'
)
response = client.create_file(request)
```

[→ 详细教程](../02-tasks/develop-node/sql-node.md)

---

## 7. 创建工作流

```python
request = dataworks_models.CreateWorkflowRequest(
    project_identifier='workspace_id',
    workflow_name='my_workflow',
    description='我的数据处理流程'
)
response = client.create_workflow(request)
```

[→ 详细教程](../02-tasks/develop-node/workflow.md)

---

## 8. 提交节点

```python
request = dataworks_models.DeployFileRequest(
    project_identifier='workspace_id',
    file_id='file_id'
)
response = client.deploy_file(request)
```

---

## 9. 补数据

```python
request = dataworks_models.CreateManualDagRequest(
    project_identifier='workspace_id',
    node_id='node_id',
    biz_date='20250101',
    end_biz_date='20250131'
)
response = client.create_manual_dag(request)
```

---

## 10. 查询质量规则

```python
request = dataworks_models.ListQualityRulesRequest(
    project_identifier='workspace_id',
    table_name='table_name'
)
response = client.list_quality_rules(request)
```

---

## 需要更多帮助？

- [完整 API 参考](../03-api/index.md)
- [错误码速查](../05-reference/error-codes.md)
- [官方文档](https://help.aliyun.com/zh/dataworks)
