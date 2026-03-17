# DataWorks OpenAPI 概述

## 简介

DataWorks 开放平台提供 OpenAPI 能力，通过调用 DataWorks 的 OpenAPI 使用 DataWorks 的各项功能，实现应用和 DataWorks 的集成和交互。

## 核心能力

DataWorks 的 OpenAPI 提供 **100 多个 API 功能**，覆盖以下功能模块：

| 模块 | 说明 |
|------|------|
| 租户管理 | 工作空间管理、成员管理 |
| 元数据 | 表管理、文件管理、函数管理 |
| 数据开发 | 节点创建、修改、删除、发布 |
| 数据集成 | 离线同步任务、实时同步任务 |
| 运维中心 | 补数据、任务运维 |
| 数据质量 | 质量规则配置、运行监控 |
| 数据服务 | API 生成与管理 |

## API 版本

DataWorks 提供两个版本的 OpenAPI：

### 2024-05-18（新版 OpenAPI）

- **适用场景**：新版数据开发（Data Studio）
- **功能范围**：覆盖新版数据开发、数据目录等新功能
- **OpenAPI 门户**：[https://next.api.aliyun.com/document/dataworks-public](https://next.api.aliyun.com/document/dataworks-public)

> **重要**：如果您参加了新版数据开发公测，请使用 2024-05-18 版本。

### 2020-05-18（旧版 OpenAPI）

- **适用场景**：旧版数据开发
- **功能范围**：覆盖经典版数据开发功能
- **OpenAPI 门户**：[https://next.api.aliyun.com/document/dataworks-public](https://next.api.aliyun.com/document/dataworks-public)

## 支持的地域

| 地域 | 中文名称 |
|------|----------|
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

## 调用方式

| 调用方式 | 支持情况 | 说明 |
|----------|----------|------|
| 阿里云 SDK | 支持（推荐） | 支持 Java、Python、PHP、.NET、Go、Node.js、TypeScript 等多种语言 |
| OpenAPI Explorer | 支持 | 在线调试工具 |
| 阿里云 CLI | 支持 | 命令行工具 |
| HTTP/HTTPS REST | 支持 | RPC 风格接口 |

## 使用流程

1. **准备工作**
   - 开通 DataWorks 服务
   - 创建 AccessKey ID 和 AccessKey Secret
   - 确认账号具有 `AliyunDataWorksFullAccess` 权限

2. **调用 API**
   - 选择合适的 API 版本
   - 根据项目所在地域选择服务接入点
   - 使用 SDK 或 HTTP 方式调用

3. **监控与调优**
   - 通过开放平台查看 API 使用情况
   - 监控免费额度和调用次数
   - 合理控制调用并发

## 最佳实践

- 使用阿里云 SDK 可以简化开发，提高开发效率
- 合理控制调用频率，避免触发限流
- 关注 API 变更日志，及时适配变更
- 查看最佳实践文档，参考常见场景用法

## 参考链接

- [使用说明](https://help.aliyun.com/zh/dataworks/developer-reference/instructions-for-openapi/)
- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [支持的 API 列表](api-list.md)
