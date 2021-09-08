---
title: 教程 - 使用 Azure IoT Central 创建运行状况数据会审仪表板 | Microsoft Docs
description: 教程 - 了解如何使用 Azure IoT Central 应用程序模板构建运行状况数据会审仪表板。
author: dominicbetts
ms.author: dobett
ms.date: 09/01/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 70862d562db5371e1169e188c80f9436e6092ea5
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481175"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>教程：构建 Power BI 提供程序仪表板

构建持续患者监视解决方案时，还可以为医护团队创建一个仪表板，以将患者数据可视化。 本教程介绍如何通过 IoT Central 持续患者监视应用程序模板创建 Power BI 实时流式处理仪表板。

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="仪表板 GIF":::

基本体系结构遵循以下结构：

:::image type="content" source="media/dashboard-architecture.png" alt-text="提供程序鉴别分类仪表板":::

在本教程中，你将了解如何执行以下操作：

- 将数据从 Azure IoT Central 导出到 Azure 事件中心
- 设置 Power BI 流数据集
- 将逻辑应用连接到 Azure 事件中心
- 将数据从逻辑应用流式传输到 Power BI
- 构建患者生命体征数据的实时仪表板


## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* Azure IoT Central 患者持续监视应用程序模板。 如果没有所需的应用程序模板，可以遵循[部署应用程序模板](overview-iot-central-healthcare.md)的步骤。

* Azure [事件中心命名空间和事件中心](../../event-hubs/event-hubs-create.md)。

* 用于访问事件中心的逻辑应用。 若要使用 Azure 事件中心触发器启动逻辑应用，需要一个[空白逻辑应用](../../logic-apps/quickstart-create-first-logic-app-workflow.md)。

