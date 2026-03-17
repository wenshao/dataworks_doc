# 文件管理 OpenAPI 基础实践

## 概述

本指南介绍如何使用 DataWorks OpenAPI 进行文件管理操作，包括文件的创建、查询、修改和删除。

## 前提条件

1. 已开通 DataWorks 服务
2. 已创建 AccessKey ID 和 AccessKey Secret
3. 账号具有 `AliyunDataWorksFullAccess` 权限
4. 已安装阿里云 SDK

## 安装 SDK

### Python SDK

```bash
pip install alibabacloud-dataworks-public20240518
```

### Java SDK

```xml
<dependency>
    <groupId>com.aliyun</groupId>
    <artifactId>dataworks-public20240518</artifactId>
    <version>最新版本</version>
</dependency>
```

## 初始化客户端

```python
from alibabacloud_dataworks_public20240518.client import Client as DataworksPublicClient
from alibabacloud_tea_openapi import models as open_api_models

config = open_api_models.Config(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET',
    endpoint_id='dataworks.cn-hangzhou.aliyuncs.com'
)
client = DataworksPublicClient(config)
```

## 1. 创建文件

使用 `CreateFile` API 创建新文件。

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def create_file(client, workspace_id, file_name, file_type, content):
    request = dataworks_models.CreateFileRequest(
        project_identifier=workspace_id,
        file_name=file_name,
        file_type=file_type,
        content=content
    )
    response = client.create_file(request)
    return response.body.data

# 示例：创建一个 SQL 文件
file_info = create_file(
    client=client,
    workspace_id='your_workspace_id',
    file_name='example.sql',
    file_type='10',  # 10 表示 SQL 文件
    content='SELECT * FROM your_table;'
)
print(f"文件创建成功，文件ID: {file_info.file_id}")
```

## 2. 查询文件列表

使用 `ListFiles` API 查询文件列表。

```python
def list_files(client, workspace_id, page_number=1, page_size=20):
    request = dataworks_models.ListFilesRequest(
        project_identifier=workspace_id,
        page_number=page_number,
        page_size=page_size
    )
    response = client.list_files(request)
    return response.body.data.files

# 示例：查询文件列表
files = list_files(client, 'your_workspace_id')
for file in files:
    print(f"文件ID: {file.file_id}, 文件名: {file.file_name}")
```

## 3. 获取文件详情

使用 `GetFile` API 获取文件详情。

```python
def get_file(client, workspace_id, file_id):
    request = dataworks_models.GetFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.get_file(request)
    return response.body.data

# 示例：获取文件详情
file_detail = get_file(client, 'your_workspace_id', 'file_id')
print(f"文件名: {file_detail.file_name}")
print(f"文件类型: {file_detail.file_type}")
print(f"文件内容: {file_detail.content}")
```

## 4. 更新文件

使用 `UpdateFile` API 更新文件内容。

```python
def update_file(client, workspace_id, file_id, new_content):
    request = dataworks_models.UpdateFileRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        content=new_content
    )
    response = client.update_file(request)
    return response.body.data

# 示例：更新文件内容
update_file(
    client=client,
    workspace_id='your_workspace_id',
    file_id='file_id',
    new_content='SELECT * FROM updated_table;'
)
print("文件更新成功")
```

## 5. 删除文件

使用 `DeleteFile` API 删除文件。

```python
def delete_file(client, workspace_id, file_id):
    request = dataworks_models.DeleteFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.delete_file(request)
    return response.body.data

# 示例：删除文件
delete_file(client, 'your_workspace_id', 'file_id')
print("文件删除成功")
```

## 6. 发布文件

使用 `DeployFile` API 将文件发布到生产环境。

```python
def deploy_file(client, workspace_id, file_id):
    request = dataworks_models.DeployFileRequest(
        project_identifier=workspace_id,
        file_id=file_id
    )
    response = client.deploy_file(request)
    return response.body.data

# 示例：发布文件
deploy_file(client, 'your_workspace_id', 'file_id')
print("文件发布成功")
```

## 完整示例

```python
import os
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class DataWorksFileManager:
    def __init__(self, access_key_id, access_key_secret, endpoint):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=endpoint
        )
        self.client = Client(config)

    def create_file(self, workspace_id, file_name, file_type, content):
        """创建文件"""
        request = dataworks_models.CreateFileRequest(
            project_identifier=workspace_id,
            file_name=file_name,
            file_type=file_type,
            content=content
        )
        response = self.client.create_file(request)
        return response.body.data

    def list_files(self, workspace_id, page_number=1, page_size=20):
        """查询文件列表"""
        request = dataworks_models.ListFilesRequest(
            project_identifier=workspace_id,
            page_number=page_number,
            page_size=page_size
        )
        response = self.client.list_files(request)
        return response.body.data.files

    def get_file(self, workspace_id, file_id):
        """获取文件详情"""
        request = dataworks_models.GetFileRequest(
            project_identifier=workspace_id,
            file_id=file_id
        )
        response = self.client.get_file(request)
        return response.body.data

    def update_file(self, workspace_id, file_id, new_content):
        """更新文件"""
        request = dataworks_models.UpdateFileRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            content=new_content
        )
        response = self.client.update_file(request)
        return response.body.data

    def delete_file(self, workspace_id, file_id):
        """删除文件"""
        request = dataworks_models.DeleteFileRequest(
            project_identifier=workspace_id,
            file_id=file_id
        )
        response = self.client.delete_file(request)
        return response.body.data

# 使用示例
if __name__ == '__main__':
    manager = DataWorksFileManager(
        access_key_id=os.getenv('ALIYUN_ACCESS_KEY_ID'),
        access_key_secret=os.getenv('ALIYUN_ACCESS_KEY_SECRET'),
        endpoint='dataworks.cn-hangzhou.aliyuncs.com'
    )

    # 创建文件
    file_info = manager.create_file(
        workspace_id='your_workspace_id',
        file_name='example.sql',
        file_type='10',
        content='SELECT * FROM your_table;'
    )
    print(f"文件创建成功，文件ID: {file_info.file_id}")
```

## 错误处理

```python
from alibabacloud_tea_util import models as util_models

try:
    file_info = create_file(client, 'workspace_id', 'test.sql', '10', 'SELECT 1')
except util_models.TeaException as e:
    print(f"错误码: {e.code}")
    print(f"错误信息: {e.message}")
    print(f"请求ID: {e.data.get('RequestId')}")
except Exception as e:
    print(f"未知错误: {str(e)}")
```

## 注意事项

1. **文件类型代码**：不同文件类型对应不同的代码，如 SQL 文件为 '10'
2. **权限验证**：确保账号具有相应权限
3. **调用频率**：注意 QPS 限制，避免触发限流
4. **错误处理**：做好异常捕获和重试机制

## 参考链接

- [API 目录](../api/api-list.md)
- [使用限制与计费](../api/limits.md)
- [表管理 OpenAPI 实践](table-management.md)
