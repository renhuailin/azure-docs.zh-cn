---
title: 使用 REST API 来管理 Azure IoT Central 中的作业
description: 如何使用 IoT Central REST API 来创建和管理应用程序中的作业
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 47548b3c7e1fa7503e3f30c155755d6fe4f29031
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667039"
---
# <a name="how-to-use-the-iot-central-rest-api-to-create-and-manage-jobs"></a>如何使用 IoT Central REST API 来创建和管理作业

通过 IoT Central REST API，你可以开发与 IoT Central 应用程序集成的客户端应用程序。 你可以使用 REST API 来创建和管理 IoT Central 应用程序中的作业。 你可以使用 REST API 达到以下目的：

- 列出应用程序中的作业并查看作业的详细信息。
- 在应用程序中创建作业。
- 停止、恢复和重新运行应用程序中的作业。

> [!IMPORTANT]
> 该作业 API 目前处于预览阶段。 本文描述的所有 REST API 调用都应包括 `?api-version=preview`。

本文介绍如何使用 `/jobs/{job_id}` API 批量控制设备。 还可以单独控制设备。

每个 IoT Central REST API 调用都需要授权标头。 有关详细信息，请参阅[如何对 IoT Central REST API 调用进行身份验证和授权](howto-authorize-rest-api.md)。

有关 IoT Central REST API 的参考文档，请参阅 [Azure IoT Central REST API 参考](/rest/api/iotcentral/)。

> [!TIP]
> [预览 API](/rest/api/iotcentral/1.1-previewdataplane/jobs) 支持新的[组织功能](howto-create-organizations.md)。

若要了解如何在 UI 中创建和管理作业，请参阅[在 Azure IoT Central 应用程序中批量管理设备](howto-manage-devices-in-bulk.md)。

## <a name="job-payloads"></a>作业有效负载

本文中所述的许多 API 包括类似于以下 JSON 代码片段的定义：

```json
{
  "id": "job-014",
  "displayName": "Set target temperature",
  "description": "Set target temperature for all thermostat devices",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "56"
    }
  ],
  "status": "complete"
}
```

下表描述了上一个 JSON 代码片段中的字段：

