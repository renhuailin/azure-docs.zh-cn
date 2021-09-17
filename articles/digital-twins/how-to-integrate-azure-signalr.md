---
title: 与 Azure SignalR 服务集成
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure SignalR 将 Azure 数字孪生遥测数据流式传输到客户端
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8690f69bace02e73f144b563a5541073cb976f22
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770606"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>将 Azure 数字孪生与 Azure SignalR 服务集成

本文介绍如何将 Azure 数字孪生与 [Azure SignalR 服务](../azure-signalr/signalr-overview.md)集成。

通过本文中所述的解决方案，可以将数字孪生遥测数据推送到连接的客户端，例如单个网页或移动应用程序。 因此，客户端将使用 IoT 设备的实时指标和状态进行更新，而无需轮询服务器或提交新的 HTTP 请求来获得更新内容。

## <a name="prerequisites"></a>必备条件

下面是在继续操作之前应完成的必备条件：

* 在将你的解决方案与本文中的 Azure SignalR 服务集成之前，应先完成 Azure 数字孪生[连接端到端解决方案](tutorial-end-to-end.md)，因为本操作方法文章以该方案为基础。 本教程介绍如何设置 Azure 数字孪生实例，以使该实例与虚拟 IoT 设备一起触发数字孪生体更新。 本操作说明将使用 Azure SignalR 服务将这些更新连接到示例 Web 应用。

* 需要使用本教程中的以下值：
  - 事件网格主题
  - 资源组
  - 应用服务/函数应用名称
    
* 需要在计算机上安装 Node.js。

确保使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)，因为需要在本指南中使用它。

## <a name="solution-architecture"></a>解决方案体系结构

将通过以下路径将 Azure SignalR 服务附加到 Azure 数字孪生。 图中 A、B 和 C 部分取自[端到端教程必备条件](tutorial-end-to-end.md)的体系结构图。 在本操作指南文章中，将在现有体系结构上生成 D 部分，其中包括与 SignalR 和客户端应用通信的两个新 Azure 函数。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="端到端方案中的 Azure 服务示意图，描述了 Azure 数字孪生中的数据流动。" lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>下载示例应用程序

