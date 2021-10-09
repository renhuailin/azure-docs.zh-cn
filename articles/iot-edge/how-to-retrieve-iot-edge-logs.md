---
title: 检索 IoT Edge 日志 - Azure IoT Edge
description: IoT Edge 模块日志检索以及上传到 Azure Blob 存储。
author: v-tcassi
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 75b89acff320b3083c098a0546882d80bdcfc5ad
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060889"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>检索 IoT Edge 部署中的日志

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

检索 IoT Edge 部署中的日志，无需使用 IoT Edge 代理模块中包含的直接方法对设备进行物理或 SSH 访问。 直接方法在设备上实现，然后可从云中调用。 IoT Edge 代理包含一些可以帮助你远程监视和管理 IoT Edge 设备的直接方法。 本文中所述的直接方法在 1.0.10 版本中正式发布。

有关直接方法及其用法，以及如何在自己的模块中实现这些方法的详细信息，请参阅[了解直接方法以及从 IoT 中心调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

处理这些直接方法时，其名称区分大小写。

## <a name="recommended-logging-format"></a>推荐的日志记录格式

虽然不是必需的，但为了与此功能实现最佳兼容性，推荐的日志记录格式为：

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` 应设置为 `yyyy-MM-dd HH:mm:ss.fff zzz`，并且 `{Log Level}` 应遵循下表，此表从 [Syslog 标准的严重性代码](https://wikipedia.org/wiki/Syslog#Severity_level)中派生其严重性级别。

| 值 | Severity |
|-|-|
| 0 | 紧急情况 |
| 1 | 警报 |
| 2 | 严重 |
| 3 | 错误 |
| 4 | 警告 |
| 5 | 通知 |
| 6 | 信息 |
| 7 | 调试 |

[IoT Edge 中的记录器类](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs)充当规范实现。

## <a name="retrieve-module-logs"></a>检索模块日志

使用 GetModuleLogs 直接方法检索 IoT Edge 模块的日志。

>[!TIP]
>Azure 门户中的 IoT Edge 故障排除页提供了查看模块日志的简化体验。 有关详细信息，请参阅[在 Azure 门户中对 IoT Edge 设备进行监视和故障排除](troubleshoot-in-portal.md)。

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名称 | 类型 | 描述 |
|-|-|-|
| schemaVersion | 字符串 | 设置为 `1.0` |
| items | JSON 数组 | 具有 `id` 和 `filter` 元组的数组。 |
| ID | 字符串 | 提供模块名称的正则表达式。 它可以匹配边缘设备上的多个模块。 应为 [.NET 正则表达式](/dotnet/standard/base-types/regular-expressions)格式。 |
| filter | JSON 部分 | 要应用于与元组中 `id` 正则表达式匹配的模块的日志筛选器。 |
| tail | integer | 从最新日志行开始检索的过去的日志行数。 可选。 |
| since | string | 只返回此时间之后的日志，作为持续时间（1 天、90 分钟、2 天 3 小时 2 分钟）、rfc3339 时间戳或 UNIX 时间戳。  如果同时指定 `tail` 和 `since`，将首先使用 `since` 值检索日志。 然后，将 `tail` 值应用于结果，并返回最终结果。 可选。 |
| until | string | 仅返回指定时间之前的日志，作为 rfc3339 时间戳、UNIX 时间戳或持续时间（1 天、90 分钟、2 天 3 小时 2 分钟）。 可选。 |
| 日志级别 | integer | 筛选小于或等于指定日志级别的日志行。 日志行应遵循推荐的日志记录格式，并采用 [Syslog 严重性级别](https://en.wikipedia.org/wiki/Syslog#Severity_level)标准。 可选。 |
| regex | 字符串 | 使用 [.NET 正则表达式](/dotnet/standard/base-types/regular-expressions)格式，筛选内容与指定正则表达式匹配的日志行。 可选。 |
| encoding | 字符串 | `gzip` 或 `none`。 默认为 `none`。 |
| contentType | 字符串 | `json` 或 `text`。 默认为 `text`。 |

> [!NOTE]
> 如果日志内容超出了直接方法的响应大小限制（目前为 128 KB），响应会返回错误。

日志检索成功后，会返回“状态: 200”，后跟一个有效负载，其中包含从模块中检索到的日志，并按你在请求中指定的设置进行筛选。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

在 Azure 门户中，调用名为 `GetModuleLogs`、包含以下 JSON 有效负载的方法：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 门户中调用直接方法“GetModuleLogs”](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

你还可以将 CLI 输出传输到 Linux 实用程序（如 [gzip](https://en.wikipedia.org/wiki/Gzip)），以处理压缩的响应。 例如：

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>上传模块日志

使用 UploadModuleLogs 直接方法将请求的日志发送到指定的 Azure Blob 存储容器。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 若要从网关设备后的设备上传日志，需要在顶层设备上配置 [API 代理和 blob 存储模块](how-to-configure-api-proxy-module.md)。 这些模块通过网关设备将日志从较低层设备路由到云中的存储。

::: moniker-end

此方法接受类似于 GetModuleLogs 的 JSON 有效负载，并添加“sasUrl”键：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名称 | 类型 | 描述 |
|-|-|-|
| sasURL | 字符串 (URI) | [对 Azure Blob 存储容器具有写入权限的共享访问签名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)。 |

上传日志请求成功后，会返回“状态: 200”，后跟具有以下架构的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| 状态 | 字符串 | `NotStarted`、`Running`、`Completed`、`Failed` 或 `Unknown` 中的一个。 |
| message | 字符串 | 如果错误，则为消息；否则为空字符串。 |
| correlationId | 字符串   | 用于查询上传请求状态的 ID。 |

例如：

以下调用将以压缩的 JSON 格式从所有模块上传最后 100 个日志行：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

以下调用以未压缩的文本格式从 edgeAgent 和 edgeHub 上传最后 100 个日志行，从 tempSensor 模块上传最后 1000 个日志行：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

在 Azure 门户中，用你的信息填充 sasURL 后，调用名为 `UploadModuleLogs` 且包含以下 JSON 有效负载的方法：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 门户中调用直接方法“UploadModuleLogs”](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>上传支持捆绑诊断

使用 UploadSupportBundle 直接方法将 IoT Edge 模块日志的 zip 文件捆绑并上传到可用的 Azure Blob 存储容器。 此直接方法在 IoT Edge 设备上运行 [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) 命令以获取日志。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 若要从网关设备后的设备上传日志，需要在顶层设备上配置 [API 代理和 blob 存储模块](how-to-configure-api-proxy-module.md)。 这些模块通过网关设备将日志从较低层设备路由到云中的存储。

::: moniker-end

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| 名称 | 类型 | 描述 |
|-|-|-|
| schemaVersion | 字符串 | 设置为 `1.0` |
| sasURL | 字符串 (URI) | [对 Azure Blob 存储容器具有写入权限的共享访问签名 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | string | 只返回此时间之后的日志，作为持续时间（1 天、90 分钟、2 天 3 小时 2 分钟）、rfc3339 时间戳或 UNIX 时间戳。 可选。 |
| until | string | 仅返回指定时间之前的日志，作为 rfc3339 时间戳、UNIX 时间戳或持续时间（1 天、90 分钟、2 天 3 小时 2 分钟）。 可选。 |
| edgeRuntimeOnly | boolean | 如果为 true，则仅从 Edge 代理、Edge 中心和 Edge 安全守护程序返回日志。 默认值：false。  可选。 |

> [!IMPORTANT]
> IoT Edge 支持捆绑可能包含个人身份信息。

上传日志请求成功后，会返回“状态: 200”，后跟架构与 UploadModuleLogs 响应相同的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| 状态 | 字符串 | `NotStarted`、`Running`、`Completed`、`Failed` 或 `Unknown` 中的一个。 |
| message | 字符串 | 如果错误，则为消息；否则为空字符串。 |
| correlationId | 字符串   | 用于查询上传请求状态的 ID。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

在 Azure 门户中，用你的信息填充 sasURL 后，调用名为 `UploadSupportBundle` 且包含以下 JSON 有效负载的方法：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![在 Azure 门户中调用直接方法“UploadSupportBundle”](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>获取上传请求状态

使用 GetTaskStatus 直接方法查询上传日志请求的状态。 GetTaskStatus 请求有效负载使用上传日志请求的 `correlationId` 获取任务的状态。 返回 `correlationId`，以响应 UploadModuleLogs 直接方法调用。

此方法接受具有以下架构的 JSON 有效负载：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

上传日志请求成功后，会返回“状态: 200”，后跟架构与 UploadModuleLogs 响应相同的有效负载：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名称 | 类型 | 说明 |
|-|-|-|
| 状态 | string | `NotStarted`、`Running`、`Completed`、`Failed`、“Cancelled”或 `Unknown` 之一。 |
| message | 字符串 | 如果错误，则为消息；否则为空字符串。 |
| correlationId | 字符串   | 用于查询上传请求状态的 ID。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

在 Azure 门户中，用你的信息填充 GUID 后，调用名为 `GetTaskStatus` 且包含以下 JSON 有效负载的方法：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![在 Azure 门户中调用直接方法“GetTaskStatus”](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>后续步骤

[IoT Edge 代理和 IoT Edge 中心模块孪生的属性](module-edgeagent-edgehub.md)
