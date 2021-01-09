---
title: 从 IoT 中心引入遥测数据
titleSuffix: Azure Digital Twins
description: 请参阅如何从 IoT 中心摄取设备遥测消息。
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051082"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>将 IoT 中心遥测数据引入 Azure 数字孪生

Azure 数字孪生由 IoT 设备和其他来源的数据驱动。 要在 Azure 数字孪生中使用的设备数据的常见来源为 [IoT 中心](../iot-hub/about-iot-hub.md)。

将数据引入 Azure 数字孪生的过程是设置外部计算资源，如使用 [Azure Functions](../azure-functions/functions-overview.md)创建的函数。 函数接收数据，并使用 [DigitalTwins api](/rest/api/digital-twins/dataplane/twins) 来设置属性或在 [数字孪生](concepts-twins-graph.md) 上触发遥测事件。 

本操作说明文档介绍了如何编写可从 IoT 中心引入遥测数据的函数。

## <a name="prerequisites"></a>先决条件

继续此示例之前，需要将以下资源设置为系统必备组件：
* **IoT 中心**。 有关说明，请参阅 [此 Iot 中心快速入门](../iot-hub/quickstart-send-telemetry-cli.md)中的 *创建 iot 中心* 部分。
* 具有调用数字克隆实例的正确权限的 **函数**。 有关说明，请参阅 [*如何：在 Azure 中设置用于处理数据的函数*](how-to-create-azure-function.md)。 
* 将接收设备遥测数据的 **Azure 数字孪生实例**。 有关说明，请参阅 [*如何：设置 Azure 数字孪生实例和身份验证*](./how-to-set-up-instance-portal.md)。

### <a name="example-telemetry-scenario"></a>遥测方案示例

本操作指南概述了如何在 Azure 中使用函数将消息从 IoT 中心发送到 Azure 数字孪生。 可以使用多个可能的配置和匹配策略来发送消息，但本文的示例包含以下部分：
* IoT 中心中具有已知设备 ID 的温度计设备
* 用于表示设备的数字克隆，具有匹配 ID

> [!NOTE]
> 此示例在设备 ID 与相应的数字克隆的 ID 之间使用简单的 ID 匹配，但可以提供更复杂的映射，从设备到其克隆 (如) 的映射表。

每当恒温器设备发送温度遥测事件时，函数就会处理遥测数据，而数字克隆的 *温度* 属性应该会更新。 下图中概述了此方案：

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="显示流程图的关系图。在此图表中，IoT 中心设备通过 IoT 中心将温度遥测发送到 Azure 中的一个函数，该函数更新了 Azure 数字孪生中的每个克隆的温度属性。" border="false":::

## <a name="add-a-model-and-twin"></a>添加模型和克隆

你可以使用以下 CLI 命令添加/上传模型，然后使用此模型创建一个克隆，此模型将使用 IoT 中心提供的信息进行更新。

模型如下所示：
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

若要将 **此模型上传到孪生实例**，请打开 Azure CLI，并运行以下命令：

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

然后，需要 **使用此模型创建一个** 克隆。 使用以下命令创建一个克隆并将0.0 设置为初始温度值。

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

成功的 "创建" 命令的输出应如下所示：
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>创建函数

本部分使用与 [*操作方法：设置用于处理数据的函数*](how-to-create-azure-function.md)相同的 Visual Studio 启动步骤和函数主干。 该主干处理身份验证并创建服务客户端，使你可以处理数据，并在响应中调用 Azure 数字孪生 Api。 

在接下来的步骤中，你将向其添加特定代码，用于处理 IoT 中心的 IoT 遥测事件。  

### <a name="add-telemetry-processing"></a>添加遥测处理
    
遥测事件以来自设备的消息形式提供。 添加遥测处理代码的第一步是从事件网格事件中提取此设备消息的相关部分。 

不同的设备可能以不同的方式构造其消息，因此 **此步骤的代码取决于所连接的设备。** 

