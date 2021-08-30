---
title: 在 Azure IoT Central 中配置规则和操作 | Microsoft Docs
description: 本操作指南文章演示，作为构建者，如何在 Azure IoT Central 应用程序中配置基于遥测的规则和操作。
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 5d36c303f1ebc23b317ccd5dcb08d65f5ceaf30b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746419"
---
# <a name="configure-rules"></a>配置规则

IoT Central 中的规则充当可自定义的响应工具，针对已连接设备的主动监视事件进行触发。 以下部分介绍如何评估规则。 可以定义在触发规则时要发生的一个或多个操作，本文介绍电子邮件、Webhook 和 Azure Monitor 操作组。 要了解其他操作类型，请参阅[使用工作流将 Azure IoT Central 应用程序与其他云服务集成](howto-configure-rules-advanced.md)。

## <a name="select-target-devices"></a>选择目标设备

使用目标设备部分选择将此规则应用于的设备类型。 筛选器可以进一步优化应该包含的设备。 筛选器使用设备模板上的属性来筛选一组设备。 筛选器本身不触发操作。 在以下屏幕截图中，目标设备的设备模板类型是冰箱。   筛选器表明规则应只包含冰箱，其中“产地”属性等于“华盛顿”。

:::image type="content" source="media/howto-configure-rules/filters.png" alt-text="显示条件定义的屏幕截图。":::

## <a name="use-multiple-conditions"></a>使用多个条件

条件是规则触发操作所根据的内容。 可以向规则添加多个条件，并指定是在所有条件都为 true 还是任一条件为 true 时触发该规则。  

在下面的屏幕截图中，条件检查何时温度大于 70&deg; F 且湿度小于 10。 当这些语句中的任何一个为 true 时，规则的结果为 true 并触发操作。

:::image type="content" source="media/howto-configure-rules/conditions.png" alt-text="屏幕截图显示了具有指定温度和湿度条件的冰箱监视器。":::

> [!NOTE]
> 目前仅支持遥测条件。  

### <a name="use-a-cloud-property-in-a-value-field"></a>在值字段中使用云属性

可以在“值”字段中为条件引用来自设备模板的云属性。 云属性和遥测值必须具有相似的类型。  例如，如果温度是一个 double 值，则只有 double 类型的云属性显示在“值”下拉列表的选项中。

 如果选择事件类型的遥测值，则“值”下拉列表包含“任一”选项。 “任一”选项意味着当应用程序收到该类型的事件时将触发规则（无论有效负载是什么）。

## <a name="use-aggregate-windowing"></a>使用聚合窗口

可以指定时间聚合以根据时间窗口触发规则。 规则条件将遥测数据上的聚合时间窗口评估为翻转窗口。 如果规则中有任何属性筛选器，它们将在时间窗口结束时应用。 在下面的屏幕截图中，时间窗口为 5 分钟。 每隔五分钟，规则将评估过去五分钟的遥测数据。 只有当数据位于其对应的窗口时才评估它。

:::image type="content" source="media/howto-configure-rules/tumbling-window.png" alt-text="显示如何定义翻转窗口的关系图。":::

## <a name="create-an-email-action"></a>创建电子邮件操作

创建电子邮件操作时，电子邮件地址必须是应用程序中的用户 ID，并且该用户必须至少登录过该应用程序一次。 也可指定要包含在电子邮件中的注释。 IoT Central 显示规则触发时电子邮件外观的示例：

:::image type="content" source="media/howto-configure-rules/email-action.png" alt-text="显示规则的电子邮件操作的屏幕截图。":::

## <a name="create-a-webhook-action"></a>创建 Webhook 操作

Webhook 支持将 IoT Central 应用连接到其他应用程序和服务，以便进行远程监视和通知。 只要在 IoT Central 应用中触发了规则，Webhook 就会自动通知你连接的其他应用程序和服务。 每当触发规则时，IoT Central 应用就会向其他应用程序的 HTTP 终结点发送 POST 请求。 有效负载包含设备详细信息和规则触发详细信息。

