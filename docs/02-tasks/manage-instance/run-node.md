---
title: 运行节点
id: task-run-node
category: 实例管理
difficulty: 初级
time: 5分钟
prerequisites:
  - 节点已发布到生产环境
apis:
  - RunNode
  - GetInstance
related:
  - 查询实例状态
  - 终止实例运行
tags: [instance, run, execute]
---

# 运行节点

本教程演示如何手动运行已发布的节点。

## 场景说明

| 项目 | 说明 |
|------|------|
| **操作** | 手动触发节点运行 |
| **目的** | 测试、补数据、临时执行 |
| **参数** | 业务日期 (bizdate) |

## 步骤一：运行节点

```python
from alibabacloud_dataworks_public20240518 import models as dataworks_models

def run_node(workspace_id, file_id, bizdate):
    """运行节点"""
    request = dataworks_models.RunNodeRequest(
        project_identifier=workspace_id,
        file_id=file_id,
        biz_date=bizdate  # 格式：YYYYMMDD
    )
    response = client.run_node(request)
    return response.body.data.instance_id

# 使用
instance_id = run_node(
    workspace_id='your_workspace_id',
    file_id='your_file_id',
    bizdate='20250101'  # 业务日期：2025年1月1日
)

print(f"任务已提交，实例ID: {instance_id}")
```

## 步骤二：查询运行状态

```python
def get_status(workspace_id, instance_id):
    """查询实例状态"""
    request = dataworks_models.GetInstanceRequest(
        project_identifier=workspace_id,
        instance_id=instance_id
    )
    response = client.get_instance(request)
    return response.body.data

# 使用
instance = get_status('your_workspace_id', instance_id)

print(f"状态: {instance.status}")
print(f"开始时间: {instance.start_time}")
print(f"结束时间: {instance.end_time}")

# 状态值：
# WAIT_WAITING - 等待调度时间
# WAIT_RUNNING - 等待依赖完成
# RUNNING - 正在运行
# SUCCESS - 运行成功
# FAILED - 运行失败
```

## 步骤三：等待运行完成

```python
import time

def wait_for_complete(workspace_id, instance_id, timeout=3600, interval=30):
    """等待任务完成"""
    start_time = time.time()

    while time.time() - start_time < timeout:
        instance = get_status(workspace_id, instance_id)

        if instance.status == 'SUCCESS':
            print("✓ 任务执行成功！")
            return True
        elif instance.status == 'FAILED':
            print(f"✗ 任务执行失败")
            print(f"  错误信息: {instance.dag_error_message}")
            return False

        print(f"  运行中... ({time.time() - start_time:.0f}s)")
        time.sleep(interval)

    print("✗ 任务超时")
    return False

# 使用
wait_for_complete('your_workspace_id', instance_id, timeout=1800)
```

## 步骤四：批量运行

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def run_nodes_batch(workspace_id, file_ids, bizdate, max_workers=5):
    """批量运行节点"""
    results = {}

    with ThreadPoolExecutor(max_workers=max_workers) as executor:
        futures = {}
        for file_id in file_ids:
            future = executor.submit(run_node, workspace_id, file_id, bizdate)
            futures[future] = file_id

        for future in as_completed(futures):
            file_id = futures[future]
            try:
                instance_id = future.result()
                results[file_id] = {'status': 'submitted', 'instance_id': instance_id}
                print(f"✓ {file_id} 已提交")
            except Exception as e:
                results[file_id] = {'status': 'failed', 'error': str(e)}
                print(f"✗ {file_id} 失败: {e}")

    return results

# 使用
file_ids = ['file_id_1', 'file_id_2', 'file_id_3']
results = run_nodes_batch('your_workspace_id', file_ids, '20250101')
```

## 业务日期说明

`bizdate` 参数表示数据的业务日期，与实际运行日期不同：

| 场景 | 运行时间 | bizdate | 说明 |
|------|----------|---------|------|
| T+1 日处理 | 2025-01-02 02:00 | 20250101 | 处理 1月1日的数据 |
| 当日处理 | 2025-01-01 23:59 | 20250101 | 处理当天的数据 |
| 补历史数据 | 2025-01-15 10:00 | 20241201 | 补 12月1日的数据 |

## 完整工具类

```python
import time
from typing import Dict, List
from alibabacloud_dataworks_public20240518.client import Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_dataworks_public20240518 import models as dataworks_models

class InstanceRunner:
    """实例运行管理"""

    def __init__(self, access_key_id: str, access_key_secret: str, region: str = 'cn-hangzhou'):
        config = open_api_models.Config(
            access_key_id=access_key_id,
            access_key_secret=access_key_secret,
            endpoint=f'dataworks.{region}.aliyuncs.com'
        )
        self.client = Client(config)

    def run(self, workspace_id: str, file_id: str, bizdate: str) -> str:
        """运行节点，返回实例ID"""
        request = dataworks_models.RunNodeRequest(
            project_identifier=workspace_id,
            file_id=file_id,
            biz_date=bizdate
        )
        response = self.client.run_node(request)
        return response.body.data.instance_id

    def get_status(self, workspace_id: str, instance_id: str) -> dict:
        """获取实例状态"""
        request = dataworks_models.GetInstanceRequest(
            project_identifier=workspace_id,
            instance_id=instance_id
        )
        data = self.client.get_instance(request).body.data
        return {
            'status': data.status,
            'start_time': data.start_time,
            'end_time': data.end_time,
            'error_message': data.dag_error_message
        }

    def wait(self, workspace_id: str, instance_id: str,
             timeout: int = 3600, interval: int = 30) -> bool:
        """等待实例完成"""
        start_time = time.time()

        while time.time() - start_time < timeout:
            status_info = self.get_status(workspace_id, instance_id)
            status = status_info['status']

            if status == 'SUCCESS':
                return True
            elif status == 'FAILED':
                raise Exception(f"任务失败: {status_info['error_message']}")

            time.sleep(interval)

        raise TimeoutError(f"任务超时（{timeout}秒）")

    def run_and_wait(self, workspace_id: str, file_id: str,
                     bizdate: str, timeout: int = 3600) -> bool:
        """运行节点并等待完成"""
        instance_id = self.run(workspace_id, file_id, bizdate)
        print(f"实例已提交: {instance_id}")
        return self.wait(workspace_id, instance_id, timeout)

# 使用示例
runner = InstanceRunner(
    access_key_id='YOUR_ACCESS_KEY_ID',
    access_key_secret='YOUR_ACCESS_KEY_SECRET'
)

# 运行并等待
success = runner.run_and_wait(
    workspace_id='your_workspace_id',
    file_id='your_file_id',
    bizdate='20250101'
)

if success:
    print("任务执行成功！")
```

## 相关教程

- [GetInstance API](../../03-api/instance/GetInstance.md) - 查询实例状态
- [KillInstance API](../../03-api/instance/KillInstance.md) - 终止实例运行
