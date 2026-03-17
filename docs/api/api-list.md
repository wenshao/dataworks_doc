# DataWorks OpenAPI 目录

本文档列出 DataWorks OpenAPI (2024-05-18) 的所有 API 分类。

## API 分类

### 认证文件管理

| API | 说明 |
|-----|------|
| CreateAuthToken | 创建认证 Token |
| DeleteAuthToken | 删除认证 Token |
| ListAuthTokens | 获取认证 Token 列表 |
| UpdateAuthToken | 更新认证 Token |

### 空间管理

| API | 说明 |
|-----|------|
| CreateWorkspace | 创建工作空间 |
| DeleteWorkspace | 删除工作空间 |
| ListWorkspaces | 获取工作空间列表 |
| UpdateWorkspace | 更新工作空间 |

### 数据源

| API | 说明 |
|-----|------|
| CreateDataSource | 创建数据源 |
| DeleteDataSource | 删除数据源 |
| GetDataDataSource | 获取数据源详情 |
| ListDataSources | 获取数据源列表 |
| UpdateDataSource | 更新数据源 |

### 计算资源

| API | 说明 |
|-----|------|
| CreateCalculateEngine | 创建计算资源 |
| DeleteCalculateEngine | 删除计算资源 |
| ListCalculateEngines | 获取计算资源列表 |
| UpdateCalculateEngine | 更新计算资源 |

### 资源组管理

| API | 说明 |
|-----|------|
| CreateResourceGroup | 创建资源组 |
| DeleteResourceGroup | 删除资源组 |
| ListResourceGroups | 获取资源组列表 |
| UpdateResourceGroup | 更新资源组 |

### 数据开发（新版）

| API | 说明 |
|-----|------|
| CreateFile | 创建文件 |
| DeleteFile | 删除文件 |
| GetFile | 获取文件详情 |
| ListFiles | 获取文件列表 |
| UpdateFile | 更新文件 |
| DeployFile | 发布文件 |
| CreateNode | 创建节点 |
| DeleteNode | 删除节点 |
| GetNode | 获取节点详情 |
| ListNodes | 获取节点列表 |
| UpdateNode | 更新节点 |
| RunNode | 运行节点 |
| CreateWorkflow | 创建工作流 |
| DeleteWorkflow | 删除工作流 |
| GetWorkflow | 获取工作流详情 |
| ListWorkflows | 获取工作流列表 |
| UpdateWorkflow | 更新工作流 |
| CreateResource | 创建资源 |
| DeleteResource | 删除资源 |
| GetResource | 获取资源详情 |
| ListResources | 获取资源列表 |
| UpdateResource | 更新资源 |
| CreateFunction | 创建函数 |
| DeleteFunction | 删除函数 |
| GetFunction | 获取函数详情 |
| ListFunctions | 获取函数列表 |
| UpdateFunction | 更新函数 |

### 数据开发（旧版）

| API | 说明 |
|-----|------|
| CreateFile (旧版) | 创建文件 |
| DeleteFile (旧版) | 删除文件 |
| GetFile (旧版) | 获取文件详情 |
| ListFiles (旧版) | 获取文件列表 |
| UpdateFile (旧版) | 更新文件 |
| DeployFile (旧版) | 发布文件 |

### 数据集成

| API | 说明 |
|-----|------|
| CreateConnection | 创建数据源连接 |
| DeleteConnection | 删除数据源连接 |
| GetConnection | 获取数据源连接详情 |
| ListConnections | 获取数据源连接列表 |
| UpdateConnection | 更新数据源连接 |
| CreateSyncTask | 创建同步任务 |
| DeleteSyncTask | 删除同步任务 |
| GetSyncTask | 获取同步任务详情 |
| ListSyncTasks | 获取同步任务列表 |
| UpdateSyncTask | 更新同步任务 |
| RunSyncTask | 运行同步任务 |

### 数据地图

| API | 说明 |
|-----|------|
| ListDataAssets | 获取数据资产列表 |
| ListDataAssetTags | 获取数据资产标签 |
| GetTable | 获取表详情 |
| ListTables | 获取表列表 |
| CreateDataset | 创建数据集 |
| DeleteDataset | 删除数据集 |
| GetDataset | 获取数据集详情 |
| ListDatasets | 获取数据集列表 |
| UpdateDataset | 更新数据集 |
| ListDatasetVersions | 获取数据集版本列表 |

### 运维中心

| API | 说明 |
|-----|------|
| CreateManualDag | 创建手动补数据实例 |
| GetInstance | 获取实例详情 |
| ListInstances | 获取实例列表 |
| KillInstance | 终止实例 |
| ResumeInstance | 恢复运行实例 |
| SuspendInstance | 暂停实例 |

### 数据质量

| API | 说明 |
|-----|------|
| CreateQualityRule | 创建质量规则 |
| DeleteQualityRule | 删除质量规则 |
| GetQualityRule | 获取质量规则详情 |
| ListQualityRules | 获取质量规则列表 |
| UpdateQualityRule | 更新质量规则 |

### 标签管理

| API | 说明 |
|-----|------|
| CreateTag | 创建标签 |
| DeleteTag | 删除标签 |
| GetTag | 获取标签详情 |
| ListTags | 获取标签列表 |
| UpdateTag | 更新标签 |

### 开放平台

| API | 说明 |
|-----|------|
| CreateExtension | 创建扩展程序 |
| DeleteExtension | 删除扩展程序 |
| GetExtension | 获取扩展程序详情 |
| ListExtensions | 获取扩展程序列表 |
| UpdateExtension | 更新扩展程序 |

## API 风格

DataWorks OpenAPI 采用 **RPC 风格**接口。

## 查看完整 API 文档

- [OpenAPI 门户](https://next.api.aliyun.com/document/dataworks-public)
- [API 参考文档](https://help.aliyun.com/zh/dataworks/developer-reference/api-dataworks-public-2024-05-18-dir/)
