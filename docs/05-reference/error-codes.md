---
title: 错误码速查
id: reference-error-codes
category: 参考资料
tags: [error, troubleshooting]
---

# 错误码速查

## 认证相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidAccessKey.NotFound` | AccessKey 不存在 | 检查 AccessKey ID 是否正确 |
| `InvalidAccessKey.Secret` | AccessKey Secret 错误 | 检查 AccessKey Secret 是否正确 |
| `Forbidden.AccessKey` | AccessKey 被禁用 | 检查账号状态，启用 AccessKey |
| `Forbidden.AccessKey.Disabled` | AccessKey 已禁用 | 在控制台启用 AccessKey |

## 权限相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `Forbidden.RAM` | 没有权限 | 授予 `AliyunDataWorksFullAccess` 权限 |
| `Forbidden.User.NotInWorkspace` | 用户不在工作空间中 | 加入工作空间 |
| `Forbidden.Resource.NoPermission` | 资源无权限 | 检查资源权限配置 |

## 限流相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `Throttling` | 触发限流 | 降低调用频率 |
| `Throttling.User` | 用户级限流 | 等待后重试 |
| `Throttling.Api` | API 级限流 | 降低该 API 调用频率 |
| `Throttling.System` | 系统限流 | 稍后重试 |

## 资源相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidParameter.WorkspaceId` | 工作空间ID错误 | 检查工作空间ID |
| `Workspace.NotFound` | 工作空间不存在 | 检查工作空间是否存在 |
| `File.NotFound` | 文件不存在 | 检查文件ID是否正确 |
| `Node.NotFound` | 节点不存在 | 检查节点ID是否正确 |
| `Instance.NotFound` | 实例不存在 | 检查实例ID是否正确 |
| `Table.NotFound` | 表不存在 | 检查表名是否正确 |

## 参数相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidParameter` | 参数错误 | 检查请求参数 |
| `InvalidParameter.FileType` | 文件类型错误 | 检查 file_type 参数 |
| `InvalidParameter.CronExpression` | Cron表达式错误 | 检查 Cron 表达式格式 |
| `InvalidParameter.BizDate` | 业务日期格式错误 | 使用 YYYYMMDD 格式 |
| `MissingParameter` | 缺少必填参数 | 检查必填参数是否完整 |

## 状态相关

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InvalidStatus.Workflow` | 工作流状态异常 | 检查工作流状态 |
| `InvalidStatus.Node` | 节点状态异常 | 检查节点状态 |
| `InvalidStatus.Instance` | 实例状态异常 | 检查实例状态 |
| `OperationDenied.InstanceStatus` | 实例状态不允许操作 | 等待实例完成后再操作 |

## 其他常见错误

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| `InternalError` | 内部错误 | 联系技术支持，提供 RequestId |
| `ServiceUnavailable` | 服务不可用 | 稍后重试 |
| `RequestTimeout` | 请求超时 | 检查网络，增加超时时间 |
| `Duplicate` | 资源已存在 | 检查是否已创建同名资源 |
| `Conflict` | 资源冲突 | 检查资源配置 |

## 错误处理示例

```python
from alibabacloud_tea_util import models as util_models

try:
    response = client.some_api(request)
except util_models.TeaException as e:
    # API 业务错误
    print(f"错误码: {e.code}")
    print(f"错误信息: {e.message}")
    print(f"请求ID: {e.data.get('RequestId')}")

    # 根据错误码处理
    if e.code == 'Throttling':
        # 限流，等待后重试
        time.sleep(60)
    elif e.code == 'Forbidden.RAM':
        # 权限不足
        print("需要授予 AliyunDataWorksFullAccess 权限")
    else:
        # 其他错误
        raise

except Exception as e:
    # 系统错误（网络等）
    print(f"系统错误: {str(e)}")
```

## 重试策略

```python
import time
from functools import wraps

def retry_on_throttling(max_retries=3, wait=60):
    """限流重试装饰器"""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except util_models.TeaException as e:
                    if e.code == 'Throttling' and attempt < max_retries - 1:
                        print(f"触发限流，{wait}秒后重试...")
                        time.sleep(wait)
                        continue
                    raise
        return wrapper
    return decorator

# 使用
@retry_on_throttling(max_retries=3, wait=60)
def call_api():
    return client.some_api(request)
```

## 获取帮助

如果遇到无法解决的错误：

1. 记录 **错误码** 和 **请求ID (RequestId)**
2. 查阅 [官方文档](https://help.aliyun.com/zh/dataworks)
3. 提交工单（附上 RequestId）
