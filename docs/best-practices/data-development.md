# 数据开发、提交与运行 OpenAPI 基础实践

## 概述

本指南介绍如何使用 DataWorks OpenAPI 进行数据开发操作，包括节点创建、代码提交、调度配置和运行。

## 前提条件

1. 已开通 DataWorks 服务
2. 已创建工作空间
3. 已创建 AccessKey ID 和 AccessKey Secret
4. 账号具有 `AliyunDataWorksFullAccess` 权限

## 1. 创建节点

使用 `CreateNode` API 创建数据开发节点。

```python
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def create_node(client, workspace_id, node_name, node_type, code):
    """创建节点"""
    request = dataworks_models.CreateNodeRequest(
        project_identifier=workspace_id,
        node_name=node_name,
        node_type=node_type,  # 节点类型，如 SQL、Shell 等
        code=code
    )
    response = client.create_node(request)
    return response.body.data

# 示例：创建 ODPS SQL 节点
node_info = create_node(
    client=client,
    workspace_id='your_workspace_id',
    node_name='my_sql_node',
    node_type='ODPS_SQL',
    code='SELECT * FROM my_table WHERE dt = "${bizdate}";'
)
print(f"节点创建成功，节点ID: {node_info.node_id}")
```

## 2. 创建工作流

使用 `CreateWorkflow` API 创建工作流。

```python
def create_workflow(client, workspace_id, workflow_name, description=''):
    """创建工作流"""
    request = dataworks_models.CreateWorkflowRequest(
        project_identifier=workspace_id,
        workflow_name=workflow_name,
        description=description
    )
    response = client.create_workflow(request)
    return response.body.data

# 示例：创建工作流
workflow_info = create_workflow(
    client=client,
    workspace_id='your_workspace_id',
    workflow_name='my_workflow',
    description='我的数据处理工作流'
)
print(f"工作流创建成功，工作流ID: {workflow_info.workflow_id}")
```

## 3. 向工作流添加节点

```python
def add_node_to_workflow(client, workspace_id, workflow_id, node_id):
    """将节点添加到工作流"""
    request = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=node_id,
        workflow_id=workflow_id
    )
    response = client.update_node(request)
    return response.body.data

# 示例：添加节点到工作流
add_node_to_workflow(
    client=client,
    workspace_id='your_workspace_id',
    workflow_id='workflow_id',
    node_id='node_id'
)
print("节点已添加到工作流")
```

## 4. 配置节点调度

```python
def configure_node_schedule(client, workspace_id, node_id, cron_expression):
    """配置节点调度"""
    request = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=node_id,
        schedule_configuration={
            'cronExpression': cron_expression,
            'autoRerunTimes': 3,
            'autoRerunInterval': 120
        }
    )
    response = client.update_node(request)
    return response.body.data

# 示例：配置每天凌晨 2 点执行
configure_node_schedule(
    client=client,
    workspace_id='your_workspace_id',
    node_id='node_id',
    cron_expression='00 02 * * * ? *'
)
print("调度配置成功")
```

## 5. 配置节点依赖关系

```python
def set_node_dependency(client, workspace_id, node_id, parent_node_ids):
    """设置节点依赖"""
    request = dataworks_models.UpdateNodeRequest(
        project_identifier=workspace_id,
        node_id=node_id,
        parent_node_ids=parent_node_ids
    )
    response = client.update_node(request)
    return response.body.data

# 示例：设置依赖关系
set_node_dependency(
    client=client,
    workspace_id='your_workspace_id',
    node_id='child_node_id',
    parent_node_ids=['parent_node_id_1', 'parent_node_id_2']
)
print("依赖关系设置成功")
```

## 6. 提交节点

使用 `DeployFile` API 提交节点到调度系统。

```python
def submit_node(client, workspace_id, file_id):
    """提交节点"""
    request = dataworks_models.DeployFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.deploy_file(request)
    return response.body.data

# 示例：提交节点
submit_node(client, 'your_workspace_id', 'file_id')
print("节点提交成功")
```

## 7. 运行节点

使用 `RunNode` API 手动运行节点。

```python
def run_node(client, workspace_id, file_id, bizdate):
    """运行节点"""
    request = dataworks_models.RunNodeRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        biz_date=bizdate
    )
    response = client.run_node(request)
    return response.body.data

# 示例：运行节点
run_info = run_node(
    client=client,
    workspace_id='your_workspace_id',
    file_id='file_id',
    bizdate='20250101'
)
print(f"节点运行实例ID: {run_info.instance_id}")
```

## 8. 查询节点列表

```python
def list_nodes(client, workspace_id, workflow_id=None, page_number=1, page_size=20):
    """查询节点列表"""
    request = dataworks_models.ListNodesRequest(
        project_identifier=workspace_id,
        page_number=page_number,
        page_size=page_size
    )
    if workflow_id:
        request.workflow_id = workflow_id

    response = client.list_nodes(request)
    return response.body.data

# 示例：查询节点
nodes = list_nodes(client, 'your_workspace_id')
for node in nodes.nodes:
    print(f"节点名称: {node.node_name}, 节点ID: {node.node_id}, 类型: {node.node_type}")
```

