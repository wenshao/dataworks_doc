# DataWorks 文档

> 阿里云 DataWorks OpenAPI 文档 - 对初学者、进阶用户和大模型都友好

## 📖 文档导航

### [快速入门](00-quick-start/)
5 分钟上手，第一个 API 调用，常见任务速查

### [核心概念](01-concepts/)
工作空间、节点、工作流、实例等核心概念

### [任务教程](02-tasks/)
按任务分类的实用教程
- [数据同步](02-tasks/sync-data/)
- [节点开发](02-tasks/develop-node/)
- [表管理](02-tasks/manage-table/)
- [实例管理](02-tasks/manage-instance/)

### [API 参考](03-api/)
完整的 API 参考文档

### [实战指南](04-guides/)
ETL 流程、数据仓库搭建等实战案例

### [参考资料](05-reference/)
错误码、限制配额、变更日志

## 🚀 快速开始

```python
# 安装 SDK
pip install alibabacloud-dataworks-public20240518

# 查询工作空间
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

config = open_api_models.Config(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET',
    endpoint='dataworks.cn-hangzhou.aliyuncs.com'
)
client = Client(config)

response = client.list_workspaces(dataworks_models.ListWorkspacesRequest())
```

## 📚 文档特点

- 📗 **初学者友好**：清晰的学习路径，从入门到精通
- 📕 **进阶用户友好**：完整的 API 参考，实战案例
- 📘 **大模型友好**：结构化元数据，易于理解和检索

## 🔗 相关链接

- [官方文档](https://help.aliyun.com/zh/dataworks)
- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [控制台](https://dataworks.console.aliyun.com/)

## 📄 许可证

Apache License 2.0
