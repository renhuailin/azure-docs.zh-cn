---
title: 与 Azure Maps 集成
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure Functions 创建一个函数，该函数可使用孪生图和 Azure 数字孪生通知来更新 Azure Maps 室内地图。
author: baanders
ms.author: baanders
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: dd161468cda08a0046f2f46a79a973e4bbf8b49a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735297"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>使用 Azure 数字孪生更新 Azure Maps 室内地图

本文展示了通过 [Azure Maps](../azure-maps/about-azure-maps.md) 使用 Azure 数字孪生数据更新室内地图上显示的信息时需要执行的步骤。 Azure 数字孪生存储 IoT 设备关系图，并将遥测数据路由到不同的终结点，这使它成为用于更新地图上的信息覆盖的完美服务。

本操作说明将涵盖以下内容：

1. 配置你的 Azure 数字孪生实例，以将孪生体更新事件发送到 [Azure Functions](../azure-functions/functions-overview.md) 中的函数。
2. 创建一个函数来更新 Azure Maps 室内地图特征状态集。
3. 如何在 Azure 数字孪生图中存储地图 ID 和特征状态集 ID。

### <a name="prerequisites"></a>必备条件

* 按照 Azure 数字孪生的[连接端到端解决方案](./tutorial-end-to-end.md)进行操作。
    * 你将使用额外的终结点和路由扩展此孪生体。 你还将向你在该教程中创建的函数应用添加另一个函数。 
* 按照 Azure Maps 的[使用 Azure Maps Creator 创建室内地图](../azure-maps/tutorial-creator-indoor-maps.md)创建具有某个“特征状态集”的 Azure Maps 室内地图。
    * [特征状态集](../azure-maps/creator-indoor-maps.md#feature-statesets)是分配给数据集特征（例如房间或设备）的动态属性（状态）的集合。 在上述 Azure Maps 教程中，特征状态集存储会在地图上显示的房间状态。
    * 你需要特征状态集 ID 和 Azure Maps 订阅密钥。

### <a name="topology"></a>拓扑

下图展示了本教程中的室内地图集成元素在更大的端到端 Azure 数字孪生方案中的相应位置。

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="端到端方案中的 Azure 服务的图示，其中突出显示了 Indoor Maps 集成块。" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>创建一个用于在孪生体更新时更新地图的函数

首先，你将在 Azure 数字孪生中创建一个路由，以便将所有孪生体更新事件转发到事件网格主题。 然后，你将使用一个函数读取这些更新消息，更新 Azure Maps 中的特征状态集。 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>对孪生体更新通知创建路由和筛选器

每当孪生体的状态更新时，Azure 数字孪生实例就可以发出孪生体更新事件。 上面链接的 Azure 数字孪生[连接端到端解决方案](./tutorial-end-to-end.md)展示了以下方案：使用温度计来更新附加到房间的孪生体的温度属性。 你将扩展该解决方案，方法是：订阅孪生体的更新通知，并使用该信息来更新地图。

此模式直接从房间孪生体读取数据，而不是从 IoT 设备读取数据，这样你就能灵活地更改温度的基础数据源，而无需更新映射逻辑。 例如，你可以添加多个温度计或将此房间设置为与另一个房间共用一个温度计，所有这些都不需要更新你的地图逻辑。

1. 创建一个事件网格主题，用于接收来自 Azure 数字孪生实例的事件。
    ```azurecli-interactive
    az eventgrid topic create --resource-group <your-resource-group-name> --name <your-topic-name> --location <region>
    ```

2. 创建一个终结点，用于将事件网格主题关联到 Azure 数字孪生。
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> --dt-name <your-Azure-Digital-Twins-instance-name>
    ```

3. 在 Azure 数字孪生中创建路由，将孪生体更新事件发送到终结点。

    >[!NOTE]
    >目前，Cloud Shell 中存在一个已知问题，该问题会影响以下命令组：`az dt route`、`az dt model` 和 `az dt twin`。
    >
    >若要解决此问题，请在运行命令之前在 Cloud Shell 中运行 `az login`，或者使用[本地 CLI](/cli/azure/install-azure-cli) 而不使用 Cloud Shell。 有关此操作的详细信息，请参阅[故障排除：Azure 数字孪生中的已知问题](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)。

    ```azurecli-interactive
    az dt route create --dt-name <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>创建一个函数来更新地图

你将在端到端教程（[连接端到端解决方案](./tutorial-end-to-end.md)）的函数应用中创建一个事件网格触发的函数。 此函数会解压这些通知并将更新发送到 Azure Maps 特征状态集，以更新一个房间的温度。

有关参考信息，请参阅以下文档：Azure Functions 的 Azure 事件网格触发器。

将函数代码替换为以下代码。 它将仅筛选出空间孪生体的更新，读取更新的温度，并将该信息发送到 Azure Maps。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

你需要在函数应用中设置两个环境变量。 其中一个是你的 [Azure Maps 主订阅密钥](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)，另一个是你的 [Azure Maps 状态集 ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)。

```azurecli-interactive
az functionapp config appsettings set --name <your-function-app-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-function-app-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>在地图上查看实时更新

若要查看实时更新的温度，请执行以下步骤：

1. 通过运行 Azure 数字孪生[连接端到端解决方案](tutorial-end-to-end.md)中的 DeviceSimulator 项目，开始发送模拟的 IoT 数据。 有关此操作的说明，请参阅配置并运行模拟部分。
2. 使用 [Azure Maps Indoor](../azure-maps/how-to-use-indoor-module.md) 模块呈现你在 Azure Maps Creator 中创建的室内地图。
    1. 将室内地图[使用 Azure Maps Indoor Maps 模块](../azure-maps/how-to-use-indoor-module.md)的[示例：使用 Indoor Maps 模块](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module)部分的 HTML 复制到一个本地文件。
    1. 将本地 HTML 文件中的订阅密钥、tilesetId 和 statesetID 替换为你的值。
    1. 在浏览器中打开该文件。

两个样本发送的温度在一个兼容的范围内，所以你应该看到 121 房间的颜色在地图上大约每 30 秒更新一次。

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="办公室地图的屏幕截图，其中的 121 房间显示为橙色。":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>将地图信息存储在 Azure 数字孪生中

现在，你已经有了用来更新地图信息的硬编码解决方案，可以使用 Azure 数字孪生图来存储更新室内地图所需的所有信息了。 这将包括每个地图和位置的相应状态集 ID、地图订阅 ID 和特征 ID。 

此特定示例的解决方案将涉及的内容包括：更新每个顶级空间以使其具有状态集 ID 和地图订阅 ID 属性，以及更新每个房间以使其具有特征 ID。 你需要在初始化孪生图时将这些值设置一次，然后就可以针对每个孪生体更新事件查询这些值。

你可以根据拓扑配置将这三个属性存储在与地图的粒度相关的不同级别。

## <a name="next-steps"></a>后续步骤

若要了解管理、升级孪生图和从中检索信息的详情，请查看以下参考内容：

* [管理数字孪生](./how-to-manage-twin.md)
* [查询孪生图](./how-to-query-graph.md)