下面的代码显示了将遥测作为 JSON 发送的简单设备的示例。 本示例在 [*教程：连接端到端解决方案*](./tutorial-end-to-end.md)中进行了全面的探讨。 以下代码将查找发送消息的设备的设备 ID 以及温度值。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

接下来的代码示例采用 ID 和温度值，并使用它们 "patch" (对克隆) 进行更新。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>更新函数代码

现在，你已了解先前示例中的代码，请从 Visual Studio 中的 " [*先决条件*](#prerequisites) " 部分打开函数。  (如果你没有在 Azure 中创建的函数，请访问必备组件中的链接立即创建一个) 。

将函数的代码替换为此示例代码。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

保存函数代码，并将函数应用发布到 Azure。 若要了解如何操作，请参阅如何在 [*Azure 中设置用于处理数据的函数*](how-to-create-azure-function.md)中的 [*发布函数应用*](./how-to-create-azure-function.md#publish-the-function-app-to-azure)。

成功发布后，将在 Visual Studio 命令窗口中看到输出，如下所示：

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
你还可以在 [Azure 门户](https://portal.azure.com/)中验证发布过程的状态。 搜索 _资源组_ 并导航到 " _活动日志_ "，并在列表中查找 " _获取 web 应用发布配置文件_ "，并验证状态是否为 "成功"。

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="显示发布过程状态的 Azure 门户的屏幕截图。":::

## <a name="connect-your-function-to-iot-hub"></a>将函数连接到 IoT 中心

设置中心数据的事件目标。
在 [Azure 门户](https://portal.azure.com/)中，导航到在 " [*先决条件*](#prerequisites) " 部分中创建的 IoT 中心实例。 在 " **事件**" 下，为函数创建订阅。

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="显示添加事件订阅的 Azure 门户屏幕截图。":::

在 " **创建事件订阅** " 页中，按如下所示填写字段：
  1. 在 " **名称**" 下，将订阅命名为你所需的名称。
  2. 在 " **事件架构**" 下，选择 " _事件网格架构_"。
  3. 在 " **事件类型**" 下，选择 " _设备遥测_ " 复选框，并取消选中其他事件类型。
  4. 在 " **终结点类型**" 下，选择 " _Azure Function_"。
  5. 在 " **终结点**" 下，选择 " _选择终结点_ " 链接来创建终结点。
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="用于创建事件订阅详细信息 Azure 门户的屏幕截图":::

在打开的 " _选择 Azure 函数_ " 页上，验证以下详细信息。
 1. **订阅**：Azure 订阅
 2. **资源组**：资源组
 3. **函数应用**：函数应用名称
 4. **槽**： _生产_
 5. **函数**：从下拉列表中选择您的函数。

选择 " _确认选择_ " 按钮保存详细信息。            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="用于选择函数的 Azure 门户屏幕截图。":::

选择 " _创建_ " 按钮创建事件订阅。

## <a name="send-simulated-iot-data"></a>发送模拟 IoT 数据

若要测试新的入口函数，请使用 [*教程：连接端到端解决方案*](./tutorial-end-to-end.md)中的设备模拟器。 该教程由用 c # 编写的示例项目驱动。 示例代码位于此处： [Azure 数字孪生端到端示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples)。 将使用该存储库中的 **devicesimulator.exe** 项目。

在端到端教程中，完成以下步骤：
1. [*在 IoT 中心注册模拟设备*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*配置和运行模拟*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>验证结果

在运行上面的设备模拟器时，将更改数字克隆的温度值。 在 Azure CLI 中运行以下命令以查看温度值。

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

输出应包含温度值，如下所示：

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

若要查看值更改，请重复运行上面的查询命令。

## <a name="next-steps"></a>后续步骤

阅读有关 Azure 数字孪生的数据入口和出口：
* [*概念：与其他服务集成*](concepts-integration.md)