* 一个 Power BI 服务帐户。 如果没有该帐户，可以[创建 Power BI 服务的免费试用帐户](https://app.powerbi.com/)。 如果你以前未用过 Power BI，请浏览 [Power BI 入门](/power-bi/service-get-started)。


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>设置到 Azure 事件中心的持续数据导出

首先需要在订阅中设置从 Azure IoT Central 应用程序模板到 Azure 事件中心的持续数据导出。 可以遵循本 Azure IoT Central 教程所述的[导出到事件中心](../core/howto-export-data.md)的步骤来完成此设置。 对于本教程，只需导出遥测数据。


## <a name="create-a-power-bi-streaming-dataset"></a>创建 Power BI 流数据集

1. 登录到你的 Power BI 帐户。

1. 在首选的工作区中，通过选择工具栏右上角的“+ 创建”按钮来创建新的流数据集。 对于你要在仪表板上鉴别分类的每个患者，需要单独创建一个数据集。

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="创建流数据集":::


1. 选择数据集的源对应的 **API**。

1. 输入数据集的 **名称**（例如患者姓名），并填写流中的值。 根据来自模拟设备的值，患者持续监视应用程序模板中会显示以下示例信息。 该示例涉及到两个患者：

   * Teddy Silvers，其数据来自 Smart Knee Brace。
   * Yesenia Sanford，其数据来自 Smart Vitals Patch。

   :::image type="content" source="media/enter-dataset-values.png" alt-text="输入数据集值":::

若要详细了解 Power BI 中的流数据集，可以阅读这篇有关 [Power BI 中的实时流](/power-bi/service-real-time-streaming)的文档。


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>将逻辑应用连接到 Azure 事件中心

若要将逻辑应用连接到 Azure 事件中心，可以按照这篇有关[使用 Azure 事件中心和 Azure 逻辑应用发送事件](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action)的文档中的说明操作。 下面是一些建议的参数：

|参数|值|
|---|---|
|内容类型|application/json|
|时间间隔|3|
|频率|秒|

在此步骤结束时，逻辑应用设计器应如下所示：

>[!div class="mx-imgBorder"] 
>![逻辑应用连接到事件中心](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="输入数据集值":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>将数据从逻辑应用流式传输到 Power BI

下一步是分析来自事件中心的数据，以将其流式传输到前面创建的 Power BI 数据集。

在执行此操作之前，需要了解从设备发送到事件中心的 JSON 有效负载。 为此，可以查看此[示例架构](../core/howto-export-data.md#telemetry-format)并根据自己的架构对其进行修改，或使用[服务总线资源管理器](https://github.com/paolosalvatori/ServiceBusExplorer)检查消息。 如果使用的是患者持续监视应用程序，则消息将如下所示：

**Smart Vitals Patch 遥测数据**

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

**Smart Knee Brace 遥测数据**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**属性**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. 检查 JSON 有效负载后，请返回到逻辑应用设计器并选择“+ 新建步骤”。 搜索并添加“初始化变量”作为下一步骤，然后输入以下参数：

   |参数|值|
   |---|---|
   |名称|接口名称|
   |类型|字符串|

   选择“保存”。  

1. 添加名为“身体”、类型为“字符串”的另一个变量。  将在逻辑应用中添加以下操作：

   :::image type="content" source="media/initialize-string-variables.png" alt-text="初始化变量":::
    
1. 选择“+ 新建步骤”并添加一个“分析 JSON”操作。  将此操作重命名为“分析属性”。 对于“内容”，请选择来自事件中心的“属性”。 选择底部的“使用示例有效负载生成架构”，并粘贴上面“属性”部分中的示例有效负载。

1. 接下来，选择“设置变量”操作，并使用已分析的 JSON 属性中的 **iothub-interface-name** 更新“接口名称”变量。 

1. 添加一个“拆分”控件作为下一操作，并选择“接口名称”变量作为“打开”参数。  此控件用于将数据传送到正确的数据集。

1. 在 Azure IoT Central 应用程序的“设备模板”视图中，找到 Smart Vitals Patch 健康数据和 Smart Knee Brace 健康数据的“接口名称”。 为每个“接口名称”的“切换”控件创建两个不同的案例，并相应地重命名该控件。 可将“默认”案例设置为使用“终止”控件，并选择要显示的状态。

   :::image type="content" source="media/split-by-interface.png" alt-text="拆分控件":::

1. 对于 **Smart Vitals Patch** 案例，请添加“分析 JSON”操作。 对于“内容”，请选择来自事件中心的“内容”。 复制并粘贴上述 Smart Vitals Patch 的示例有效负载以生成架构。

1. 添加“设置变量”操作，并使用步骤 7 中已分析的 JSON 中的“身体”数据更新“身体”变量。  

1. 添加一个“条件”控件作为下一操作，并将条件设置为“身体”、“包含”、“心率”。    这可以确保在填充 Power BI 数据集之前，已获得来自 Smart Vitals Patch 的正确数据集。 步骤 7-9 如下所示：

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Smart Vitals - 添加条件":::

1. 对于“条件”的 **True** 案例，请添加一个调用“将行添加到数据集”Power BI 功能的操作。 为此，必须登录到 Power BI。 **False** 案例可以再次使用“终止”控件。

1. 选择适当的“工作区”、“数据集”和“表”。   将在 Power BI 中创建流数据集时指定的参数映射到来自事件中心的已分析 JSON 值。 填充的操作应如下所示：
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="将行添加到 Power BI":::

1. 对于 **Smart Knee Brace** 切换案例，请添加一个“分析 JSON”操作来分析内容（类似于步骤 7）。 然后选择“将行添加到数据集”，以更新 Power BI 中的 Teddy Silvers 数据集。

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="显示如何将行添加到数据集的屏幕截图":::

1. 按“保存”，然后运行逻辑应用。


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>构建患者生命体征数据的实时仪表板

现在，请返回到 Power BI 并选择“+ 创建”，以创建新的 **仪表板**。 为仪表板命名并点击“创建”。

选择顶部导航栏中的省略号图标，然后选择“+ 添加磁贴”。

:::image type="content" source="media/add-tile.png" alt-text="将磁贴添加到仪表板":::

选择要添加的磁贴类型，并根据需要自定义应用。


## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请按以下步骤删除相关的资源：

1. 在 Azure 门户中，可以删除创建的事件中心和逻辑应用资源。

1. 对于 IoT Central 应用程序，请转到“管理”选项卡并选择“删除”。

