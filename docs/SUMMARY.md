---
title: 文档索引
id: doc-index
category: 索引
---

# DataWorks 文档索引

## 快速导航

| 读者类型 | 推荐路径 |
|----------|----------|
| **初学者** | [快速入门](00-quick-start/) → [核心概念](01-concepts/) → [任务教程](02-tasks/) |
| **进阶用户** | [API 参考](03-api/) → [参考资料](05-reference/) |
| **大模型** | [完整索引](#完整索引) |

---

## 完整索引

### 00 快速入门

- [快速入门指南](00-quick-start/index.md) - 5分钟上手 DataWorks OpenAPI
- [第一个 API 调用](00-quick-start/first-call.md) - 完整示例代码
- [10 个常见任务速查](00-quick-start/common-tasks.md) - 快速查找到需要的操作

### 01 核心概念

- [核心概念](01-concepts/index.md) - 工作空间、节点、工作流、实例等

### 02 任务教程

#### 数据同步
- [MySQL 同步到 MaxCompute](02-tasks/sync-data/mysql-to-odps.md)

#### 节点开发
- [创建 SQL 节点](02-tasks/develop-node/sql-node.md)
- [创建工作流](02-tasks/develop-node/workflow.md)

#### 表管理
- [查询数据表](02-tasks/manage-table/query-table.md)

#### 实例管理
- [运行节点](02-tasks/manage-instance/run-node.md)

### 03 API 参考

- [API 索引](03-api/index.md) - 完整的 API 分类列表

### 04 实战指南

- [ETL 数据处理全流程](04-guides/etl-pipeline.md)

### 05 参考资料

- [限制与配额](05-reference/limits.md) - QPS、调用次数、免费额度
- [错误码速查](05-reference/error-codes.md) - 错误码含义和解决方案
- [变更日志](05-reference/changelog.md) - API 版本变更记录

---

## 按场景查找

| 我想... | 教程 |
|---------|------|
| 同步数据 | [MySQL 到 MaxCompute](02-tasks/sync-data/mysql-to-odps.md) |
| 创建 SQL 任务 | [创建 SQL 节点](02-tasks/develop-node/sql-node.md) |
| 查询表信息 | [查询数据表](02-tasks/manage-table/query-table.md) |
| 运行任务 | [运行节点](02-tasks/manage-instance/run-node.md) |
| 构建 ETL | [ETL 全流程](04-guides/etl-pipeline.md) |

---

## 按功能查找

### 文件/节点管理
- CreateFile - 创建文件/节点
- UpdateFile - 修改文件/节点
- DeleteFile - 删除文件/节点
- GetFile - 获取文件详情
- ListFiles - 获取文件列表
- DeployFile - 提交发布

### 实例管理
- GetInstance - 获取实例详情
- ListInstances - 获取实例列表
- RunNode - 运行节点
- KillInstance - 终止实例

### 表管理
- GetTable - 获取表详情
- ListTables - 获取表列表

---

## 外部资源

- [阿里云 DataWorks 官方文档](https://help.aliyun.com/zh/dataworks)
- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [DataWorks 控制台](https://dataworks.console.aliyun.com/)
- [SDK 下载](https://help.aliyun.com/zh/sdk/)
