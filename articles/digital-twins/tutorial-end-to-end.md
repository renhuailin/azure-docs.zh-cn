---
title: 教程：连接端到端解决方案
titleSuffix: Azure Digital Twins
description: 扩建由设备数据驱动的端到端 Azure 数字孪生解决方案的教程。
author: baanders
ms.author: baanders
ms.date: 8/23/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 9d19a74dc7bacc996fe328679d9c3e12766bfadf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626183"
---
# <a name="tutorial-build-out-an-end-to-end-solution"></a>教程：扩建端到端解决方案

若要设置由环境中的实时数据驱动的完整端到端解决方案，可以将 Azure 数字孪生实例连接到其他 Azure 服务，以管理设备和数据。

在本教程中，你将...
> [!div class="checklist"]
> * 设置 Azure 数字孪生实例
> * 了解示例建筑方案，并实例化预先编写的组件
> * 使用 [Azure Functions](../azure-functions/functions-overview.md) 应用将模拟的遥测从 [IoT 中心](../iot-hub/about-iot-hub.md)设备路由到数字孪生属性
> * 通过使用 Azure Functions、终结点和路由处理数字孪生通知，通过孪生图传播更改

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>设置 Cloud Shell 会话
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>开始使用建筑方案

本教程中使用的示例项目代表真实的建筑方案，包含楼层、房间和温控设备。 Azure 数字孪生实例将以数字形式表示这些组件，然后连接到 [IoT 中心](../iot-hub/about-iot-hub.md)、[事件网格](../event-grid/overview.md)和两个 [Azure 函数](../azure-functions/functions-overview.md)以实现数据移动。

下面是表示完整方案的关系图。 

首先，你将创建 Azure 数字孪生实例（图中的 A 部分），然后设置传入数字孪生的遥测数据流（箭头 B），再设置通过孪生图进行的数据传播（箭头 C）  。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整建筑方案图，其中显示了通过各种 Azure 服务在设备和 Azure 数字孪生之间流动的数据。":::

为逐步完成此方案，需要与先前下载的预先编写的示例应用的组件进行交互。

