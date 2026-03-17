# 表管理 OpenAPI 基础实践

## 概述

本指南介绍如何使用 DataWorks OpenAPI 进行表管理操作，包括数据表的创建、查询、修改和删除。

## 前提条件

1. 已开通 DataWorks 服务
2. 已创建 AccessKey ID 和 AccessKey Secret
3. 账号具有 `AliyunDataWorksFullAccess` 权限
4. 已安装阿里云 SDK

## 安装 SDK

```bash
pip install alibabacloud-dataworks-public20240518
```

## 初始化客户端

```python
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models

def create_client(access_key_id, access_key_secret, region='cn-hangzhou'):
    config = open_api_models.Config(
        access_key_id=access_key_id,
        access_key_secret=access_key_secret,
        endpoint=f'dataworks.{region}.aliyuncs.com'
    )
    return Client(config)

client = create_client(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)
```

## 1. 查询表列表

使用 `ListTables` API 查询工作空间中的表列表。

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def list_tables(client, workspace_id, page_number=1, page_size=50):
    """查询表列表"""
    request = dataworks_models.ListTablesRequest(
        project_identifier=workspace_id,
        page_number=page_number,
        page_size=page_size
    )
    response = client.list_tables(request)
    return response.body.data

# 示例：查询前 50 张表
tables = list_tables(client, 'your_workspace_id')
for table in tables.tables:
    print(f"表名: {table.table_name}, 表ID: {table.table_id}")
```

## 2. 获取表详情

使用 `GetTable` API 获取表的详细信息。

```python
def get_table(client, workspace_id, table_name):
    """获取表详情"""
    request = dataworks_models.GetTableRequest(
        project_identifier=workspace_id,
        table_name=table_name
    )
    response = client.get_table(request)
    return response.body.data

# 示例：获取表详情
table_detail = get_table(client, 'your_workspace_id', 'your_table')
print(f"表名: {table_detail.table_name}")
print(f"表类型: {table_detail.table_type}")
print(f"创建时间: {table_detail.create_time}")
print(f"列信息: {table_detail.columns}")
```

## 3. 创建数据集

使用 `CreateDataset` API 创建数据集（用于数据目录管理）。

```python
def create_dataset(client, workspace_id, dataset_name, dataset_type):
    """创建数据集"""
    request = dataworks_models.CreateDatasetRequest(
        project_identifier=workspace_id,
        dataset_name=dataset_name,
        dataset_type=dataset_type
    )
    response = client.create_dataset(request)
    return response.body.data

# 示例：创建数据集
dataset = create_dataset(
    client=client,
    workspace_id='your_workspace_id',
    dataset_name='my_dataset',
    dataset_type='1'  # 1 表示普通数据集
)
print(f"数据集创建成功，数据集ID: {dataset.dataset_id}")
```

## 4. 查询数据集列表

使用 `ListDatasets` API 查询数据集列表。

```python
def list_datasets(client, workspace_id, page_number=1, page_size=20):
    """查询数据集列表"""
    request = dataworks_models.ListDatasetsRequest(
        project_identifier=workspace_id,
        page_number=page_number,
        page_size=page_size
    )
    response = client.list_datasets(request)
    return response.body.data

# 示例：查询数据集
datasets = list_datasets(client, 'your_workspace_id')
for dataset in datasets.datasets:
    print(f"数据集名称: {dataset.dataset_name}, 数据集ID: {dataset.dataset_id}")
```

## 5. 更新数据集

使用 `UpdateDataset` API 更新数据集信息。

```python
def update_dataset(client, workspace_id, dataset_id, new_name=None, new_comment=None):
    """更新数据集"""
    request = dataworks_models.UpdateDatasetRequest(
        project_identifier=workspace_id,
        dataset_id=dataset_id
    )
    if new_name:
        request.dataset_name = new_name
    if new_comment:
        request.comment = new_comment

    response = client.update_dataset(request)
    return response.body.data

