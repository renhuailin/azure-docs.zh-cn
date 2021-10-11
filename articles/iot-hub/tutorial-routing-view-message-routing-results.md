---
title: 教程 - 查看 Azure IoT 中心消息路由结果 (.NET) | Microsoft Docs
description: 教程 - 在使用本教程的第 1 部分设置了所有资源之后，添加将消息路由到 Azure 流分析的功能并在 Power BI 中查看结果。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/21/2021
ms.author: robinsh
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: e055868c04056170f351c3534f065d842f833691
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129457371"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>教程：第 2 部分 - 查看路由的消息

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>消息路由规则

下面是消息路由规则，这些规则是在本教程的第 1 部分设置的，本文第 2 部分将演示其工作方式。

|值 |结果|
|------|------|
|级别=“storage” |写入到 Azure 存储。|
|级别=“critical” |写入服务总线队列。 逻辑应用从队列检索消息 
 并使用 Office 365 通过电子邮件发送该消息。|
|default |使用 Power BI 显示此数据。|

现在，创建要将消息路由到的资源，运行某个应用以将消息发送到中心，然后查看路由的运作方式。

## <a name="create-a-logic-app"></a>创建逻辑应用  

服务总线队列将用于检索指定为关键的消息。 设置一个逻辑应用，用于监视服务总线队列，并在消息添加到队列时发送电子邮件。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“+ 创建资源”。  在搜索框中输入“逻辑应用”，然后选择 Enter。 在显示的搜索结果中选择“逻辑应用”，然后选择“创建”进入“创建逻辑应用”窗格   。 填充字段。

   **订阅**：选择 Azure 订阅。

   资源组：在“资源组”字段下，选择“新建” 。 指定 ContosoResources 作为资源组的名称。 

   实例详细信息
   类型：选择“消耗”作为实例类型  。 

   逻辑应用名称：指定逻辑应用的名称。 本教程使用 ContosoLogicApp  。 

   区域：使用最近的数据中心的位置。 本教程使用“美国西部”  。

   启用 Log Analytics：将此切换按钮设置为不启用 Log Analytics。 

   ![“创建逻辑应用”屏幕](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   选择“查看 + 创建”  。 可能需要花费几分钟时间才能部署应用。 完成后，会显示一个屏幕，提供部署概述。 

2. 转到逻辑应用。 如果仍在部署页上，可选择“转到资源”。 要转到逻辑应用，最简单的方法是选择“资源组”，选择你的资源组（本教程使用 ContosoResources），然后从资源列表中选择逻辑应用 。 

    向下滚动，直到看见几乎为空的显示“空白逻辑应用 +”的磁贴，然后选择该磁贴。 屏幕上的默认选项卡是“为你提供”。 如果此窗格为空，请选择“全部”以查看可用的连接器和触发器。

3. 从连接器列表中选择“服务总线”。 

   ![连接器列表](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. 此屏幕截图显示了触发器列表。 选择显示“在队列中收到消息时(自动完成)”的选项。

   ![触发器列表](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. 在下一屏幕的字段中填写连接信息。

   连接名称：ContosoConnection
   
   选择“服务总线”命名空间。 本教程使用 ContosoSBNamespace  。 检索并加载密钥名称 (RootManageSharedAccessKey) 和权限（侦听、管理、发送）。 选择“RootManageSharedAccessKey”。 “创建”按钮变为蓝色（活动状态）。 选择该按钮；此时会显示队列选择屏幕。  

6. 接下来，系统会要求提供队列信息。  

   ![选择队列](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

   队列名称：此字段是发送消息的队列的名称。 单击此下拉列表，并选择在安装步骤中设置的队列名称。 本教程使用 contososbqueue  。

   队列类型：队列的类型。 从下拉列表中选择“主要”。

   对其他字段使用默认值。 选择“保存”，保存逻辑应用设计器配置。

7. 选择“+ 新步骤”。 此时会显示“选择操作”窗格。 选择“Office 365 Outlook”。 在列表中找到并选择“发送电子邮件(V2)”。 登录到 Office 365 帐户。   

8. 填写通过电子邮件发送队列中的消息时要使用的字段。 

   ![选择通过一个 Outlook 连接器发送电子邮件](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png) 

   收件人：输入要将警告发送到的电子邮件地址。

   主题：填写电子邮件的主题。

   正文：为正文填写一些文本。 单击“添加动态内容”，这将显示电子邮件中可选择包括的字段。 如果看不到任何选项，请选择“查看更多”来查看更多选项。 选择“内容”，在错误消息中显示电子邮件中的正文。

9. 单击“保存”以保存更改。 关闭逻辑应用设计器。 

## <a name="set-up-azure-stream-analytics"></a>设置 Azure 流分析

若要在 Power BI 可视化中查看数据，首先需要设置流分析作业来检索数据。 请记住，只会将“级别”为“常规”的消息发送到默认终结点，并由针对 Power BI 可视化的流分析作业检索   。

### <a name="create-the-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com)的搜索框中输入“流分析作业”，然后选择 Enter   。 选择“创建”以转到“流分析作业”屏幕，然后再次选择“创建”来转到创建屏幕 。 

2. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。 本教程使用 contosoJob  。

   **订阅**：本教程使用的 Azure 订阅。

   **资源组**：使用 IoT 中心所用的同一资源组。 本教程使用 ContosoResources  。

   **位置**：使用设置脚本中所用的同一位置。 本教程使用“美国西部”  。

   ![创建流分析作业](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. 选择“创建”  来创建作业。 可能需要几分钟时间才能完成部署。

    若要返回作业，请选择“转到资源”。 还可选择“资源组”。 本教程使用 ContosoResources  。 选择该资源组，然后在资源列表中选择流分析作业。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 在“作业拓扑”下选择“输入”。  

2. 在“输入”窗格中，依次选择“添加流输入”、“IoT 中心”   。 在出现的屏幕上，填写以下字段：

   **输入别名**：本教程使用 contosoinputs  。

   选择“从订阅中选择 IoT 中心”，然后在下拉列表中选择你的订阅。
   
   **IoT 中心**：选择 IoT 中心。 本教程使用 ContosoTestHub  。

   **使用者组**：选择在本教程的第 1 部分中设置的使用者组。 本教程使用 contosoconsumers  。

   **共享访问策略名称**：选择“服务”  。 门户将填充共享访问策略密钥。

   **终结点**：选择“消息传送”  。 （如果选择操作监视，将获得有关 IoT 中心的遥测数据，而不是正在发送的数据。） 

   其余字段接受默认值。 

   ![设置流分析作业的输入](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. 选择“保存”。 

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。  

2. 在“输出”窗格中，依次选择“添加”、“Power BI”    。 在出现的屏幕上，填写以下字段：

   **输出别名**：输出的唯一别名。 本教程使用 contosooutputs  。 

   选择“从订阅中选择组工作区”。 在“组工作区”中指定“我的工作区” 。

   **身份验证模式**：选择“用户令牌”。 

   **数据集名称**：要在 Power BI 中使用的数据集的名称。 本教程使用 contosodataset  。 

   **表名称**：要在 Power BI 中使用的表的名称。 本教程使用 contosotable  。

3. 选择“授权”并登录到 Power BI 帐户  。 （登录可能需要多次重试）。

   ![设置流分析作业的输出](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. 选择“保存”。 

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下选择“查询”。  

2. 将 `[YourInputAlias]` 替换为作业的输入别名。 本教程使用 contosoinputs  。

3. 将 `[YourOutputAlias]` 替换为作业的输出别名。 本教程使用 contosooutputs  。

   ![设置流分析作业的查询](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. 选择“保存”。 

5. 关闭“查询”窗格。 随后将返回到资源组中资源的视图。 选择流分析作业。 本教程中将其称为 **contosoJob**。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，单击“启动”   > “立即”   >   “启动”。 成功启动作业后，作业状态将从“已停止”  更改为“正在运行”  。

设置 Power BI 报表需要数据，因此将先创建设备并运行设备模拟应用程序来生成一些数据，再设置 Power BI。

## <a name="run-simulated-device-app"></a>运行模拟设备应用

在本教程的第 1 部分，我们已设置一个使用 IoT 设备进行模拟的设备。 如果尚未下载，请勿下载用于模拟设备向 IoT 中心发送设备到云的消息的 .NET 控制台应用，你将在此处下载它。

此应用程序针对每个不同的消息路由方法发送消息。 下载内容还带有一个文件夹，其中包含完整的 Azure 资源管理器模板和参数文件，以及 Azure CLI 和 PowerShell 脚本。

如果在本教程的第 1 部分中未从存储库下载这些文件，现在请继续从 [IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)下载。 选择此链接可下载包含多个应用程序的存储库；要查找的解决方案是 iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln。 

双击解决方案文件 (IoT_SimulatedDevice.sln) 以在 Visual Studio 中打开代码，然后打开 Program.cs。 使用 IoT 中心主机名代替 `{your hub name}`。 IoT 中心主机名的格式为“{iot-hub-name}.azure-devices.net”  。 本教程的中心主机名为“ContosoTestHub.azure-devices.net”  。 接下来，使用之前设置模拟设备时保存的设备密钥代替 `{your device key}`。 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>运行和测试

运行控制台应用程序。 稍等几分钟。 可在应用程序的控制台屏幕上看到正在发送的消息。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它随机分配 `critical` 级别或 `storage` 级别，使消息路由到存储帐户或路由到服务总线队列（这会触发逻辑应用发送电子邮件）。 默认 (`normal`) 读取内容会在 BI 报表中显示。 

如果所有内容都正确设置，则此时应可以看见以下结果：

1. 开始收到关键消息相关的电子邮件。

   ![生成的电子邮件](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   此结果表示以下语句为 true。 

   * 到服务总线队列的路由运行正常。
   * 从服务总线队列检索消息的逻辑应用运行正常。
   * 连接到 Outlook 的逻辑应用连接器工作正常。 

2. 在 [Azure 门户](https://portal.azure.com)中选择“资源组”，然后选择你的资源组  。 本教程使用 ContosoResources  。 

    依次选择存储帐户、“容器”和存储结果的容器。 本教程使用 contosoresults  。 现在应该可以看见一个文件夹，可继续深入查看目录，直到看见一个或多个文件。 打开其中某个文件；这些文件中包含路由到存储帐户的条目。 

   ![存储中的结果文件](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

此结果表示以下语句为 true。

   * 到存储帐户的路由运行正常。

在应用程序仍在运行的情况下，设置 Power BI 可视化以显示来自默认终结点的消息。

## <a name="set-up-the-power-bi-visualizations"></a>设置 Power BI 可视化效果

1. 登录到 [Power BI](https://powerbi.microsoft.com/) 帐户。

2. 选择“我的工作区”。 至少会显示一个已创建的数据集。 如果未显示任何内容，请再运行模拟设备应用程序 5-10 分钟以流式传输更多数据。 工作区显示后，会显示一个名为 ContosoDataset 的数据集。 右键单击该数据集名称右侧的三个垂直点。 在下拉列表中，选择“创建报表”。

     ![Power BI 创建报表](./media/tutorial-routing-view-message-routing-results/bi-personal-workspace.png)

3. 查看右侧的“可视化”部分，然后选择“折线图”，在 BI 报表页中选择添加折线图 。 拖动图形，使其水平填充空间。 现在，在右侧的“字段”部分打开 ContosoTable。 选择“EventEnqueuedUtcTime”。 将它放在 X 轴上。 选择“温度”并将它拖到温度的“值”字段中 。 这会将温度添加到图表中。 应会得到如下所示的图：

     ![Power BI 温度图](./media/tutorial-routing-view-message-routing-results/bi-temperature-chart.png)

4. 单击图表区域的下半部分。 再次选择“折线图”。 这会在第一个图表下创建一个图表。

5. 在表中，选择“EventQueuedTime”，将其放在“轴”字段中。 将“湿度”拖到“值”字段中。 现在，你可以看到两个图表。

     ![包含两个字段的 Power BI 图](./media/tutorial-routing-view-message-routing-results/bi-chart-temp-humidity.png)

   你从 IoT 中心的默认终结点将消息发送到了 Azure 流分析。 然后添加了 Power BI 报表来显示数据，并添加了两个图表来分别表示温度和湿度。 

7. 选择“文件”>“保存”以保存报表，如果出现提示，请输入报表的名称。 在工作区中保存报表。

现在两个图表上都能看到数据。 此结果表示以下语句为 true：

   * 到默认终结点的路由运行正常。
   * Azure 流分析作业的流式传输正常。
   * Power BI 可视化设置正确。

选择 Power BI 窗口顶部的“刷新”按钮可刷新图表，以查看最近的数据。 

## <a name="clean-up-resources"></a>清理资源 

若要删除在本教程的两个部分中创建的所有 Azure 资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在这种情况下，它会删除 IoT 中心、服务总线命名空间和队列、逻辑应用、存储帐户和资源组本身。 还可以删除 Power BI 资源并清理使用教程期间发送的电子邮件。

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>清理 Power BI 可视化效果中的资源

登录到 [Power BI](https://powerbi.microsoft.com/) 帐户。 转到你的工作区。 本教程使用“我的工作区”  。 若要删除 Power BI 可视化效果，请转到“数据集”并选择垃圾桶图标来删除相应的数据集。 本教程使用 contosodataset  。 删除数据集时，报表也随之删除。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>使用 PowerShell 清理资源

若要删除资源组，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令。 在本教程的开头，`$resourceGroup` 已设置为 **ContosoResources**。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>清理测试电子邮件

你可能还想要在运行设备应用程序的同时删除收件箱中通过逻辑应用生成的电子邮件数量。

## <a name="next-steps"></a>后续步骤

在由两部分组成的本教程中，你学习了如何通过执行以下任务，使用消息路由将 IoT 中心消息路由到不同的目标。  

**第 I 部分：创建资源并设置消息路由**
> [!div class="checklist"]
> * 创建资源 - IoT 中心、存储帐户、服务总线队列和模拟设备。
> * 在 IoT 中心为存储帐户和服务总线队列配置终结点和消息路由。

**第 II 部分：将消息发送到中心并查看路由的结果**
> [!div class="checklist"]
> * 创建一个逻辑应用，该应用将在消息添加到服务总线队列时触发，并发送电子邮件。
> * 下载并运行应用，该应用模拟 IoT 设备将消息发送到中心，以获得不同的路由选择。
>
> * 为发送至默认终结点的数据创建 Power BI 可视化。
>
> * 查看结果...
> * ...在服务总线队列和电子邮件中。
> * ...在存储帐户中。
> * ...在 Power BI 可视化中。


转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
> [通过 IoT 中心设置和使用指标和诊断](tutorial-use-metrics-and-diags.md)