## 9. 查询节点详情

```python
def get_node(client, workspace_id, node_id):
    """获取节点详情"""
    request = dataworks_models.GetNodeRequest(
        project_identifier=workspace_id,
        node_id=node_id
    )
    response = client.get_node(request)
    return response.body.data

# 示例：获取节点详情
node_detail = get_node(client, 'your_workspace_id', 'node_id')
print(f"节点名称: {node_detail.node_name}")
print(f"节点代码: {node_detail.code}")
print(f"调度配置: {node_detail.schedule_configuration}")
```

## 10. 创建资源文件

```python
def create_resource(client, workspace_id, resource_name, resource_type, content):
    """创建资源文件"""
    request = dataworks_models.CreateResourceRequest(
        project_identifier=workspace_id,
        resource_name=resource_name,
        resource_type=resource_type,  # 如 'file', 'jar', 'py' 等
        content=content
    )
    response = client.create_resource(request)
    return response.body.data

# 示例：创建 Python 资源
with open('my_script.py', 'r') as f:
    python_code = f.read()

resource_info = create_resource(
    client=client,
    workspace_id='your_workspace_id',
    resource_name='my_script.py',
    resource_type='py',
    content=python_code
)
print(f"资源创建成功，资源ID: {resource_info.resource_id}")
```

## 11. 创建函数

```python
def create_function(client, workspace_id, function_name, resource_id, class_name):
    """创建函数"""
    request = dataworks_models.CreateFunctionRequest(
        project_identifier=workspace_id,
        function_name=function_name,
        resource_id=resource_id,
        class_name=class_name
    )
    response = client.create_function(request)
    return response.body.data

# 示例：创建函数
function_info = create_function(
    client=client,
    workspace_id='your_workspace_id',
    function_name='my_udf',
    resource_id='resource_id',
    class_name='com.example.MyUDF'
)
print(f"函数创建成功，函数ID: {function_info.function_id}")
```

## 完整示例：数据处理流程

```python
class DataDevelopmentFlow:
    def __init__(self, client, workspace_id):
        self.client = client
        self.workspace_id = workspace_id
        self.nodes = []

    def create_workflow(self, name, description=''):
        """创建工作流"""
        workflow_info = create_workflow(self.client, self.workspace_id, name, description)
        self.workflow_id = workflow_info.workflow_id
        return self.workflow_id

    def add_sql_node(self, name, sql_code):
        """添加 SQL 节点"""
        node_info = create_node(self.client, self.workspace_id, name, 'ODPS_SQL', sql_code)
        self.nodes.append(node_info.node_id)
        return node_info.node_id

    def add_shell_node(self, name, shell_code):
        """添加 Shell 节点"""
        node_info = create_node(self.client, self.workspace_id, name, 'SHELL', shell_code)
        self.nodes.append(node_info.node_id)
        return node_info.node_id

    def set_dependency(self, child_node_id, parent_node_id):
        """设置节点依赖"""
        set_node_dependency(self.client, self.workspace_id, child_node_id, [parent_node_id])

    def submit_all(self):
        """提交所有节点"""
        for node_id in self.nodes:
            # 获取节点对应的 file_id 并提交
            submit_node(self.client, self.workspace_id, node_id)
        print(f"已提交 {len(self.nodes)} 个节点")

# 使用示例
flow = DataDevelopmentFlow(client, 'your_workspace_id')

# 创建工作流
flow.create_workflow('daily_data_pipeline', '每日数据处理流程')

# 添加节点
extract_node = flow.add_sql_node('extract_data', 'SELECT * FROM source_table WHERE dt="${bizdate}"')
transform_node = flow.add_sql_node('transform_data', 'INSERT INTO target_table SELECT * FROM extract_result')
load_node = flow.add_shell_node('load_notification', 'echo "Data loading completed"')

# 设置依赖关系
flow.set_dependency(transform_node, extract_node)
flow.set_dependency(load_node, transform_node)

# 提交所有节点
flow.submit_all()
```

## 常见节点类型

| 节点类型 | 代码 | 说明 |
|----------|------|------|
| ODPS_SQL | ODPS_SQL | MaxCompute SQL 节点 |
| SHELL | SHELL | Shell 脚本节点 |
| Spark | SPARK | Spark 节点 |
| MR | MAPREDUCE | MapReduce 节点 |
| Python | PYTHON | Python 节点 |
| 虚拟节点 | VIRTUAL | 虚拟节点（用于控制依赖） |

## 注意事项

1. **节点类型**：创建节点时需指定正确的节点类型
2. **代码格式**：节点代码需符合对应语言的语法规范
3. **依赖关系**：避免循环依赖
4. **调度时间**：合理设置调度时间，避免资源冲突
5. **参数变量**：使用 `${变量名}` 格式引用调度参数

## 参考链接

- [API 目录](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [文件管理 OpenAPI 实践](file-management.md)
- [数据 ETL 操作全流程实践](etl-workflow.md)
