---
title: 通过 Azure IoT 集线器接收设备数据-Azure 医疗保健 api
description: 在本教程中，你将了解如何通过 IoT 连接器使设备数据从 IoT 中心路由到 FHIR 服务。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jasteppe
ms.openlocfilehash: fe478235897b855d079241192108f5d5ebd70fe0
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992972"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>教程：通过 Azure IoT 中心接收设备数据

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

IoT 连接器使你能够从医疗物 Internet (IoMT) 设备引入快速医疗保健互操作性资源 (FHIR&#174;) 服务。 IoT 连接器还可用于通过 Azure IoT 中心预配和管理的设备。 本教程提供连接 Azure IoT 中心的设备数据并将其路由到 IoT 连接器的过程。

## <a name="prerequisites"></a>先决条件

- 有效的 Azure 订阅 - [免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 包含至少一个 IoT 连接器的 FHIR 服务资源- [使用 Azure 门户部署 iot 连接器](deploy-iot-connector-in-azure.md)
- 与真实或模拟设备连接的 Azure IoT 中心资源 - [使用 Azure 门户创建 IoT 中心](../../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)

> [!TIP]
> 如果你使用的是 Azure IoT 中心模拟设备应用程序，则可以从采用各种不同支持语言和系统的应用程序中进行选择。

## <a name="get-connection-string-for-iot-connector"></a>获取 IoT 连接器的连接字符串

Azure IoT集线器需要连接字符串才能安全地连接到 IoT 连接器。 如 [生成连接字符串](../azure-api-for-fhir/iot-fhir-portal-quickstart.md#generate-a-connection-string)中所述，为 IoT 连接器创建新的连接字符串。 保险箱要在下一步中使用的此连接字符串。

IoT 连接器在幕后使用 Azure 事件中心实例来接收设备消息。 上面创建的连接字符串基本上就是此基础事件中心的连接字符串。

## <a name="connect-azure-iot-hub-with-iot-connector"></a>通过 IoT 连接器连接 Azure IoT 中心

Azure IoT 中心支持[消息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md)功能，该功能可将设备数据发送到各种 Azure 服务（例如事件中心、存储帐户和服务总线）。 IoT 连接器使用此功能连接 Azure IoT 集线器中的设备数据并将其发送到事件中心终结点。

> [!NOTE] 
> 目前只能使用 PowerShell 或 CLI 命令 [创建消息路由](../../iot-hub/tutorial-routing.md) ，因为 IoT 连接器的事件中心不是在客户订阅上托管的，因此不会通过 Azure 门户查看。 但是，在使用 PowerShell 或 CLI 添加消息路由对象之后，这些对象将在 Azure 门户上可见，并可以从其中进行管理。

消息路由的设置包括两个步骤。

### <a name="add-an-endpoint"></a>添加终结点

此步骤定义一个终结点，IoT 中心会将数据路由到该终结点。 可根据偏好使用 [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 命令或 [az iot hub routing-endpoint create](/cli/azure/iot/hub/route#az_iot_hub_route_create) CLI 命令创建此终结点。

下面是用于创建终结点的命令的参数列表：

|PowerShell 参数|CLI 参数|说明|
|---|---|---|
|ResourceGroupName|resource-group|IoT 中心资源的资源组名称。|
|名称|hub-name|IoT 中心资源的名称。|
|EndpointName|endpoint-name|使用想要分配给创建的终结点的名称。|
|EndpointType|endpoint-type|IoT 中心需要连接的终结点的类型。 对于 PowerShell 和 CLI ，请分别使用文本值“EventHub”和“eventhub”。|
|EndpointResourceGroup|endpoint-resource-group|IoT 连接器的 FHIR 服务资源的资源组名称。 可以从 FHIR 服务的 "概述" 页获取此值。|
|EndpointSubscriptionID|endpoint-subscription-id|IoT 连接器的 FHIR 服务资源的订阅 ID。 可以从 FHIR 服务的 "概述" 页获取此值。|
|ConnectionString|connection-string|IoT 连接器的连接字符串。 使用在上一步中获得的值。|

### <a name="add-a-message-route"></a>添加消息路由
此步骤使用上面创建的终结点定义消息路由。 可根据偏好使用 [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 命令或 [az iot hub route create](/cli/azure/iot/hub/route) CLI 命令创建路由。

下面是可以与添加消息路由的命令配合使用的参数列表：

|PowerShell 参数|CLI 参数|说明|
|---|---|---|
|ResourceGroupName|g|IoT 中心资源的资源组名称。|
|名称|hub-name|IoT 中心资源的名称。|
|EndpointName|endpoint-name|上面创建的终结点的名称。|
|RouteName|route-name|要分配给正在创建的消息路由的名称。|
|源|source-type|要发送给终结点的数据的类型。 对于 PowerShell 和 CLI，分别使用文本值“Devicemessages”和“devicemessages”。|

## <a name="send-device-message-to-azure-iot-hub"></a>向 Azure IoT 集线器发送设备消息

使用设备（真实或模拟）将下面显示的示例心率消息发送到 Azure IoT 中心。 此消息将路由到 IoT 连接器，其中，消息将转换为 FHIR 观测资源并存储到 FHIR 服务。

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> 请确保发送与 IoT 连接器配置的 [映射模板](how-to-use-fhir-mapping-iot.md) 相符的设备消息。

## <a name="view-device-data-in-fhir-service"></a>查看 FHIR service 中的设备数据

你可以使用 Postman 查看 FHIR 服务上 IoT 连接器创建的 FHIR 观测资源 (s) 。 有关信息，请参阅 [使用 Postman 访问 FHIR 服务](./../use-postman.md)和发出对的 `GET` 请求， `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 查看在上述示例消息中提交的使用心率值 FHIR 资源。

> [!TIP]
> 确保用户对 FHIR 服务数据平面具有适当的访问权限。 使用 [Azure 基于角色的访问控制 (Azure RBAC)](../azure-api-for-fhir/configure-azure-rbac.md) 分配所需的数据平面角色。

## <a name="next-steps"></a>后续步骤

在本教程中，将设置 Azure IoT 集线器，以便将设备数据路由到 IoT 连接器。 选择下面的 "后续步骤" 以了解有关 IoT 连接器的详细信息：

了解 IoT 连接器中数据流的不同阶段。

>[!div class="nextstepaction"]
>[IoT 连接器数据流](iot-data-flow.md)

 (FHIR&#174;) 是 HL7 的注册商标，并与 HL7 的权限一起使用。