首先，下载所需的示例应用。 需要以下两个示例：
* [Azure 数字孪生端到端示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)：此示例包含一个 AdtSampleApp，它包含两个用于移动 Azure 数字孪生实例相关数据的 Azure 函数（可以在[连接端到端解决方案](tutorial-end-to-end.md)中详细了解此方案）。 此外还包含一个 *devicesimulator.exe* 示例应用程序，可模拟 IoT 设备，每秒生成一个新的温度值。
    - 如果尚未下载此示例作为本教程[先决条件](#prerequisites)的一部分，[请导航到示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)，然后选择标题下面的“浏览代码”按钮。 此时会转到这些示例的 GitHub 存储库，可以通过选择“代码”按钮和“下载 ZIP”将其下载为 .zip 。

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub 上的 digital-twins-samples 存储库的屏幕截图，其中包含将该存储库下载为 zip 的步骤。" lightbox="media/includes/download-repo-zip.png":::

    此按钮会将示例存储库副本以 digital-twins-samples-master.zip 形式下载到计算机上。 解压缩文件夹。
* [SignalR 集成 Web 应用示例](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)：此示例 React Web 应用将使用来自 Azure SignalR 服务中的 Azure 数字孪生遥测数据。
    -  导航到示例链接，并使用相同的下载过程将示例副本以 _**digitaltwins-signalr-webapp-sample-main.zip**_ 形式下载到计算机。 解压缩文件夹。

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

保持打开浏览器窗口并显示 Azure 门户，因为将在下一部分再次使用它。

## <a name="publish-and-configure-the-azure-functions-app"></a>发布并配置 Azure 函数应用

在本部分中，将设置两个 Azure 函数：
* **negotiate** - HTTP 触发器函数。 它使用 *SignalRConnectionInfo* 输入绑定生成并返回有效的连接信息。
* **broadcast** - [事件网格](../event-grid/overview.md)触发器函数。 它通过事件网格接收 Azure 数字孪生遥测数据，并使用在上一步创建的 *SignalR* 实例的输出绑定将消息广播到所有连接的客户端应用程序。

启动 Visual Studio（或选择其他代码编辑器），然后在 *digital-twins-samples-master > ADTSampleApp* 文件夹中打开代码解决方案。 按下列步骤创建函数：

1. 在 *SampleFunctionsApp* 项目中，新建一个名为 **SignalRFunctions.cs** 的 c # 类。 有关如何创建新类的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)。

1. 将类文件的内容替换为以下代码：
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. 在 Visual Studio 的“包管理器控制台窗口或计算机的任何命令窗口中，导航到 *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* 文件夹，并运行以下命令将 `SignalRService` NuGet 包安装到项目：
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    运行此命令应该能解决类中的所有依赖项问题。

1. 将函数发布到 Azure。 可以将其发布到端到端教程[必备条件](#prerequisites)中使用的同一应用服务/函数应用，也可以创建新的应用服务/函数应用，但可能希望使用同一个应用来最大程度减少重复项。 若要了解如何使用 Visual Studio 发布函数，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

### <a name="configure-the-function"></a>配置函数

接下来，将该函数配置为与 Azure SignalR 实例通信。 首先收集 SignalR 实例的连接字符串，然后将其添加到函数应用的设置。

1. 转到 [Azure 门户](https://portal.azure.com/)，在门户顶部的搜索栏中搜索 SignalR 实例的名称。 选择该实例以将其打开。
1. 从实例菜单选择“密钥”，以查看 SignalR 服务实例的连接字符串。
1. 选择“复制”图标以复制主连接字符串。

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="显示 SignalR 实例的“密钥”页的 Azure 门户屏幕截图。截图中正在复制连接字符串。" lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. 最后，使用以下 Azure CLI 命令将 Azure SignalR 连接字符串添加到函数应用的设置中。 此外，还需将占位符替换为在[教程必备条件](how-to-integrate-azure-signalr.md#prerequisites)中准备的资源组和应用服务/函数应用名称。 可在 [Azure Cloud Shell](https://shell.azure.com) 中运行此命令，如果[计算机中安装了 Azure CLI](/cli/azure/install-azure-cli)，也可在本地运行它：
 
    ```azurecli-interactive
    az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    此命令的输出将打印为 Azure 函数设置的所有应用设置。 在列表底部查找 `AzureSignalRConnectionString`，验证是否已添加。

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="命令窗口中输出的屏幕截图，显示名为“AzureSignalRConnectionString”的列表项。":::

## <a name="connect-the-function-to-event-grid"></a>将函数连接到事件网格

接下来，将 Azure 函数 broadcast 订阅到在[教程必备条件](how-to-integrate-azure-signalr.md#prerequisites)期间创建的事件网格主题。 此操作会使遥测数据从 thermostat67 孪生体经由事件网格主题流到该函数。 在这里，该函数会将数据广播到所有客户端。

要广播此数据，需要从事件网格主题中创建对 broadcast Azure 函数的事件订阅作为终结点。

在 [Azure 门户](https://portal.azure.com/)中，导航到事件网格主题，方法是在顶部搜索栏中搜索其名称。 选择“+ 事件订阅”。

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure 门户的屏幕截图，其中显示了如何创建事件订阅。":::

在“创建事件订阅”页，按如下所示填写字段（默认填充的字段未提及）：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* 终结点详细信息 > 终结点：选择“选择终结点”链接，这将打开“选择 Azure 函数”窗口：
    - 填写“订阅”、“资源组”、“函数应用”和“函数”(broadcast)   。 在选择订阅后，其中一些字段可能会自动填充。
    - 选择“确认所选内容”  。

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure 门户中“创建事件订阅”表单的屏幕截图。":::

返回“创建事件订阅”页，选择“创建”。 

此时，“事件网格主题”页中应会出现两个事件订阅。

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure 门户的屏幕截图，其中显示了“事件网格主题”页中的两个事件订阅。" lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>配置和运行 Web 应用

在本部分中，将看到操作结果。 首先，将示例客户端 Web 应用配置为连接到已设置的 Azure SignalR 流。 接下来，启动模拟设备示例应用，该应用通过 Azure 数字孪生实例发送遥测数据。 之后，查看示例 Web 应用，可以看到实时更新示例 Web 应用的模拟设备数据。

### <a name="configure-the-sample-client-web-app"></a>配置示例客户端 Web 应用

接下来，配置示例客户端 Web 应用。 首先收集 negotiate 函数的 HTTP 终结点 URL，然后在计算机中使用它配置应用代码。

1. 转到 Azure 门户的“[函数应用](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp)”页，并从列表中选择函数应用。 在应用菜单中，选择“函数”，然后选择 negotiate 函数。

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure 门户函数应用的屏幕截图，其中在菜单中突出显示了“函数”，在函数列表中突出显示了“negotiate”。":::

1. 选择“获取函数 URL”并复制自开头到 /api 结尾的值（不包含最后的 /negotiate?） 。 下一步骤中将使用此值。

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure 门户的屏幕截图，其中显示了“negotiate”函数并突出显示了“获取函数 URL”按钮和函数 URL。":::

1. 使用 Visual Studio 或所选的任何代码编辑器，打开在下载示例应用程序部分中下载的 _digitaltwins-signalr-webapp-main_ 已解压缩文件夹。

1. 打开 src/App.js 文件，将 `HubConnectionBuilder` 中的函数 URL 替换为上一个步骤中保存的 negotiate 函数的 HTTP 终结点 URL：

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function-URL>')
            .build();
    ```
1. 在 Visual Studio 的“开发人员命令提示符”或计算机上的任何命令窗口中，导航到 digitaltwins-signalr-webapp-sample-main\src 文件夹。 运行以下命令以安装依赖节点包：

    ```cmd
    npm install
    ```

接下来，在 Azure 门户的函数应用中设置权限：
1. 在 Azure 门户的“[函数应用](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp)”页中，选择函数应用实例。

1. 在实例菜单中向下滚动，选择“CORS”。 在“CORS”页中，通过将 `http://localhost:3000` 输入到空白方框中，添加其作为允许的源。 选中“启用 Access-Control-Allow-Credentials”复选框，然后选择“保存” 。

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="Azure 门户的屏幕截图，其中显示了 Azure Function 中的 CORS 设置。":::

### <a name="run-the-device-simulator"></a>使用设备模拟器

在端到端教程必备条件步骤中，[已配置设备模拟器](tutorial-end-to-end.md#configure-and-run-the-simulation)，使数据经由 IoT 中心发送到 Azure 数字孪生实例。

现在，只需启动位于 digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln 的模拟器项目。 如果要使用 Visual Studio，则可以打开该项目，然后使用工具栏中的此按钮运行它：

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Visual Studio 开始按钮的屏幕截图，其中打开了 DeviceSimulator 项目。":::

控制台窗口将打开并显示模拟的温度遥测消息。 这些消息将通过 Azure 数字孪生实例发送，随后在其中由 Azure 函数和 SignalR 选取。

无需在此控制台中执行任何其他操作，只需在完成下一步操作时保持其运行状态。

### <a name="see-the-results"></a>查看结果

若要查看操作结果，请启动 SignalR 集成 Web 应用示例。 可以在 digitaltwins-signalr-webapp-sample-main\src 位置通过运行以下命令从任何控制台窗口执行此操作：

```cmd
npm start
```

运行此命令将打开一个运行示例应用的浏览器窗口，其中显示可视的温度仪表。 应用运行后，应可以开始查看到温度遥测值，这些值从设备模拟器经由 Azure 数字孪生传播，并由 Web 应用实时反映。

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="示例客户端 Web 应用的屏幕截图，其中显示了可视温度仪表。反映的温度为 67.52。":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，请按照以下步骤将其删除。 

利用 Azure Cloud Shell 或本地 Azure CLI，可以使用 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 命令删除资源组中的所有 Azure 资源。 删除资源组也会删除...
* Azure 数字孪生实例（从端到端教程）
* IoT 中心和中心设备注册（从端到端教程）
* 事件网格主题和关联的订阅
* Azure Functions 应用，包括所有三个函数和关联的资源（如存储）
* Azure SignalR 实例

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

最后，删除已下载到本地计算机的项目示例文件夹（digital-twins-samples-master.zip、digitaltwins-signalr-webapp-sample-main.zip，以及它们的解压缩对应项）。

## <a name="next-steps"></a>后续步骤

本文介绍如何使用 SignalR 设置 Azure 函数，将 Azure 数字孪生遥测事件广播到示例客户端应用程序。

接下来，了解有关 Azure SignalR 服务的详细信息：
* [什么是 Azure SignalR 服务？](../azure-signalr/signalr-overview.md)

或者，阅读有关使用 Azure Functions 进行 Azure SignalR 服务身份验证的详细信息：
* [Azure SignalR 服务身份验证](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)