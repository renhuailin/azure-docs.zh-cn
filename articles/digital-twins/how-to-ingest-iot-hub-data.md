---
title: 从 IoT 中心引入遥测数据
titleSuffix: Azure Digital Twins
description: 请参阅如何从 IoT 中心引入设备遥测消息。
author: baanders
ms.author: baanders
ms.date: 9/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8fd5b6a046c53a34e41340250c8d6ac38fdf5706
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128667343"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>将 IoT 中心遥测数据引入到 Azure 数字孪生

Azure 数字孪生是使用来自 IoT 设备和其他来源的数据驱动的。 要在 Azure 数字孪生中使用的设备数据的常见源为 [IoT 中心](../iot-hub/about-iot-hub.md)。

将数据引入到 Azure 数字孪生的过程是设置外部计算资源（例如使用 [Azure Functions](../azure-functions/functions-overview.md) 创建的函数）。 函数接收数据，并相应地使用 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) 来设置属性或在[数字孪生](concepts-twins-graph.md)上触发遥测事件。 

本操作说明文档介绍编写可从 IoT 中心引入遥测数据的函数的过程。

## <a name="prerequisites"></a>先决条件

在继续此示例之前，需要将以下资源设置为先决条件：
* IoT 中心。 有关说明，请参阅此 [IoT 中心快速入门](../iot-hub/quickstart-send-telemetry-cli.md)中的“创建 IoT 中心”部分。
* 将接收设备遥测的 Azure 数字孪生实例。 有关说明，请参阅[设置 Azure 数字孪生实例和身份验证](./how-to-set-up-instance-portal.md)。

本文还使用 Visual Studio。 你可从 [Visual Studio 下载](https://visualstudio.microsoft.com/downloads/)中下载最新版本。

## <a name="example-telemetry-scenario"></a>遥测方案示例

本操作说明概述了如何使用 Azure 中的函数将消息从 IoT 中心发送到 Azure 数字孪生。 可以使用多个可能的配置和匹配策略来发送消息，但本文的示例包含以下部分：
* IoT 中心中的恒温器设备，具有已知设备 ID
* 用于表示设备的数字孪生，具有匹配 ID

> [!NOTE]
> 本示例在设备 ID 与相应的数字孪生的 ID 之间使用简单的 ID 匹配，但可以提供从设备到其孪生的更复杂映射（如使用映射表）。

每当恒温器设备发送温度遥测事件时，函数就会处理遥测数据，并且数字孪生的“温度”属性应进行更新。 下图概述了此方案：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="关系图显示 IoT 中心设备将温度遥测数据发送到 Azure 中的一个函数，这更新了 Azure 数字孪生中一个孪生体上的 temperature 属性。" border="false":::

## <a name="add-a-model-and-twin"></a>添加模型和孪生体

在本部分中，你将在 Azure 数字孪生中设置用于表示恒温器设备并使用 IoT 中心提供的信息进行更新的[数字孪生](concepts-twins-graph.md)。

若要创建恒温器类型的孪生，首先需要将恒温器[模型](concepts-models.md)上传到实例，该实例描述了恒温器的属性，稍后将用来创建孪生。

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

然后，需要使用此模型创建一个孪生。 使用以下命令创建名为 thermostat67 的恒温器孪生，并将初始温度值设置为 0.0。

```azurecli-interactive
az dt twin create  --dt-name <instance-name> --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}'
```

成功创建孪生后，命令的 CLI 输出应如下所示：
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "lastUpdateTime": "2021-09-09T20:32:46.6692326Z"
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>创建函数

在本部分中，你将创建一个 Azure 函数，用于根据其收到的 IoT 遥测事件访问 Azure 数字孪生并更新孪生。 按照以下步骤创建并发布函数。

1. 首先，在 Visual Studio 中创建新的函数应用项目。 有关如何执行此操作的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)。

2. 将以下包添加到项目：
    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid/)

3. 将 Visual Studio 生成的 Function1.cs 示例函数重命名为 IoTHubtoTwins.cs 。 用下面的代码替换文件中的代码：

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

    保存函数代码。

4. 使用 IoTHubtoTwins.cs 函数将项目发布到 Azure 中的函数应用。 有关如何执行此操作的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

要访问 Azure 数字孪生，函数应用需要系统托管标识并且该标识具有访问 Azure 数字孪生实例的权限。 你接下来要设置此内容。

### <a name="configure-the-function-app"></a>配置函数应用

接下来，为函数分配访问角色并配置应用程序设置，使之能够访问 Azure 数字孪生实例。

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="connect-your-function-to-iot-hub"></a>将函数连接到 IoT 中心

在本部分中，你会将函数设置为 IoT 中心设备数据的事件目标。 这将确保来自 IoT 中心内恒温器设备的数据将发送到 Azure 函数进行处理。

在 [Azure 门户](https://portal.azure.com/)中，导航到在先决条件部分中创建的 IoT 中心实例。 在“事件”下，为函数创建订阅。

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="显示添加事件订阅的 Azure 门户的屏幕截图。":::

在“创建事件订阅”页中，按如下所示填写字段：
  1. 对于“名称”，请选择要用于事件订阅的任意名称。
  2. 对于“事件架构”，请选择“事件网格架构”。
  3. 对于“系统主题名称”，请选择所需的任意名称。
  1. 对于“筛选到事件类型”，请选中“设备遥测”复选框并取消选中其他事件类型。
  1. 对于“终结点类型”，请选择“Azure 函数”。
  1. 对于“终结点”，请使用“选择终结点”链接选择要用于终结点的 Azure 函数。
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="用于创建事件订阅详细信息的 Azure 门户的屏幕截图。":::

在打开的“选择 Azure 函数”页上，验证或填充以下详细信息。
 1. **订阅**：Azure 订阅。
 2. 资源组：资源组。
 3. 函数应用：函数应用名称。
 4. 槽：生产。
 5. 函数：从下拉列表中选择之前的 IoTHubtoTwins 中的函数。

使用“确认选择”按钮保存详细信息。            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="用于选择函数的 Azure 门户的屏幕截图。":::

选择“创建”按钮以创建事件订阅。

## <a name="send-simulated-iot-data"></a>发送模拟 IoT 数据

若要测试新的 ingress 函数，请使用[连接端到端解决方案](./tutorial-end-to-end.md)中的设备模拟器。 本教程是在[使用 C# 编写的 Azure 数字孪生端到端示例项目](/samples/azure-samples/digital-twins-samples/digital-twins-samples)推动下编写的。 将使用该存储库中的 DeviceSimulator 项目。

在端到端教程中，完成以下步骤：
1. [在 IoT 中心注册模拟设备](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [配置和运行模拟](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>验证结果

在运行上面的设备模拟器时，将更改数字孪生的温度值。 在 Azure CLI 中，运行以下命令以查看温度值。

```azurecli-interactive
az dt twin query --query-command "select * from digitaltwins" --dt-name <Digital-Twins-instance-name>
```

输出应包含温度值，如下所示：

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"dbf2fea8-d3f7-42d0-8037-83730dc2afc5\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "lastUpdateTime": "2021-06-03T17:05:52.0062638Z"
        }
      },
      "Temperature": 70.20518558807913
    }
  ]
}
```

若要查看值更改，请重复运行上面的查询命令。

## <a name="next-steps"></a>后续步骤

了解 Azure 数字孪生的数据入口和出口：
* [数据引入和传出](concepts-data-ingress-egress.md)
