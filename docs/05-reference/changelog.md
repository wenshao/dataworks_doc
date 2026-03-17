---
title: 变更日志
id: reference-changelog
category: 参考资料
tags: [changelog, updates]
---

# API 变更日志

## 2024-05-18 版本

当前主要 API 版本。

### 2025-05-19

| API | 变更内容 | 影响 |
|-----|----------|------|
| `ListDataAssetTags` | 内部配置变更 | 无影响 |
| `ListDataAssets` | 内部配置变更 | 无影响 |

### 2025-05-15

| API | 变更内容 | 影响 |
|-----|----------|------|
| `UpdateWorkflow` | 入参变更 | 可能需要适配 |

### 2025-05-06

| API | 变更内容 | 影响 |
|-----|----------|------|
| `CreateResource` | 入参变更 | 可能需要适配 |
| `UpdateResource` | 入参变更 | 可能需要适配 |

### 2025-04-02

| API | 变更内容 | 影响 |
|-----|----------|------|
| `ListNodes` | 返回结构变更 | 可能需要适配 |

## 2020-05-18 版本

旧版 API，仍可用但不推荐新项目使用。

## 变更类型说明

| 类型 | 说明 | 是否需要适配 |
|------|------|--------------|
| 内部配置变更 | API 内部实现变化，调用接口不变 | 否 |
| 入参变更 | 请求参数发生变化 | 是 |
| 返回结构变更 | 响应结构发生变化 | 是 |
| 新增 API | 新增功能 | - |
| 废弃 API | API 即将下线 | 是，需要迁移 |

## 获取最新变更

- 访问 [官方版本说明](https://help.aliyun.com/zh/dataworks/developer-reference/api-dataworks-public-2024-05-18-changeset)
- 订阅 DataWorks 产品动态
- 关注本文档更新
