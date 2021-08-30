---
title: 访问内置指标 - Azure IoT Edge
description: 远程访问 IoT Edge 运行时组件中的内置指标
author: v-tcassi
ms.author: v-tcassi
ms.date: 06/25/2021
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4c39b7dfc4097480588620465eedd40eba53f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743323"
---
# <a name="access-built-in-metrics"></a>访问内置指标

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 运行时组件（IoT Edge 中心和 IoT Edge 代理）以 [Prometheus 公开格式](https://prometheus.io/docs/instrumenting/exposition_formats/)生成内置指标。 远程访问这些指标来监视和了解 IoT Edge 设备的运行状况。

你可以使用自己的解决方案来访问这些指标。 也可使用[指标收集器模块](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector)来收集内置指标并将它们发送到 Azure Monitor 或 Azure IoT 中心。 有关详细信息，请参阅[收集和传输指标](how-to-collect-and-transport-metrics.md)。

从版本 1.0.10 开始，默认情况下，指标会自动在 edgeHub 和 edgeAgent 模块（`http://edgeHub:9600/metrics` 和 `http://edgeAgent:9600/metrics`）的端口 9600 公开  。 默认情况下，这些端口不会映射到主机。

通过从模块的 `createOptions` 公开和映射指标端口来访问主机中的指标。 下面的示例将默认指标端口映射到主机上的端口 9601：

```json
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

如果要映射 edgeHub 和 edgeAgent 的指标终结点，请选择不同且唯一的主机端口号。

> [!NOTE]
> 为了使内置指标可用于收集，不应该将环境变量 `httpSettings__enabled` 设置为 `false`。
>
> [azure/iotedge 存储库文档](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)中列出了可用于禁用指标的环境变量。

## <a name="available-metrics"></a>可用度量值

指标包含标志，有助于识别所收集指标的性质。 所有指标都包含以下标志：

| 标记 | 描述 |
|-|-|
| iothub | 设备与之通信的中心 |
| edge_device | 当前设备的 ID |
| instance_number | 表示当前运行时的 GUID。 重启时，将重置所有指标。 此 GUID 有助于协调重启。 |

在 Prometheus 公开格式中，有四个核心指标类型：计数器、仪表、直方图和摘要。 有关不同指标类型的详细信息，请参阅 [Prometheus 指标类型文档](https://prometheus.io/docs/concepts/metric_types/)。

为内置直方图和摘要指标提供的分位数为 0.1、0.5、0.9 和 0.99。

edgeHub 模块生成以下指标：

| “属性” | 维度 | 说明 |
|-|-|-|
| `edgehub_gettwin_total` | `source`（操作源）<br> `id`（模块 ID） | 类型：计数器<br> GetTwin 调用总数 |
| `edgehub_messages_received_total` | `route_output`（发送消息的输出）<br> `id` | 类型：计数器<br> 从客户端接收的消息总数 |
| `edgehub_messages_sent_total` | `from`（消息源）<br> `to`（消息目标）<br>`from_route_output`<br> `to_route_input`（消息目标输入）<br> `priority`（到目标的消息优先级） | 类型：计数器<br> 发送到客户端或上游的消息总数<br> 当 `to` 为 $upstream 时，`to_route_input` 为空 |
| `edgehub_reported_properties_total` | `target`（更新目标）<br> `id` | 类型：计数器<br> 报告的属性更新调用总数 |
| `edgehub_message_size_bytes` | `id`<br> | 类型：摘要<br> 来自客户端的消息大小<br> 如果在某段时间内（当前为 10 分钟）未报告新的度量，则可能会将值报告为 `NaN`；对于 `summary` 类型，将发出相应的 `_count` 和 `_sum` 计数器。 |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 类型：摘要<br> 获取孪生操作所用的时间 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 类型：摘要<br> 发送消息所用的时间 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 类型：摘要<br> 处理来自队列的消息所用的时间 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 类型：摘要<br> 更新报告的属性所用的时间 |
| `edgehub_direct_method_duration_seconds` | `from`（调用方）<br> `to`（接收方） | 类型：摘要<br> 解析直接消息所用的时间 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 类型：计数器<br> 发送的直接消息总数 |
| `edgehub_queue_length` | `endpoint`（消息源）<br> `priority`（队列优先级） | 类型：仪表<br> 给定优先级的 edgeHub 队列的当前长度 |
| `edgehub_messages_dropped_total` | `reason`（no_route、ttl_expiry）<br> `from` <br> `from_route_output` | 类型：计数器<br> 因某种原因而删除的消息总数 |
| `edgehub_messages_unack_total` | `reason` (storage_failure)<br> `from`<br> `from_route_output` | 类型：计数器<br> 由于存储故障而未确认的消息总数 |
| `edgehub_offline_count_total` | `id` | 类型：计数器<br> edgeHub 脱机的总次数 |
| `edgehub_offline_duration_seconds`| `id` | 类型：摘要<br> Edge 中心脱机的时间 |
| `edgehub_operation_retry_total` | `id`<br> `operation`（操作名称） | 类型：计数器<br> 重试 edgeHub 操作的总次数 |
| `edgehub_client_connect_failed_total` | `id` <br> `reason`（未通过身份验证）<br> | 类型：计数器<br> 客户端无法连接到 edgeHub 的总次数 |

edgeAgent 模块生成以下指标：

| “属性” | 维度 | 说明 |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 类型：仪表<br> 在部署中指定模块并且处于运行状态的时间 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 类型：仪表<br> 在部署中指定模块的时间 |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 类型：计数器<br> edgeAgent 请求 Docker 启动模块的次数 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 类型：计数器<br> edgeAgent 请求 Docker 停止模块的次数 |
| `edgeAgent_command_latency_seconds` | `command` | 类型：仪表<br> Docker 执行给定命令所用的时间。 可能的命令有：create、update、remove、start、stop、restart |
| `edgeAgent_iothub_syncs_total` | | 类型：计数器<br> edgeAgent 尝试将其孪生与 iotHub 同步的成功和失败次数。 此数目包括请求孪生的代理和通知孪生更新的中心 |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 类型：计数器<br> edgeAgent 无法与 iotHub 同步其孪生的次数。 |
| `edgeAgent_deployment_time_seconds` | | 类型：计数器<br> 接收更改后完成新部署所用的时间。 |
| `edgeagent_direct_method_invocations_count` | `method_name` | 类型：计数器<br> 调用内置 edgeAgent 直接方法（如 Ping 或 Restart）的次数。 |
| `edgeAgent_host_uptime_seconds` | | 类型：仪表<br> 主机已开启的时间 |
| `edgeAgent_iotedged_uptime_seconds` | | 类型：仪表<br> iotedged 已运行的时间 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 类型：仪表<br> 磁盘上的剩余空间量 |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 类型：仪表<br> 磁盘的大小 |
| `edgeAgent_used_memory_bytes` | `module_name` | 类型：仪表<br> 所有进程使用的 RAM 量 |
| `edgeAgent_total_memory_bytes` | `module_name` | 类型：仪表<br> 可用 RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 类型：直方图<br> 所有进程使用的 CPU 百分比 |
| `edgeAgent_created_pids_total` | `module_name` | 类型：仪表<br> 容器创建的进程或线程数 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 类型：仪表<br> 从网络接收的字节数 |
| `edgeAgent_total_network_out_bytes` | `module_name` | 类型：仪表<br> 发送到网络的字节数 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 类型：仪表<br> 从磁盘读取的字节数 |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 类型：仪表<br> 写入磁盘的字节数 |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 类型：仪表<br> 有关设备的常规元数据。 此值始终为 0，将在标志中对信息进行编码。 请注意，`experimental_features` 和 `host_information` 是 JSON 对象。 `host_information` 类似于 ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}```。 请注意，`ServerVersion` 是 Docker 版本，`Version` 是 IoT Edge 安全守护程序版本。 |

## <a name="next-steps"></a>后续步骤

* [收集和传输指标](how-to-collect-and-transport-metrics.md)
* [了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)
* [IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)