在此示例中，请连接到 RequestBin，以便在规则触发时收到通知：

1. 打开 [RequestBin](https://requestbin.net/)。

1. 创建一个新的 RequestBin 并复制 **Bin URL**。

1. 向规则添加操作：

    :::image type="content" source="media/howto-configure-rules/webhook-create.png" alt-text="显示 Webhook 创建屏幕的屏幕截图。":::

1. 选择 Webhook 操作，输入显示名称，然后将 RequestBin URL 粘贴为回叫 URL。

1. 保存规则。

现在，当规则触发时，你会看到 RequestBin 中出现一个新请求。

### <a name="payload"></a>有效负载

当规则触发时，它会向回叫 URL 发出 HTTP POST 请求。 该请求包含带有遥测、设备、规则和应用程序详细信息的 JSON 负载。 有效负载类似于以下 JSON 代码片段：

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```

如果该规则监视一段时间内的聚合遥测，则有效负载包含一个类似于以下内容的遥测部分：

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

### <a name="data-format-change-notice"></a>数据格式更改通知

如果在 2020 年 4 月 3 日之前创建并保存了一个或多个 Webhook，请将其删除并创建新的 Webhook。 旧版 Webhook 使用已弃用的有效负载格式：

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="create-an-azure-monitor-group-action"></a>创建 Azure Monitor 组操作

本部分介绍如何使用 [Azure Monitor](../../azure-monitor/overview.md) 操作组将多个操作附加到 IoT Central 规则。 可将一个操作组附加到多个规则。 [操作组](../../azure-monitor/alerts/action-groups.md)是由 Azure 订阅的所有者定义的通知首选项的集合。

可以 [在 Azure 门户中创建和管理操作组](../../azure-monitor/alerts/action-groups.md)，也可以使用 [Azure 资源管理器模板](../../azure-monitor/alerts/action-groups-create-resource-manager-template.md)执行这些操作。

操作组可以：

- 发送电子邮件、短信等通知，或拨打语音电话。
- 运行某个操作，例如调用 Webhook。

以下屏幕截图显示了一个可以发送电子邮件和短信通知以及调用 Webhook 的操作组：

:::image type="content" source="media/howto-configure-rules/action-group.png" alt-text="显示 Azure 门户中操作组的屏幕截图。":::

若要在 IoT Central 规则中使用某个操作组，该操作组必须与 IoT Central 应用程序位于同一 Azure 订阅中。

在 IoT Central 中向规则添加操作时，选择“Azure Monitor 操作组”。

从 Azure 订阅中选择操作组：

:::image type="content" source="media/howto-configure-rules/choose-action-group.png" alt-text="显示 IoT Central 规则中的操作组的屏幕截图。":::

选择“保存”。 该操作组现在显示在触发规则后要运行的操作的列表中。

下表汇总了发送到受支持操作类型的信息：

| 操作类型 | 输出格式 |
| ----------- | -------------- |
| 电子邮件       | 标准 IoT Central 电子邮件模板 |
| SMS         | Azure IoT Central alert: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} |
| 语音       | Azure I.O.T Central alert: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[常规 Webhook 有效负载](howto-create-webhooks.md#payload)}} |

以下文本是来自操作组的示例短信：

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="use-rules-with-iot-edge-modules"></a>将规则用于 IoT Edge 模块

应用于 IoT Edge 模块的规则有一个限制。 不同模块的遥测规则不会评估为有效规则。 以下面的例子为例，规则的第一个条件是关于模块 A 中的温度遥测。规则的第二个条件是关于模块 B 中的湿度遥测。由于这两个条件来自不同的模块，因此这一组条件无效。 规则无效并在尝试保存规则时引发错误。

## <a name="next-steps"></a>后续步骤

现在已了解如何在 Azure IoT Central 应用程序中配置规则，接下来将了解如何使用 Power Automate 或 Azure 逻辑应用[配置高级规则](howto-configure-rules-advanced.md)。
