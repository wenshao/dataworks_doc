# DataWorks 开放平台概述

## 简介

DataWorks 开放平台提供了一系列开放能力，帮助企业实现与 DataWorks 的深度集成和定制化开发。

## 开放能力

### 1. 开放 API（OpenAPI）

通过调用 DataWorks 的 OpenAPI 使用 DataWorks 的各项功能，实现应用和 DataWorks 的集成和交互。

**主要能力：**
- 100+ API 功能
- 支持租户管理、元数据管理、数据开发、运维中心等功能模块
- 提供多语言 SDK 支持

**适用场景：**
- 批量操作
- 系统集成对接
- 自动化流程
- 定制化开发

### 2. 开放事件（OpenEvent）

通过事件订阅机制，实时获取 DataWorks 内部各类事件的消息通知。

**主要能力：**
- 实时事件推送
- 支持多种事件类型
- 灵活的订阅机制

**适用场景：**
- 任务状态监控
- 实时告警通知
- 审计日志收集
- 第三方系统集成

### 3. 扩展程序（Extensions）

允许第三方开发扩展程序，扩展 DataWorks 的功能。

**主要能力：**
- 事件回调机制
- 扩展点注册
- 自定义逻辑处理

**适用场景：**
- 自定义审批流程
- 复杂的校验逻辑
- 第三方系统集成
- 自定义消息通知

## 使用流程

### 1. 开通权限

确保您的阿里云账号具有以下权限：
- `AliyunDataWorksFullAccess` 或相关权限

### 2. 访问开放平台

1. 登录 [DataWorks 控制台](https://dataworks.console.aliyun.com/)
2. 切换至目标地域
3. 单击左侧导航栏的 **开放平台**

### 3. 选择开放能力

根据您的需求选择：
- **OpenAPI**：查看 API 文档、调用示例、使用情况
- **OpenEvent**：配置事件订阅
- **Extensions**：注册和管理扩展程序

## 开发资源

### 官方文档

- [OpenAPI 使用说明](https://help.aliyun.com/zh/dataworks/developer-reference/instructions-for-openapi/)
- [OpenAPI 目录](https://help.aliyun.com/zh/dataworks/developer-reference/api-dataworks-public-2024-05-18-dir/)
- [事件列表与消息格式](https://help.aliyun.com/zh/dataworks/user-guide/open-platform/event-list)

### 开发工具

- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [OpenAPI Explorer](https://api.aliyun.com/)
- [阿里云 SDK](https://help.aliyun.com/zh/sdk/)

### 最佳实践

- [文件管理 OpenAPI 基础实践](../best-practices/file-management.md)
- [表管理 OpenAPI 基础实践](../best-practices/table-management.md)
- [通过 OpenAPI 创建、修改、删除离线同步任务](../best-practices/sync-task.md)
- [数据开发、提交与运行 OpenAPI 基础实践](../best-practices/data-development.md)
- [数据 ETL 操作全流程实践](../best-practices/etl-workflow.md)

## 技术支持

如需技术支持，请通过以下方式：
1. 提交工单
2. 访问 [阿里云社区](https://developer.aliyun.com/)
3. 联系您的客户经理

## 参考链接

- [DataWorks 官方文档](https://help.aliyun.com/zh/dataworks)
- [OpenAPI 概述](../api/overview.md)
- [使用限制与计费](../api/limits.md)