# 示例：更新数据集
update_dataset(
    client=client,
    workspace_id='your_workspace_id',
    dataset_id='dataset_id',
    new_name='updated_dataset',
    new_comment='这是一个更新的数据集'
)
print("数据集更新成功")
```

## 6. 删除数据集

使用 `DeleteDataset` API 删除数据集。

```python
def delete_dataset(client, workspace_id, dataset_id):
    """删除数据集"""
    request = dataworks_models.DeleteDatasetRequest(
        project_identifier=workspace_id,
        dataset_id=dataset_id
    )
    response = client.delete_dataset(request)
    return response.body.data

# 示例：删除数据集
delete_dataset(client, 'your_workspace_id', 'dataset_id')
print("数据集删除成功")
```

## 7. 查询数据资产标签

使用 `ListDataAssetTags` API 查询数据资产标签。

```python
def list_asset_tags(client, workspace_id):
    """查询数据资产标签"""
    request = dataworks_models.ListDataAssetTagsRequest(
        project_identifier=workspace_id
    )
    response = client.list_data_asset_tags(request)
    return response.body.data

# 示例：查询标签
tags = list_asset_tags(client, 'your_workspace_id')
for tag in tags.tags:
    print(f"标签名称: {tag.tag_name}, 标签ID: {tag.tag_id}")
```

## 8. 查询数据资产

使用 `ListDataAssets` API 查询数据资产。

```python
def list_data_assets(client, workspace_id, asset_type=None):
    """查询数据资产"""
    request = dataworks_models.ListDataAssetsRequest(
        project_identifier=workspace_id
    )
    if asset_type:
        request.asset_type = asset_type

    response = client.list_data_assets(request)
    return response.body.data

# 示例：查询数据资产
assets = list_data_assets(client, 'your_workspace_id', asset_type='table')
for asset in assets.assets:
    print(f"资产名称: {asset.asset_name}, 资产类型: {asset.asset_type}")
```

## 完整示例：表管理工具类

```python
from typing import List, Optional
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class TableManager:
    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def list_tables(self, workspace_id: str, page_number: int = 1, page_size: int = 50):
        """查询表列表"""
        request = dataworks_models.ListTablesRequest(
            project_identifier=workspace_id,
            page_number=page_number,
            page_size=page_size
        )
        response = self.client.list_tables(request)
        return response.body.data

    def get_table(self, workspace_id: str, table_name: str):
        """获取表详情"""
        request = dataworks_models.GetTableRequest(
            project_identifier=workspace_id,
            table_name=table_name
        )
        response = self.client.get_table(request)
        return response.body.data

    def create_dataset(self, workspace_id: str, dataset_name: str, dataset_type: str):
        """创建数据集"""
        request = dataworks_models.CreateDatasetRequest(
            project_identifier=workspace_id,
            dataset_name=dataset_name,
            dataset_type=dataset_type
        )
        response = self.client.create_dataset(request)
        return response.body.data

    def list_datasets(self, workspace_id: str, page_number: int = 1, page_size: int = 20):
        """查询数据集列表"""
        request = dataworks_models.ListDatasetsRequest(
            project_identifier=workspace_id,
            page_number=page_number,
            page_size=page_size
        )
        response = self.client.list_datasets(request)
        return response.body.data

    def update_dataset(self, workspace_id: str, dataset_id: str, **kwargs):
        """更新数据集"""
        request = dataworks_models.UpdateDatasetRequest(
            project_identifier=workspace_id,
            dataset_id=dataset_id,
            **kwargs
        )
        response = self.client.update_dataset(request)
        return response.body.data

    def delete_dataset(self, workspace_id: str, dataset_id: str):
        """删除数据集"""
        request = dataworks_models.DeleteDatasetRequest(
            project_identifier=workspace_id,
            dataset_id=dataset_id
        )
        response = self.client.delete_dataset(request)
        return response.body.data
```

## 注意事项

1. **表权限**：确保账号具有表的相应操作权限
2. **表存在性检查**：操作前建议先检查表是否存在
3. **分页处理**：表数量较多时注意分页处理
4. **调用频率**：注意 QPS 限制，批量操作时做好节流

## 参考链接

- [API 目录](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [文件管理 OpenAPI 实践](file-management.md)