下面是建筑方案 AdtSampleApp 示例应用实现的组件：
* 设备身份验证 
* [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 用法示例（参见 CommandLoop.cs）
* 调用 Azure 数字孪生 API 的控制台接口
* SampleClientApp - Azure 数字孪生解决方案示例
* SampleFunctionsApp - Azure Functions 应用，可将 Azure 数字孪生图更新为来自 IoT 中心和 Azure 数字孪生事件的遥测结果

### <a name="instantiate-the-pre-created-twin-graph"></a>实例化预先创建的孪生图

首先，你将使用示例项目中的 AdtSampleApp 解决方案构建端到端方案的 Azure 数字孪生部分（A 部分）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="完整建筑方案图的摘录关系图，其中突出显示了“Azure 数字孪生实例”部分。":::

在打开 AdtE2ESample 项目的 Visual Studio 窗口中，使用工具栏中的此按钮运行该项目：

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio 开始按钮的屏幕截图，其中打开了 SampleClientApp 项目。":::

控制台窗口随即打开，执行身份验证，然后等待命令。 在此控制台中，运行下一个命令来实例化示例 Azure 数字孪生解决方案。

> [!IMPORTANT]
> 如果 Azure 数字孪生实例中已经有数字孪生和关系，运行此命令会将其删除，并替换为示例方案的孪生和关系。

```cmd/sh
SetupBuildingScenario
```

此命令的输出是一系列确认消息，因为 Azure 数字孪生实例中创建和连接了 3 个[数字孪生](concepts-twins-graph.md)：名为 floor1 的楼层、名为 room21 的房间和名为 thermostat67 的温度传感器。 这些数字孪生表示真实环境中可能存在的实体。

它们通过关系连接到以下[孪生图](concepts-twins-graph.md)。 孪生图表示整个环境，包括实体之间的交互和关联方式。

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="显示 floor1 包含 room21 且 room21 包含 thermostat67 的示意图。" border="false":::

通过运行以下命令可以验证孪生是否已创建，这样会查询已连接的 Azure 数字孪生实例中所包含的所有数字孪生：

```cmd/sh
Query
```

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

现在可以停止运行该项目。 不过，请在 Visual Studio 中使解决方案保持打开状态，因为本教程还将继续使用它。

## <a name="set-up-the-sample-function-app"></a>设置示例函数应用

下一步是设置将在本教程中用来处理数据的 [Azure Functions 应用](../azure-functions/functions-overview.md)。 函数应用 SampleFunctionsApp 包含两个函数：
* ProcessHubToDTEvents：处理传入的 IoT 中心数据，并相应地更新 Azure 数字孪生
* ProcessDTRoutedData：处理来自数字孪生的数据，并相应地更新 Azure 数字孪生中的父级孪生

在本部分中，你将发布预先编写的函数应用，并通过向函数应用分配 Azure Active Directory (Azure AD) 标识，确保它可以访问 Azure 数字孪生。 完成这些步骤后，本教程的其余部分即可使用函数应用中的函数。 

返回到打开 AdtE2ESample 项目的 Visual Studio 窗口中，该函数应用位于 SampleFunctionsApp 项目文件中 。 你可以在“解决方案资源管理器”窗格中进行查看。

### <a name="update-dependencies"></a>更新依赖项

在发布应用之前，最好确保依赖项是最新版本，以确保具有所有包含的包的最新版本。

在“解决方案资源管理器”窗格中，展开“SampleFunctionsApp”>“依赖项”。 右键选择“包”，并选择“管理 NuGet 包...” 。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio 的屏幕截图，其中显示了 SampleFunctionsApp 项目的“管理 NuGet 包”菜单按钮。" border="false":::

这样做将打开 NuGet 包管理器。 选择“更新”选项卡，如果有任何要更新的包，请选中此复选框以“选择所有的包” 。 然后选择“更新”。

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio 的屏幕截图，其中显示了如何选择在 NuGet 包管理器中更新所有包。":::

### <a name="publish-the-app"></a>发布应用

若要将函数应用发布到 Azure，首先需要创建一个存储帐户，然后在 Azure 中创建函数应用，最后将函数发布到 Azure 函数应用。 本部分使用 Azure CLI 完成这些操作。

1. 通过运行以下命令来创建 Azure 存储帐户：

    ```azurecli-interactive
    az storage account create --name <name-for-new-storage-account> --location <location> --resource-group <resource-group> --sku Standard_LRS
    ```

1. 通过运行以下命令来创建 Azure 函数应用：

    ```azurecli-interactive
    az functionapp create --name <name-for-new-function-app> --storage-account <name-of-storage-account-from-previous-step> --consumption-plan-location <location> --runtime dotnet --resource-group <resource-group>
    ```

1. 接下来，你将压缩函数并将其发布到新的 Azure 函数应用 。

    1. 在本地计算机上打开一个终端，比如 PowerShell，然后导航到在本教程前面下载的[数字孪生示例存储库](https://github.com/azure-samples/digital-twins-samples/tree/master/)。 在下载的存储库文件夹中，导航到 digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp。
    
    1. 在终端中，运行以下命令以发布项目：

        ```powershell
        dotnet publish -c Release
        ```

        此命令将项目发布到 digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish 目录。

    1. 创建位于 digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp\bin\Release\netcoreapp3.1\publish 目录中的已发布文件的 zip。 
        
        如果使用的是 PowerShell，可以通过将完整路径复制到该 \publish 目录并将其粘贴到以下命令中来创建 zip：
    
        ```powershell
        Compress-Archive -Path <full-path-to-publish-directory>\* -DestinationPath .\publish.zip
        ```

        该 cmdlet 将在终端的目录位置创建一个 publish.zip 文件，其中包含 host.json 文件以及 bin、ProcessDTRoutedData 和 ProcessHubToDTEvents 目录   。

        如果未使用 PowerShell 且无权访问 `Compress-Archive` cmdlet，则需要使用文件资源管理器或其他方法压缩文件。

1. 在 Azure CLI 中，运行以下命令，将已发布和已压缩的函数部署到 Azure 函数应用：

    ```azurecli-interactive
    az functionapp deployment source config-zip --resource-group <resource-group> --name <name-of-your-function-app> --src "<full-path-to-publish.zip>"
    ```

    > [!NOTE]
    > 如果在本地使用 Azure CLI，则可以使用计算机上的 ZIP 文件路径直接访问该文件。
    > 
    >如果使用的是 Azure Cloud Shell，请在运行命令之前使用此按钮将 ZIP 文件上传到 Cloud Shell：
    >
    > :::image type="content" source="media/tutorial-end-to-end/azure-cloud-shell-upload.png" alt-text="Azure Cloud Shell 的屏幕截图，其中侧重展示了如何上传文件。":::
    >
    > 在本例中，该文件将上传到 Cloud Shell 存储的根目录中，以便可以通过命令（如 `--src publish.zip`）的 `--src` 参数名称直接引用该文件。

    一个成功的部署的响应状态代码为 202 并会输出一个包含新函数详细信息的 JSON 对象。 可以通过在结果中查找此字段来确认部署是否成功：

    ```json
    {
      ...
      "provisioningState": "Succeeded",
      ...
    }
    ```

你现在已将函数发布到 Azure 中的函数应用。

接下来，函数应用需要具有访问 Azure 数字孪生实例的正确权限。 你将在下一部分配置此访问权限。

### <a name="configure-permissions-for-the-function-app"></a>为函数应用配置权限

需要为函数应用执行两项设置才能访问 Azure 数字孪生实例，这两项设置都可以使用 Azure CLI 来完成。 

#### <a name="assign-access-role"></a>分配访问角色

第一个设置为函数应用提供 Azure 数字孪生实例中的“Azure 数字孪生数据所有者”角色。 要对实例执行许多数据平面活动的任何用户或函数都需要此角色。 可以在 [Azure 数字孪生解决方案的安全性](concepts-security.md)中阅读有关安全性和角色分配的详细信息。 

1. 使用以下命令查看函数的系统托管标识的详细信息。 记下输出中的 principalId 字段。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > 如果结果为空而不是显示标识详细信息，请使用以下命令为函数创建新的系统托管标识：
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > 然后，输出将显示标识的详细信息，包括下一步所需的 principalId 值。 

1. 在以下命令中使用 principalId 值将函数应用的标识分配给 Azure 数字孪生实例的 Azure 数字孪生数据所有者角色。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

此命令的结果是已创建的角色分配的输出信息。 函数应用现在有权访问 Azure 数字孪生实例中的数据。

#### <a name="configure-application-settings"></a>配置应用程序设置

第二个设置使用 Azure 数字孪生实例的 URL 为函数创建环境变量。 函数代码将使用此变量的值来引用你的实例。 有关环境变量的详细信息，请参阅管理函数应用。 

运行下面的命令，并在占位符中填入资源的详细信息。

```azurecli-interactive
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

输出是 Azure 函数的设置列表，其中现在应包含一个名为 ADT_SERVICE_URL 的条目。


## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>处理来自 IoT 中心设备的模拟遥测数据

Azure 数字孪生图将由真实设备的遥测驱动。 

在此步骤中，需要将在 [IoT 中心](../iot-hub/about-iot-hub.md)注册的模拟温控设备连接到在 Azure 数字孪生中表示它的数字孪生。 当模拟设备发出遥测时，系统将通过可触发数字孪生中相应更新的 ProcessHubToDTEvents Azure 函数定向数据。 通过这种方式，数字孪生就可与真实设备的数据保持一致。 在 Azure 数字孪生中，将事件数据从一个位置定向到另一个位置的过程称为路由事件。

模拟遥测处理过程发生在端到端方案的这一部分（箭头 B）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="完整建筑方案图的摘录关系图，其中突出显示了展示 Azure 数字孪生前的元素的部分。":::

下面是设置此设备连接需要完成的操作：
1. 创建将管理模拟设备的 IoT 中心
2. 设置事件订阅，将 IoT 中心连接到相应的 Azure 函数
3. 在 IoT 中心注册模拟设备
4. 运行模拟设备并生成遥测
5. 查询 Azure 数字孪生以查看实时结果

### <a name="create-an-iot-hub-instance"></a>创建 IoT 中心实例

Azure 数字孪生旨在搭配 [IoT 中心](../iot-hub/about-iot-hub.md)使用，后者是用于管理设备及其数据的 Azure 服务。 在此步骤中，你将设置一个 IoT 中心，用于管理本教程中的示例设备。

在 Azure Cloud Shell 中，使用此命令创建新的 IoT 中心：

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> --resource-group <your-resource-group> --sku S1
```

此命令的输出是有关已创建的 IoT 中心的信息。

保存提供给 IoT 中心的名称。 稍后将使用它。

### <a name="connect-the-iot-hub-to-the-azure-function"></a>将 IoT 中心连接到 Azure 函数

接下来，将 IoT 中心连接到之前在函数应用中发布的 ProcessHubToDTEvents Azure 函数，让数据可以通过函数从设备流入 IoT 中心，这一操作将更新 Azure 数字孪生。

为此，需要在 IoT 中心创建一个事件订阅，并将 Azure 函数作为终结点。 这会将函数“订阅”到 IoT 中心发生的事件。

在 [Azure 门户](https://portal.azure.com/)中，导航到新创建的 IoT 中心，方法是在顶部搜索栏中搜索其名称。 从中心菜单中选择“事件”，然后选择“+ 事件订阅” 。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure 门户的屏幕截图，其中显示了 IoT 中心事件订阅。":::

选择此选项时将显示“创建事件订阅”页。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure 门户的屏幕截图，其中显示了如何创建事件订阅。":::

按下面所示填写字段（默认填充的字段未提及）：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “主题详细信息” > “系统主题名称”：为系统主题提供一个名称。 
* “事件类型” > “筛选事件类型”：从菜单选项中选择“设备遥测”。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* “终结点详细信息”>“终结点”：选择“选择终结点”链接，这将打开“选择 Azure 函数”窗口：:::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure 门户事件订阅的屏幕截图，显示用于选择 Azure 函数的窗口。" border="false":::
    - 填写“订阅”、“资源组”、“函数应用”和“函数”(ProcessHubToDTEvents)   。 选择订阅后，其中一些值可能会自动填充。
    - 选择“确认所选内容”  。

返回“创建事件订阅”页，选择“创建”。 

### <a name="register-the-simulated-device-with-iot-hub"></a>在 IoT 中心注册模拟设备 

本部分在 ID 为 thermostat67 的 IoT 中心内创建设备表示形式。 模拟设备将连接到此表示形式，这是遥测事件从设备进入 IoT 中心的方式。 前面步骤中订阅的 Azure 函数将在 IoT 中心进行侦听、准备选取事件并继续进行处理。

在 Azure Cloud Shell 中，使用以下命令在 IoT 中心创建设备：

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> --resource-group <your-resource-group>
```

输出是已创建设备的相关信息。

### <a name="configure-and-run-the-simulation"></a>配置和运行模拟

接下来，配置设备模拟器以将数据发送到 IoT 中心实例。

首先，使用以下命令获取 IoT 中心连接字符串：

```azurecli-interactive
az iot hub connection-string show --hub-name <your-IoT-hub-name>
```

然后，使用此命令获取设备连接字符串：

```azurecli-interactive
az iot hub device-identity connection-string show --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

将这些值插入本地项目中的设备模拟器代码中，以将模拟器连接到此 IoT 中心和 IoT 中心设备。

在新的 Visual Studio 窗口中，（从下载的解决方案文件夹）打开“设备模拟器”>“DeviceSimulator.sln”。

>[!NOTE]
> 你现在应该有两个 Visual Studio 窗口，一个是 DeviceSimulator.sln，另一个是 AdtE2ESample.sln 。

在这个新 Visual Studio 窗口的“解决方案资源管理器”窗格中，选择“DeviceSimulator/AzureIoTHub.cs”，在编辑窗口中将其打开。 将以下连接字符串值更改为前面收集的值：

```csharp
iotHubConnectionString = <your-hub-connection-string>
deviceConnectionString = <your-device-connection-string>
```

保存文件。

现在，要查看已设置的数据模拟结果，请在工具栏中用此按钮运行 DeviceSimulator 项目：

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio 开始按钮的屏幕截图，其中打开了 DeviceSimulator 项目。":::

控制台窗口将打开并显示模拟的温度遥测消息。 这些消息将发送到 IoT 中心，由 Azure 函数选取并处理。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="设备模拟器的控制台输出的屏幕截图，其中显示了正在发送的温度遥测。":::

你无需此控制台中执行任何其他操作，但在完成后续步骤时，需要让其保持运行状态。

### <a name="see-the-results-in-azure-digital-twins"></a>查看 Azure 数字孪生中的结果

之前发布的 ProcessHubToDTEvents 函数会侦听 IoT 中心数据，并调用 Azure 数字孪生 API 来更新 thermostat67 孪生上的“温度”属性。

要查看 Azure 数字孪生端的数据，请转到打开 AdtE2ESample 项目的 Visual Studio 窗口，并运行该项目。

在打开的项目控制台窗口中运行以下命令，以获取数字孪生 thermostat67 所报告的温度：

```cmd
ObserveProperties thermostat67 Temperature
```

你应该会看到来自 Azure 数字孪生实例的实时更新温度每 2 秒被记录到控制台中一次。

>[!NOTE]
> 可能需要几秒钟才能将设备中的数据传播到孪生体。 在数据开始到达之前，前几个温度读数可能显示为 0。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="控制台输出的屏幕截图，其中显示了来自数字孪生 thermostat67 的温度消息日志。":::

一旦验证实时温度记录成功运行，就可以停止运行这两个项目。 让 Visual Studio 窗口保持打开状态，因为在本教程的其余部分将继续使用它们。

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>通过图形传播 Azure 数字孪生事件

到本教程的此阶段，你已了解如何通过外部设备数据更新 Azure 数字孪生。 接下来，你将了解如何通过 Azure 数字孪生图传播对某个数字孪生的更改，换句话说，就是如何从服务内部数据更新孪生。

为此，在更新已连接的 Thermostat 孪生时，你将使用 ProcessDTRoutedData Azure 函数来更新 Room 孪生。 更新功能发生在端到端方案的这一部分（箭头 C）：

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="完整建筑方案图的摘录关系图，其中突出显示了展示 Azure 数字孪生后的元素的部分。":::

下面是设置此数据流需要完成的操作：
1. [创建事件网格主题](#create-the-event-grid-topic)，以便在 Azure 服务之间移动数据
1. 在 Azure 数字孪生中[创建终结点](#create-the-endpoint)，以便将实例连接到事件网格主题
1. 在 Azure 数字孪生中[设置路由](#create-the-route)，以便将孪生属性更改事件发送到终结点
1. [设置 Azure 函数](#connect-the-azure-function)，以便在终结点上侦听事件网格主题，接收发送到其中的孪生属性更改事件，并相应地更新图中的其他孪生

[!INCLUDE [digital-twins-twin-to-twin-resources.md](../../includes/digital-twins-twin-to-twin-resources.md)]

### <a name="connect-the-azure-function"></a>连接 Azure 函数

接下来，请将 ProcessDTRoutedData Azure 函数订阅到前面创建的事件网格主题，让遥测数据可以通过事件网格主题从 thermostat67 孪生流向该函数，该函数会返回到 Azure 数字孪生并相应地更新 room21。

为此，你将创建一个事件网格订阅，将之前创建的事件网格主题中的数据发送到 ProcessDTRoutedData Azure 函数 。

在 [Azure 门户](https://portal.azure.com/)中，导航到事件网格主题，方法是在顶部搜索栏中搜索其名称。 选择“+ 事件订阅”。

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure 门户的屏幕截图，其中显示了如何创建事件网格事件订阅。":::

创建此事件订阅的步骤与之前在本教程中将第一个 Azure 函数订阅到 IoT 中心的步骤类似。 这次不需要将设备遥测指定为要侦听的事件类型，而且会连接到其他 Azure 函数。

在“创建事件订阅”页中，按下面所示填写字段（默认填充的字段未提及）：
* “事件订阅详细信息” > “名称”：为事件订阅指定名称。
* “终结点详细信息” > “终结点类型”：从菜单选项中选择“Azure 函数”。
* “终结点详细信息” > “终结点”：选择“选择终结点”链接，这将打开“选择 Azure 函数”窗口：
    - 填写“订阅”、“资源组”、“函数应用”和“函数”(ProcessDTRoutedData)   。 选择订阅后，其中一些值可能会自动填充。
    - 选择“确认所选内容”  。

返回“创建事件订阅”页，选择“创建”。 

## <a name="run-the-simulation-and-see-the-results"></a>运行模拟并查看结果

现在，事件应该能够从模拟设备流入 Azure 数字孪生，并通过 Azure 数字孪生图适当地更新孪生。 在本部分中，你将再次运行设备模拟器以启动已设置的完整事件流，并查询 Azure 数字孪生以查看实时结果

转到打开 DeviceSimulator 项目的 Visual Studio 窗口，然后运行该项目。

与之前运行设备模拟器一样，控制台窗口将打开并显示模拟温度遥测消息。 这些事件将经过之前设置的流来更新 thermostat67 孪生，然后经过最近设置的流来更新 room21 孪生以进行匹配 。

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="设备模拟器的控制台输出的屏幕截图，其中显示了正在发送的温度遥测。":::

你无需此控制台中执行任何其他操作，但在完成后续步骤时，需要让其保持运行状态。

若要查看 Azure 数字孪生端的数据，请转到打开 AdtE2ESample 项目的 Visual Studio 窗口，并运行该项目。

在打开的项目控制台窗口中运行以下命令，以获取 thermostat67 和 room21 这两个数字孪生所报告的温度。

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

你应该会看到来自 Azure 数字孪生实例的实时更新温度每 2 秒被记录到控制台中一次。 注意，room21 的温度正在更新，以匹配 thermostat67 的更新 。

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="控制台输出的屏幕截图，其中显示了来自温控设备和房间的温度消息日志。":::

一旦验证实例的实时温度记录成功运行，就可以停止运行这两个项目。 你也可以关闭 Visual Studio 窗口，因为本教程现已完成。

## <a name="review"></a>审阅

下面回顾了在本教程中扩建的方案。

1. Azure 数字孪生实例以数字方式表示楼层、房间和温控设备（如下图中 A 部分所示）
2. 模拟设备遥测发送到 IoT 中心，其中 ProcessHubToDTEvents Azure 函数正在侦听遥测事件。 ProcessHubToDTEvents Azure 函数使用这些事件中的信息来设置 thermostat67（图中箭头 B）的“温度”属性。
3. Azure 数字孪生的属性更改事件会路由到事件网格主题，其中 ProcessDTRoutedData Azure 函数正在侦听事件。 ProcessDTRoutedData Azure 函数使用这些事件中的信息来设置 room21（图中箭头 C）的“温度”属性。

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="完整建筑方案图，其中显示了通过各种 Azure 服务在设备和 Azure 数字孪生之间流动的数据。":::

## <a name="clean-up-resources"></a>清理资源

完成本教程后，可以选择要删除的资源，具体取决于接下来要执行的操作。

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

* 如果要继续使用本文中设置的 Azure 数字孪生实例，但想要清除其部分或全部模型、孪生和关系，可在 [Azure Cloud Shell](https://shell.azure.com) 窗口中使用 CLI 命令 [az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 来删除要移除的元素。

    此选项不会删除在本教程中创建的任何其他 Azure 资源（IoT 中心、Azure Functions 应用等）。 可以使用适用于每种资源类型的 [dt 命令](/cli/azure/reference-index?view=azure-cli-latest&preserve-view=true)分别删除这些资源。

你可能还需要从本地计算机中删除项目文件夹。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个端到端方案，它显示了由实时设备数据驱动的 Azure 数字孪生。

接下来，请开始查看概念文档，详细了解本教程中所用的元素：

> [!div class="nextstepaction"]
> [自定义模式](concepts-models.md)