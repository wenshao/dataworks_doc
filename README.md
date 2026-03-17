# 阿里云 DataWorks 文档

> 大数据开发治理平台 DataWorks OpenAPI 文档整理

## 📖 简介

阿里云 DataWorks 是基于 MaxCompute/EMR/MC-Hologres 等大数据计算引擎的一站式大数据开发与治理平台。每天阿里巴巴集团内部有数万名数据/算法工程师使用 DataWorks，承担集团 99% 数据业务构建。

本文档整理了 DataWorks OpenAPI 的使用方法、最佳实践和参考资料。

## 📚 文档结构

```
docs/
├── 00-quick-start/       # 快速入门（5分钟上手）
│   ├── index.md          # 入门指南
│   ├── first-call.md     # 第一个API调用
│   └── common-tasks.md   # 10个常见任务
│
├── 01-concepts/          # 核心概念
│   └── index.md          # 工作空间、节点、实例等
│
├── 02-tasks/             # 任务教程
│   ├── sync-data/        # 数据同步
│   ├── develop-node/     # 节点开发
│   ├── manage-table/     # 表管理
│   └── manage-instance/  # 实例管理
│
├── 03-api/               # API参考
│   ├── file/             # 文件管理API
│   ├── node/             # 节点API
│   ├── workflow/         # 工作流API
│   ├── instance/         # 实例API
│   └── table/            # 表API
│
├── 04-guides/            # 实战指南
│   └── etl-pipeline.md   # ETL全流程
│
├── 05-reference/         # 参考资料
│   ├── limits.md         # 限制与配额
│   ├── error-codes.md    # 错误码速查
│   └── changelog.md      # 变更日志
│
└── overview/             # 产品概述
    └── competitors.md    # 竞品对比
```

## 🚀 快速开始

| 读者类型 | 推荐路径 |
|----------|----------|
| **初学者** | [快速入门](docs/00-quick-start/) → [核心概念](docs/01-concepts/) → [任务教程](docs/02-tasks/) |
| **进阶用户** | [API参考](docs/03-api/) → [参考资料](docs/05-reference/) |

## 🎯 常见任务

| 任务 | 教程 |
|------|------|
| 同步数据 | [MySQL 到 MaxCompute](docs/02-tasks/sync-data/mysql-to-odps.md) |
| 创建 SQL 任务 | [创建 SQL 节点](docs/02-tasks/develop-node/sql-node.md) |
| 查询表信息 | [查询数据表](docs/02-tasks/manage-table/query-table.md) |
| 运行任务 | [运行节点](docs/02-tasks/manage-instance/run-node.md) |
| 构建 ETL | [ETL 全流程](docs/04-guides/etl-pipeline.md) |

## 🔗 相关链接

- [官方文档](https://help.aliyun.com/zh/dataworks)
- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [控制台](https://dataworks.console.aliyun.com/)

## 📄 许可证

本项目采用 [Apache License 2.0](LICENSE) 许可证。

---

> 本文档内容整理自阿里云官方文档，仅供参考使用。请以官方最新文档为准。
