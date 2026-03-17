---
title: 查询数据表信息
id: task-query-table
category: 表管理
difficulty: 初级
time: 5分钟
prerequisites:
  - 已有工作空间
  - 表已存在
apis:
  - GetTable
  - ListTables
related:
  - 创建数据表
  - 修改表结构
tags: [table, query, metadata]
---

# 查询数据表信息

本教程演示如何查询 MaxCompute 表的详细信息。

## 场景说明

| 项目 | 说明 |
|------|------|
| **查询目标** | MaxCompute 表 |
| **获取内容** | 表结构、分区信息、生命周期等 |
| **用途** | 数据探索、元数据管理 |

## 步骤一：查询单个表详情

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def get_table_info(workspace_id, table_name):
    """获取表详情"""
    request = dataworks_models.GetTableRequest(
        project_identifier=workspace_id,
        table_name=table_name
    )
    response = client.get_table(request)
    return response.body.data

# 使用
table = get_table_info('your_workspace_id', 'user_info')

print(f"表名: {table.table_name}")
print(f"表类型: {table.table_type}")
print(f"创建时间: {table.create_time}")
print(f"生命周期: {table.life_cycle} 天")

# 打印列信息
print("\n列信息:")
for col in table.columns:
    print(f"  {col.name} ({col.type}) - {col.comment}")
```

## 步骤二：查询表列表

```python
def list_tables(workspace_id, page=1, size=20):
    """获取表列表"""
    request = dataworks_models.ListTablesRequest(
        project_identifier=workspace_id,
        page_number=page,
        page_size=size
    )
    response = client.list_tables(request)
    return response.body.data

# 使用
result = list_tables('your_workspace_id', page=1, size=50)

print(f"共 {result.total_count} 张表，当前显示 {len(result.tables)} 张:\n")
for table in result.tables:
    print(f"  {table.table_name} - {table.table_comment}")
```

## 步骤三：搜索表

```python
def search_tables(workspace_id, keyword):
    """搜索表名包含关键词的表"""
    tables = []
    page = 1

    while True:
        request = dataworks_models.ListTablesRequest(
            project_identifier=workspace_id,
            page_number=page,
            page_size=100
        )
        response = client.list_tables(request)
        data = response.body.data

        # 过滤包含关键词的表
        for table in data.tables:
            if keyword.lower() in table.table_name.lower():
                tables.append(table)

        # 检查是否还有下一页
        if len(data.tables) < 100:
            break
        page += 1

    return tables

# 使用
results = search_tables('your_workspace_id', 'user')
for table in results:
    print(f"  {table.table_name}")
```

## 步骤四：获取分区信息

```python
def get_table_partitions(workspace_id, table_name):
    """获取表的分区信息"""
    request = dataworks_models.GetTableRequest(
        project_identifier=workspace_id,
        table_name=table_name
    )
    response = client.get_table(request)
    table = response.body.data

    if table.partition_keys:
        print(f"分区键: {[pk.name for pk in table.partition_keys]}")
    else:
        print("非分区表")

# 使用
get_table_partitions('your_workspace_id', 'ods_user_log')
```

## 完整工具类

```python
from typing import List, Optional
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class TableManager:
    """表管理工具类"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def get(self, workspace_id: str, table_name: str):
        """获取表详情"""
        request = dataworks_models.GetTableRequest(
            project_identifier=workspace_id,
            table_name=table_name
        )
        return self.client.get_table(request).body.data

    def list_all(self, workspace_id: str) -> List:
        """获取所有表（自动翻页）"""
        all_tables = []
        page = 1

        while True:
            request = dataworks_models.ListTablesRequest(
                project_identifier=workspace_id,
                page_number=page,
                page_size=100
            )
            response = self.client.list_tables(request)
            data = response.body.data

            all_tables.extend(data.tables)

            if len(data.tables) < 100:
                break
            page += 1

        return all_tables

    def search(self, workspace_id: str, keyword: str) -> List:
        """搜索表"""
        tables = self.list_all(workspace_id)
        return [t for t in tables if keyword.lower() in t.table_name.lower()]

    def get_columns(self, workspace_id: str, table_name: str) -> List[dict]:
        """获取列信息"""
        table = self.get(workspace_id, table_name)
        return [
            {
                'name': col.name,
                'type': col.type,
                'comment': col.comment,
                'is_partition': any(pk.name == col.name for pk in (table.partition_keys or []))
            }
            for col in (table.columns or [])
        ]

    def print_table(self, workspace_id: str, table_name: str):
        """打印表信息"""
        table = self.get(workspace_id, table_name)

        print(f"表名: {table.table_name}")
        print(f"注释: {table.table_comment}")
        print(f"类型: {table.table_type}")
        print(f"大小: {table.table_size or 0} 字节")
        print(f"行数: {table.row_count or 0}")
        print(f"创建时间: {table.create_time}")
        print(f"修改时间: {table.modified_time}")
        print(f"拥有者: {table.owner_id}")

        # 分区键
        if table.partition_keys:
            print(f"\n分区键: {', '.join(pk.name for pk in table.partition_keys)}")

        # 列信息
        print("\n列信息:")
        print(f"{'列名':<20} {'类型':<20} {'分区':<6} {'注释'}")
        print("-" * 70)
        for col in (table.columns or []):
            is_part = any(pk.name == col.name for pk in (table.partition_keys or []))
            print(f"{col.name:<20} {col.type:<20} {'是' if is_part else '否':<6} {col.comment or ''}")

# 使用示例
manager = TableManager(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 打印表信息
manager.print_table('your_workspace_id', 'user_info')

# 搜索表
results = manager.search('your_workspace_id', 'user')
print(f"\n找到 {len(results)} 张包含 'user' 的表")
```

## 响应数据结构

```python
# GetTable 响应主要字段
{
    "table_name": str,          # 表名
    "table_comment": str,       # 表注释
    "table_type": str,          # 表类型
    "table_size": int,          # 表大小（字节）
    "row_count": int,           # 行数
    "create_time": str,         # 创建时间
    "modified_time": str,       # 修改时间
    "owner_id": str,            # 拥有者
    "last_modify_time": str,    # 最后修改时间
    "life_cycle": int,          # 生命周期（天）
    "view_text": str,           # 视图文本（如果是视图）
    "is_partition": bool,       # 是否分区表
    "partition_keys": [         # 分区键
        {"name": "dt", "type": "string", "comment": "日期分区"}
    ],
    "columns": [                # 列信息
        {"name": "user_id", "type": "bigint", "comment": "用户ID"},
        {"name": "user_name", "type": "string", "comment": "用户名"}
    ]
}
```

## 相关教程

- [创建数据表](create-table.md)
- [修改表结构](alter-table.md)
- [数据表 API 参考](../../03-api/table/index.md)
