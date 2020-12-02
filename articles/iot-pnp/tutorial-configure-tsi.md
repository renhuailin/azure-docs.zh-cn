---
title: 使用时序见解来存储和分析 Azure IoT 即插即用设备遥测 | Microsoft Docs
description: 设置时序见解环境并连接 IoT 中心，以便从 IoT 即插即用设备查看和分析遥测。
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ca2319a78fb4c0c720a21e97944d5b75ada9d008
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014915"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>预览版教程：创建时序见解第 2 代并与其连接，以便存储、可视化和分析 IoT 即插即用设备遥测

本教程将介绍如何创建 [Azure 时序见解第 2 代](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) 环境并对其正确配置，以便与 IoT 即插即用解决方案集成。 使用 TSI 在物联网 (IoT) 规模上收集、处理、存储、查询和可视化时序数据。

首先，预配一个 TSI 环境，并将 IoT 中心作为流式处理事件源进行连接。 然后，将根据用于温度控制器和恒温器设备的[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 示例模型文件，通过模型同步来创建[时序模型](../time-series-insights/concepts-model-overview.md)。

> [!NOTE]
> 此集成处于预览状态。 DTDL 设备模型映射到时序模型的方式可能会发生变化。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

此时，你应具备：

* Azure IoT 中心。
* 链接到 IoT 中心的 DPS 实例，以及 IoT 即插即用设备的单个设备注册。
* 单组件或多组件设备与 IoT 中心之间的连接，该连接流式传输模拟数据。

为了绕开在本地安装 Azure CLI 的要求，可使用 Azure Cloud Shell 设置云服务。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>准备事件源

以前创建的 IoT 中心将是 TSI 环境的[事件源](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources)。

> [!IMPORTANT]
> 禁用任何现有的 IoT 中心路由。 将 IoT 中心用作配置了[路由](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints)的 TSI 事件源时，存在一个已知问题。 暂时禁用所有路由终结点，当 IoT 中心连接到 TSI 时，可以重新启用它们。

在 IoT 中心创建一个唯一的使用者组供 TSI 使用。 将 `my-pnp-hub` 替换为之前使用的 IoT 中心的名称：

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>选择时序 ID

预配 TSI 环境时，需要选择时序 ID。 选择适当的时序 ID 至关重要，因为属性不可变并且在设置后无法更改。 时序 ID 类似于数据库分区键。 时序 ID 充当时序模型的主键。 若要了解详细信息，请参阅[选择时序 ID 的最佳做法](../time-series-insights/how-to-select-tsid.md)。

作为 IoT 即插即用用户，请将 `iothub-connection-device-id` 和 `dt-subject` 构成的组合键指定为时序 ID。 IoT 中心会添加这些分别包含了 IoT 即插即用设备 ID 和设备组件名称的系统属性。

即使 IoT 即插即用设备模型目前不使用组件，也应该将 `dt-subject` 包含在内，作为组合键的一部分，以便将来需要时使用。 由于时序 ID 不可变，Microsoft 建议启用这个选项，以便将来需要时使用。

> [!NOTE]
> 以下示例适用于多组件 TemperatureController 设备，但其中的概念也适用于无组件恒温器设备 。

## <a name="provision-your-tsi-environment"></a>预配 TSI 环境

本节介绍如何预配 Azure 时序见解 Gen2 环境。

以下命令：

* 为环境的[冷存储](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store)创建一个 Azure 存储帐户，专用于历史数据的长期保留和分析。
  * 将 `mytsicoldstore` 替换为冷存储帐户的唯一名称。
* 创建 Azure 时序见解 Gen2 环境，包括七天保留期的热存储和无限保留期的冷存储。
  * 将 `my-tsi-env` 替换为 TSI 环境的唯一名称。
  * 将 `my-pnp-resourcegroup` 替换为在设置过程中使用的资源组的名称。
  * `iothub-connection-device-id, dt-subject` 是时序 ID 属性。

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

连接 IoT 中心事件源。 将 `my-pnp-resourcegroup``my-pnp-hub` 和 `my-tsi-env` 替换为所选的值。 以下命令引用你先前创建的 TSI 使用者组：

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

导航到 [Azure 门户](https://portal.azure.com)中的资源组，然后选择新的时序见解环境。 访问实例概述中显示的“时序见解资源管理器 URL”：

![门户概述页](./media/tutorial-configure-tsi/view-environment.png)

在资源管理器中，可看到三个实例：

* &lt;pnp 设备 ID&gt;，thermostat1
* &lt;pnp 设备 ID&gt;，thermostat2
* &lt;pnp 设备 ID&gt;，`null`

> [!NOTE]
> 第三个标记表示来自 TemperatureController 本身的遥测，例如设备内存的工作集。 因为这是顶级属性，所以组件名称的值为 null。 在后续步骤中，将其更新为用户更易理解的名称。

![资源管理器视图 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>配置模型转换

接下来将 DTDL 设备模型转换为 Azure 时序见解 (TSI) 中的资产模型。 TSI 的时序模型是一种语义建模工具，用于 TSI 内的数据语境化。 时序模型包括三个核心组件：

* [时序模型实例](../time-series-insights/concepts-model-overview.md#time-series-model-instances)。 实例是时序本身的虚拟表示形式。 实例由时序 ID 唯一标识。
* [时序模型层次结构](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies)。 层次结构通过指定属性名称及其关系来组织实例。
* [时序模型类型](../time-series-insights/concepts-model-overview.md#time-series-model-types)。 类型可帮助你定义用于计算的[变量](../time-series-insights/concepts-variables.md)或公式。 类型与某个特定实例关联。

### <a name="define-your-types"></a>定义类型

可以开始将数据引入到 Azure 时序见解第 2 代，无需预定义模型。 当遥测到达时，TSI 尝试基于时序 ID 属性值自动解析时序实例。 为所有实例分配默认类型。 你需要手动创建一个新类型来正确地对实例进行分类。 以下详细信息显示了将设备 DTDL 模型与时序模型类型同步的最简单方法：

* 数字孪生模型标识符将成为你的类型 ID。
* 类型名称可以是模型的名称，也可以是显示名称。
* 模型说明将成为类型的说明。
* 至少为每个具有数值架构的遥测创建一个类型变量。
  * 变量只能使用 numeric 数据类型，但如果将值作为另一种可转换的类型发送（例如 `"0"`），可使用[转换](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions)函数，例如 `toDouble`。
* 变量名称可以是遥测名称，也可以是显示名称。
* 定义时序表达式变量时，请参阅线上的遥测名称及其数据类型。

| DTDL JSON | 时序模型类型 JSON | 示例值 |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents`（数组）| `variables`（对象）  | 查看下面的示例

![DTDL 到时序模型类型](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> 此示例显示三种变量，但每种类型最多可包含 100 个。 不同的变量可以引用相同的遥测值，以根据需要执行不同的计算。 有关筛选器、聚合和标量函数的完整列表，请参阅[时序见解第 2 代时序表达式语法](/rest/api/time-series-insights/reference-time-series-expression-syntax.md)。

打开文本编辑器并将以下 JSON 保存到本地驱动器：

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

在时序见解资源管理器中，选择左侧的模型图标以导航到“模型”选项卡。 选择“类型”，然后选择“上传 JSON” ：

![上传](./media/tutorial-configure-tsi/upload-type.png)

选择“选择文件”，选择之前保存的 JSON，然后选择“上传” 

你将看到新定义的温度控制器类型。

### <a name="create-a-hierarchy"></a>创建层次结构

创建一个层次结构来组织 TemperatureController 父项下的标记。 以下简单示例只有一个级别，但 IoT 解决方案通常具有多个嵌套级别，作用是让标记在组织中所处的物理和语义位置上得到语境化的体现。

选择“层次结构”并选择“添加层次结构” 。 输入“设备组”作为名称并创建一个名为“设备名称”的级别 。 然后，选择“保存”。

![添加层次结构](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>为实例分配正确的类型

接下来将更改实例的类型，并将其置于层次结构中。

选择“实例”选项卡，找到表示设备工作集的实例，然后选择最右侧的“编辑”图标 ：

![编辑实例](./media/tutorial-configure-tsi/edit-instance.png)

单击“类型”下拉列表并选择“温度控制器” 。 输入 defaultComponent <your device name>，更新表示与设备关联的所有顶级标记的实例名称。

![更改实例类型](./media/tutorial-configure-tsi/change-type.png)

选择保存前，请选择“实例字段”选项卡并选中“设备组”框 。 输入 `<your device name> - Temp Controller` 将遥测组合在一起，然后选择“保存”。

![分配到层次结构](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

重复上述步骤，为恒温器标记分配正确的类型和层次结构。

## <a name="view-your-data"></a>查看数据

导航回图表绘制窗格，并展开“设备组”>“你的设备”。 选择“thermostat1”，选择“温度”变量，然后选择“添加”，为值绘图  。 对 thermostat2 和 defaultComponent workingSet 值执行相同的操作  。

![更改 thermostat2 的实例类型](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>后续步骤

* 若要详细了解各种图表绘制选项，包括间隔大小调整和 Y 轴控件，请参阅 [Azure 时序见解资源管理器](../time-series-insights/concepts-ux-panels.md)。

* 若要深入了解环境的时序模型，请参阅 [Azure 时序见解 Gen2 中的时序模型](../time-series-insights/concepts-model-overview.md)一文。

* 若要深入了解查询 API 和时序表达式语法，请参阅 [Azure 时序见解 Gen2 查询 API](/rest/api/time-series-insights/reference-query-apis.md)。
