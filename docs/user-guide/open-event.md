# DataWorks 开放事件（OpenEvent）

## 简介

DataWorks 开放事件（OpenEvent）是一种事件订阅机制，允许您实时获取 DataWorks 内部各类事件的消息通知，实现与外部系统的深度集成。

## 核心概念

### 事件类型

DataWorks 支持多种事件类型，主要包括：

| 事件类别 | 事件类型 | 说明 |
|----------|----------|------|
| 数据开发 | 节点提交事件 | 节点提交时触发 |
| 数据开发 | 节点发布事件 | 节点发布时触发 |
| 运维中心 | 任务成功事件 | 任务运行成功时触发 |
| 运维中心 | 任务失败事件 | 任务运行失败时触发 |
| 运维中心 | 实例开始事件 | 实例开始运行时触发 |
| 运维中心 | 实例结束事件 | 实例运行结束时触发 |
| 审批中心 | 审批通过事件 | 审批通过时触发 |
| 审批中心 | 审批拒绝事件 | 审批拒绝时触发 |

### 消息格式

事件消息采用标准的 JSON 格式，包含以下字段：

```json
{
  "eventId": "事件唯一ID",
  "eventType": "事件类型",
  "eventTime": "事件时间",
  "workspaceId": "工作空间ID",
  "userId": "触发用户ID",
  "data": {
    // 事件详细数据
  }
}
```

## 使用场景

### 1. 任务状态监控

实时监控任务运行状态，及时发现和处理问题。

**示例：**
- 任务失败时发送钉钉/企业微信告警
- 任务成功时更新业务系统状态

### 2. 审计日志收集

收集 DataWorks 的操作日志，满足审计要求。

**示例：**
- 记录节点提交和发布操作
- 记录审批流程操作

### 3. 第三方系统集成

将 DataWorks 事件推送到第三方系统。

**示例：**
- 推送到消息队列（Kafka、RocketMQ）
- 推送到日志系统（SLS、ELK）
- 推送到监控系统（Prometheus、Grafana）

### 4. 自动化流程

根据事件触发自动化流程。

**示例：**
- 任务失败时自动重试
- 数据产出后自动触发下游处理

## 配置步骤

### 1. 创建 Event Bridge

1. 登录 [EventBridge 控制台](https://eventbridge.console.aliyun.com/)
2. 创建事件总线
3. 配置事件规则

### 2. 配置事件订阅

1. 登录 [DataWorks 控制台](https://dataworks.console.aliyun.com/)
2. 进入 **开放平台** > **开放事件**
3. 创建事件订阅
4. 选择要订阅的事件类型
5. 配置目标端点

### 3. 处理事件消息

在您的目标端点处理接收到的消息。

**示例代码（Python）：**

```python
import json
from flask import Flask, request

app = Flask(__name__)

@app.route('/webhook', methods=['POST'])
def handle_event():
    event = request.json
    event_type = event.get('eventType')
    event_data = event.get('data', {})

    if event_type == 'task.failed':
        # 处理任务失败事件
        send_alert(event_data)
    elif event_type == 'task.success':
        # 处理任务成功事件
        update_status(event_data)

    return {'status': 'ok'}

def send_alert(data):
    # 发送告警逻辑
    pass

def update_status(data):
    # 更新状态逻辑
    pass

if __name__ == '__main__':
    app.run(port=8080)
```

## 事件列表与消息格式

详细的事件列表和消息格式请参考：
[开发参考：事件列表与消息格式](https://help.aliyun.com/zh/dataworks/user-guide/open-platform/event-list)

## 最佳实践

1. **幂等性处理**：确保事件处理逻辑具有幂等性，避免重复处理
2. **错误重试**：实现合理的错误处理和重试机制
3. **监控告警**：监控事件处理状态，及时发现异常
4. **消息验证**：验证消息签名，确保消息来源可靠

## 参考链接

- [开放平台概述](open-platform.md)
- [开放 API（OpenAPI）](openapi.md)
- [EventBridge 文档](https://help.aliyun.com/zh/event-bridge)
