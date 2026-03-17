# 开放 API（OpenAPI）使用指南

## 简介

DataWorks 开放平台提供 OpenAPI 能力，通过调用 DataWorks 的 OpenAPI 使用 DataWorks 的各项功能，实现应用和 DataWorks 的集成和交互。

## 功能概述

DataWorks 的 OpenAPI 提供 **100 多个 API 功能**，可以快速进行批量操作和系统集成对接等工作，提高数据开发效率，满足企业定制化需求。

## 使用限制

### 版本要求

各功能模块的 OpenAPI 对 DataWorks 的版本要求与功能本身对 DataWorks 版本的要求一致。

### 可用地域

| 地域 | 名称 |
|------|------|
| cn-hangzhou | 华东1（杭州） |
| cn-shanghai | 华东2（上海） |
| cn-shenzhen | 华南1（深圳） |
| cn-beijing | 华北2（北京） |
| cn-zhangjiakou | 华北3（张家口） |
| cn-beijing-finance-1 | 北京政务云 |
| cn-shanghai-finance-1 | 华东2（上海）金融云 |
| cn-chengdu | 西南1（成都） |
| cn-hongkong | 中国（香港） |
| ap-southeast-1 | 亚太东南1（新加坡） |
| ap-southeast-5 | 印度尼西亚（雅加达） |
| us-silicon-valley | 美国（硅谷） |
| us-virginia | 美国（弗吉尼亚） |
| eu-central-1 | 德国（法兰克福） |
| eu-west-1 | 英国（伦敦） |
| ap-northeast-1 | 日本（东京） |
| ap-southeast-3 | 马来西亚（吉隆坡） |

## OpenAPI 功能区

### OpenAPI 免费额度

可以查看每个自然月截止当前日期 API 的免费额度使用情况。不同版本的 DataWorks 会为您提供不同的免费调用额度，免费额度使用完后，超出部分会按量计费。

### OpenAPI 调用次数

可以查看每个自然月截止当前日期所有 OpenAPI 的总调用次数，按自然月累计，每月 1 日清零重新累计。

### OpenAPI 调用明细

可以查看特定时间段内 API 的调用信息，包括：
- 调用日期
- API 类别
- 当天调用次数
- 本月累计调用次数

您可以通过 API 类型筛选所要查找的 API 信息。

## 访问开发者后台

1. 登录 [DataWorks 控制台](https://dataworks.console.aliyun.com/)
2. 切换至目标地域
3. 单击左侧导航栏的 **开放平台** > **开发者后台**

## 最佳实践

DataWorks 提供以下 OpenAPI 最佳实践文档：

| 实践主题 | 描述 |
|----------|------|
| 文件管理 OpenAPI 基础实践 | 文件的创建、查询、修改、删除 |
| 表管理 OpenAPI 基础实践 | 数据表的创建、查询、修改、删除 |
| 通过 OpenAPI 创建、修改、删除离线同步任务 | 数据集成同步任务的完整生命周期管理 |
| 数据开发、提交与运行 OpenAPI 基础实践 | 节点的开发、提交、运行全流程 |
| 数据 ETL 操作全流程实践 | 端到端的 ETL 数据处理流程 |

## 参考链接

- [支持的 API 列表](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [最佳实践：文件管理](../best-practices/file-management.md)
- [最佳实践：表管理](../best-practices/table-management.md)
- [最佳实践：离线同步任务](../best-practices/sync-task.md)
- [最佳实践：数据开发](../best-practices/data-development.md)
- [最佳实践：ETL 工作流](../best-practices/etl-workflow.md)
