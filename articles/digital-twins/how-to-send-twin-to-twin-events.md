---
title: 设置孪生到孪生的事件处理
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 中创建用于通过孪生图形传播事件的函数。
author: baanders
ms.author: baanders
ms.date: 8/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 68229c648968711bf65c0870c2fb38903376b1d6
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252582"
---
# <a name="set-up-twin-to-twin-event-handling-with-azure-functions"></a>使用 Azure Functions 设置孪生到孪生的事件处理

完全连接的 Azure 数字孪生图形由事件传播驱动。 数据从外部源（如 IoT 中心）到达 Azure 数字孪生，然后通过 Azure 数字孪生图形进行传播，并根据需要更新相关孪生。

例如，假设有一个表示建筑物中的楼层和房间的图形，其中每个楼层包含多个房间。 你可能想要设置孪生到孪生的数据流，这样每次更新房间孪生的温度属性时，都会为同一楼层上的所有房间计算新的平均温度，并更新楼层孪生的温度属性以反映其包含（包括已更新的属性）的所有房间的新平均温度。 

在本文中，你将了解如何将事件从孪生发送到孪生，以便更新孪生以响应属性更改或图形中另一个孪生的其他数据。 目前，孪生到孪生的更新通过设置一个 [Azure 函数](../azure-functions/functions-overview.md)进行处理，该函数监视应该会影响图形的其他区域的生命周期事件并相应地更改其他孪生。

## <a name="prerequisites"></a>先决条件

本文使用 Visual Studio。 你可从 [Visual Studio 下载](https://visualstudio.microsoft.com/downloads/)中下载最新版本。

若要设置孪生到孪生的处理，需要一个要使用的 Azure 数字孪生实例。 有关如何创建实例的说明，请参阅[设置 Azure 数字孪生实例和身份验证](./how-to-set-up-instance-portal.md)。 实例应至少包含两个要在其中发送数据的孪生。

（可选）你可能还想要[通过 IoT 中心为孪生设置自动遥测引入](how-to-ingest-iot-hub-data.md)。 将数据从孪生发送到孪生不需要这样做，但这是完整解决方案的重要部分，其中孪生图形由实时遥测驱动。

## <a name="set-up-endpoint-and-route"></a>设置终结点和路由

若要设置孪生到孪生的事件处理，请首先在 Azure 数字孪生中创建终结点以及到该终结点的路由。 正在进行更新的孪生将使用路由将有关其更新事件的信息发送到终结点（事件网格稍后可以选取这些事件，并将它们传递给 Azure 函数进行处理）。

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

## <a name="create-the-azure-function"></a>创建 Azure 函数

接下来，创建一个 Azure 函数，该函数将侦听终结点并接收通过路由发送到该终结点的孪生事件。 

1. 首先，在计算机上的 Visual Studio 中创建 Azure Functions 项目。 有关如何执行此操作的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#create-an-azure-functions-project)。

2. 将以下包添加到项目（可以使用 Visual Studio NuGet 包管理器，也可以在命令行工具中使用 `dotnet` 命令）。

    * [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
    * [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
    * [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid)

3. 填写函数的逻辑。 可以在 [azure-digital-twins-getting-started](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/azure-functions) 存储库中查看多个方案的示例函数代码，以帮助你入门。

5. 将函数应用发布到 Azure。 若要了解如何发布函数应用，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

[!INCLUDE [digital-twins-verify-function-publish.md](../../includes/digital-twins-verify-function-publish.md)]

### <a name="configure-the-function-app"></a>配置函数应用

在函数可以访问 Azure 数字孪生之前，需要实例的一些相关信息以及访问该实例的权限。 在本部分中，为函数分配访问角色并配置应用程序设置，使其能够查找并访问实例。

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="connect-the-function-to-event-grid"></a>将函数连接到事件网格

接下来，将 Azure 函数订阅到先前创建的事件网格主题。 这将确保数据可以通过事件网格主题从更新的孪生流向函数。

为此，你将创建一个事件网格订阅，使其将来自你之前创建的事件网格主题的数据发送到 Azure 函数。

使用以下 CLI 命令，填写订阅 ID、资源组、函数应用和函数名称的占位符。

```azurecli-interactive
az eventgrid event-subscription create --name <name-for-your-event-subscription> --source-resource-id /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.EventGrid/topics/<your-event-grid-topic> \ --endpoint-type azurefunction --endpoint /subscriptions/<subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.Web/sites/<your-function-app-name>/functions/<function-name> 
```

现在，函数可以通过事件网格主题接收事件。 数据流设置已完成。

## <a name="test-and-verify-results"></a>测试和验证结果

最后一步是验证流是否正常工作，方法是更新孪生并检查相关孪生是否根据 Azure 函数中的逻辑进行更新。

若要启动该过程，请更新作为事件流源的孪生。 可以使用 [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_update)、[Azure 数字孪生 SDK](how-to-manage-twin.md#update-a-digital-twin) 或 [Azure 数字孪生 REST API](how-to-use-postman.md?tabs=data-plane) 进行更新。

接下来，查询相关孪生的 Azure 数字孪生实例。 可以使用 [Azure CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_query) 或 [Azure 数字孪生 REST API 和 SDK](how-to-query-graph.md#run-queries-with-the-api)。 验证孪生是否收到数据并按预期更新。

## <a name="next-steps"></a>后续步骤

在本文中，你在 Azure 数字孪生中设置了孪生到孪生的事件处理。 接下来，设置一个 Azure 函数，根据来自 IoT 中心设备的传入遥测自动触发此流：[从 IoT 中心传入遥测](how-to-ingest-iot-hub-data.md)。