| 字段 | 说明 |
| ----- | ----------- |
| `id` | 应用程序中作业的唯一 ID。 |
| `displayName` | 应用程序中作业的显示名称。 |
| `description` | 作业的说明。 |
| `group` | 应用作业的设备组的 ID。 使用 `deviceGroups` 预览 REST API 获取应用程序中设备组的列表。 |
| `status` | 作业的[状态](howto-manage-devices-in-bulk.md#view-job-status)。 其中之一：`complete`、`cancelled`、`failed`、`pending`、`running`、`stopped`。 |
| `batch` | 如果存在，此部分定义如何[批处理](howto-manage-devices-in-bulk.md#create-and-run-a-job)作业中的设备。 |
| `batch/type` | 每批的大小是组中设备总数的 `percentage` 或设备的 `number`。 |
| `batch/value` | 每个批次中设备的百分比或设备数。 |
| `cancellationThreshold` | 如果存在，此部分定义作业的[取消阈值](howto-manage-devices-in-bulk.md#create-and-run-a-job)。 |
| `cancellationThreshold/batch` | `true` 或 `false`。 如果为 true，则设置每个批次的取消阈值。 如果为 `false`，则取消阈值应用于整个作业。 |
| `cancellationThreshold/type` | 作业的取消阈值是设备的 `percentage` 或 `number`。 |
| `cancellationThreshold/value` | 定义取消阈值的设备百分比或设备数。 |
| `data` | 作业执行的操作数组。 |
| `data/type` | `PropertyJobData`、`CommandJobData`或 `CloudPropertyJobData` 之一 |
| `data/target` | 目标设备的模型 ID。 |
| `data/path` | 属性、命令或云属性的名称。 |
| `data/value` | 要设置的属性值或要发送的命令参数。 |

## <a name="get-job-information"></a>获取作业信息

使用以下请求检索应用程序中作业的列表：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs?api-version=preview
```

对此请求的响应如下方示例所示：

```json
{
  "value": [
    {
      "id": "job-006",
      "displayName": "Set customer name",
      "description": "Set the customer name cloud property",
      "group": "4fcbec3b-5ee8-4324-855d-0f03b56bcf7f",
      "data": [
        {
          "type": "CloudPropertyJobData",
          "target": "dtmi:modelDefinition:bojo9tfju:yfvu5gv2vl",
          "path": "CustomerName",
          "value": "Contoso"
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-005",
      "displayName": "Set target temperature",
      "description": "Set target temperature device property",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "data": [
        {
          "type": "PropertyJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "targetTemperature",
          "value": 56
        }
      ],
      "status": "complete"
    },
    {
      "id": "job-004",
      "displayName": "Run device report",
      "description": "Call command to run the device reports",
      "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
      "batch": {
        "type": "percentage",
        "value": 25
      },
      "cancellationThreshold": {
        "type": "percentage",
        "value": 10,
        "batch": false
      },
      "data": [
        {
          "type": "CommandJobData",
          "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
          "path": "getMaxMinReport",
          "value": "2021-06-15T05:00:00.000Z"
        }
      ],
      "status": "complete"
    }
  ]
}
```

使用以下请求按 ID 检索单个作业：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004?api-version=preview
```

对此请求的响应如下方示例所示：

```json
{
  "id": "job-004",
  "displayName": "Run device report",
  "description": "Call command to run the device reports",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "CommandJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "getMaxMinReport",
      "value": "2021-06-15T05:00:00.000Z"
    }
  ],
  "status": "complete"
}
```

使用以下请求检索作业中设备的详细信息：

```http
GET https://{your app subdomain}.azureiotcentral.com/api/jobs/job-004/devices?api-version=preview
```

对此请求的响应如下方示例所示：

```json
{
  "value": [
    {
      "id": "therm-01",
      "status": "completed"
    },
    {
      "id": "therm-02",
      "status": "completed"
    },
    {
      "id": "therm-03",
      "status": "completed"
    },
    {
      "id": "therm-04",
      "status": "completed"
    }
  ]
}
```

## <a name="create-a-job"></a>创建作业

使用以下请求检索作业中设备的详细信息：

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006?api-version=preview
{
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ]
}
```

对此请求的响应如下方示例所示。 初始作业状态为 `pending`：

```json
{
  "id": "job-006",
  "displayName": "Set target temperature",
  "description": "Set target temperature device property",
  "group": "833d7a7d-8f99-4e04-9e56-745806bdba6e",
  "batch": {
    "type": "percentage",
    "value": 25
  },
  "cancellationThreshold": {
    "type": "percentage",
    "value": 10,
    "batch": false
  },
  "data": [
    {
      "type": "PropertyJobData",
      "target": "dtmi:modelDefinition:zomtmdxh:eqod32zbyl",
      "path": "targetTemperature",
      "value": "55"
    }
  ],
  "status": "pending"
}
```

## <a name="stop-resume-and-rerun-jobs"></a>停止、恢复和重新运行作业

使用以下请求停止正在运行的作业：

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/stop?api-version=preview
```

如果请求成功，则返回 `204 - No Content` 响应。

使用以下请求恢复停止的作业：

```http
POST https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/resume?api-version=preview
```

如果请求成功，则返回 `204 - No Content` 响应。

使用以下命令在任何故障设备上重新运行现有作业：

```http
PUT https://{your app subdomain}.azureiotcentral.com/api/jobs/job-006/rerun/rerun-001?api-version=preview
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 REST API 管理作业，建议接下来学习如何[使用 REST API 管理 IoT Central 应用程序](/learn/modules/manage-iot-central-apps-with-rest-api/)。
