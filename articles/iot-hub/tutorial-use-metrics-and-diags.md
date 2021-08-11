---
title: 教程 - 通过 Azure IoT 中心设置和使用指标与日志
description: 教程 - 了解如何通过 Azure IoT 中心设置和使用指标与日志。 这将提供数据以进行分析，从而帮助诊断中心可能存在的问题。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: beab67a209c177c91a1c4fcfa44934234b6dbc06
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294804"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>教程：通过 IoT 中心设置和使用指标和日志

可以使用 Azure Monitor 为 IoT 中心收集指标和日志，这些指标和日志可以帮助你监视解决方案的操作，并在问题发生时进行故障排除。 在本文中，你将了解如何基于指标创建图表，如何创建触发指标的警报，如何将 IoT 中心操作和错误发送到 Azure Monitor 日志，以及如何检查日志以发现错误。

本教程使用 [.NET Send 遥测快速入门](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)中的 Azure 示例，将消息发送到 IoT 中心。 你可以始终使用设备或其他示例来发送消息，但可能需要相应地修改一些步骤。

在开始本教程之前，熟悉 Azure Monitor 概念可能会有所帮助。 有关详细信息，请参阅[监视 IoT 中心](monitor-iot-hub.md)。 若要了解 IoT 中心发出的指标和资源日志的详细信息，请参阅[监视数据参考](monitor-iot-hub-reference.md)。

将在本教程中执行以下任务：

> [!div class="checklist"]
>
> * 使用 Azure CLI 创建 IoT 中心，注册模拟设备，并创建 Log Analytics 工作区。  
> * 将 IoT 中心连接和设备遥测资源日志发送到 Log Analytics 工作区中的 Azure Monitor 日志。
> * 使用指标资源管理器根据选定的指标创建图表，并将其固定到仪表板。
> * 创建指标警报，使你可在重要条件满足时得到电子邮件通知。
> * 下载并运行一个应用，该应用模拟 IoT 设备将消息发送到 IoT 中心的情形。
> * 在你的条件满足时，查看警报。
> * 查看仪表板上的指标图表。
> * 查看 Azure Monitor 日志中的 IoT 中心错误和操作。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 开发计算机上需要有 .NET Core SDK 2.1 或更高版本。 可以从 [.NET](https://dotnet.microsoft.com/download) 为多个平台下载 .NET Core SDK。

  可以使用以下命令验证开发计算机上 C# 的当前版本：

  ```cmd/sh
  dotnet --version
  ```

- 一个能够接收邮件的电子邮件帐户。

- 确保已在防火墙中打开端口 8883。 本教程中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>设置资源

若要完成本教程，需要 IoT 中心、Log Analytics 工作区和模拟 IoT 设备。 这些资源可以通过 Azure CLI 或 Azure PowerShell 创建。 为所有资源使用相同的资源组和位置。 完成教程后，可以通过删除资源组一步删除所有内容。

下面是必需步骤。

1. 创建[资源组](../azure-resource-manager/management/overview.md)。

2. 创建 IoT 中心。

3. 创建 Log Analytics 工作区。

4. 为发送消息到 IoT 中心的模拟设备注册设备标识。 保存要用于配置模拟设备的设备连接字符串。

### <a name="set-up-resources-using-azure-cli"></a>使用 Azure CLI 设置资源

复制此脚本并将其粘贴到 Cloud Shell。 在已登录的情况下，该脚本会逐行运行。 执行某些命令可能需要一些时间。 新资源在资源组 ContosoResources 中创建。

某些资源的名称在整个 Azure 中必须是唯一的。 脚本使用 `$RANDOM` 函数生成一个随机值，并将其存储在变量中。 对于这些资源，脚本将此随机值追加到资源的基名称，使资源名称唯一。

每个订阅只允许有一个免费的 IoT 中心。 如果订阅中已经有免费的 IoT 中心，请在运行脚本之前将其删除，或者修改脚本以使用免费的 IoT 中心或一个采用标准层或基本层的 IoT 中心。

该脚本打印 IoT 中心的名称、Log Analytics 工作区的名称以及它所注册的设备的连接字符串。 请务必将这些记录下来，因为在本文后面的部分中将需要使用它们。

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>创建设备标识时，可能会出现以下错误：找不到 IoT 中心 ContosoTestHub 的策略 iothubowner 的密钥。  若要修正此错误，请更新 Azure CLI IoT 扩展，然后再次运行脚本中的最后两个命令。 
>
>下面是用于更新扩展的命令。 在 Cloud Shell 实例中运行此命令。
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>收集连接和设备遥测的日志

IoT 中心为几种操作类别发出资源日志；但是，若要查看这些日志，必须创建诊断设置以将其发送到目标。 其中一个目标是 Azure Monitor 日志，这些日志在 Log Analytics 工作区中收集。 IoT 中心资源日志分为不同的类别。 你可以在诊断设置中选择要发送到 Azure Monitor 日志的类别。 在本文中，我们将收集与连接和设备遥测有关的操作和错误的日志。 有关 IoT 中心支持的类别的完整列表，请参阅 [IoT 中心资源日志](monitor-iot-hub-reference.md#resource-logs)。

若要创建诊断设置以将 IoT 中心资源日志发送到 Azure Monitor 日志，请执行以下步骤：

1. 首先，如果还没有进入门户的中心，请选择“资源组”，然后选择资源组 ContosoResources。 从所显示的资源列表中选择你的 IoT 中心。

1. 找到 IoT 中心边栏选项卡中的“监视”部分。  选择“诊断设置”。 然后选择“添加诊断设置”。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="突出显示“监视”部分的“诊断设置”的屏幕截图。":::

1. 在“诊断设置”窗格中，为设置指定一个描述性名称，例如“将连接和遥测发送到日志”。

1. 在“类别详细信息”下，选择“连接”和“设备遥测”  。

1. 在“目标详细信息”下，选择“发送到日志分析”，然后使用 Log Analytics 工作区选取器来选择之前记下的工作区 。 完成后，诊断设置应类似于以下屏幕截图：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="显示最终诊断日志设置的屏幕截图。":::

1. 选择“保存”，保存这些设置。 关闭“诊断设置”窗格。 可以在诊断设置列表中看到你的新设置。

## <a name="set-up-metrics"></a>设置指标

现在，我们将使用指标资源管理器创建一个图表，显示所要跟踪的指标。将此图表固定到 Azure 门户中的默认仪表板。

1. 在 IoT 中心的左窗格中，选择“监视”部分中的“指标” 。

1. 在屏幕顶部，选择“过去 24 小时(自动)”。 在显示的下拉列表中，选择“过去 4 小时”作为“时间范围”，并将“时间粒度”设置为“1 分钟”，然后对“时间显示形式”选择“本地”     。 选择“应用”，保存这些设置。 设置现在应显示“本地时间:过去 4 小时(1 分钟)”。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="显示指标时间设置的屏幕截图。":::

1. 在图表上，显示了一个范围为 IoT 中心的部分指标设置。 保留“范围”和“指标命名空间”值的默认值 。 选择“指标”设置并键入“遥测”，然后从下拉列表中选择“发送的遥测消息数” 。 “聚合”将自动设置为“Sum” 。 请注意，图表的标题也会更改。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="显示向图表添加“发送的遥测消息数”指标的屏幕截图。":::

1. 现在选择“添加指标”，向图表添加另一个指标。 在“指标”下，选择“已使用的消息总数”。   “聚合”将自动设置为“Avg” 。同样，请注意，图表的标题已更改为包含此指标。

   现在，屏幕会显示针对“发送的遥测消息数”的最小化指标，以及针对“已使用的消息总数”的新指标。  

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="显示向图表添加“使用的消息总数”指标的屏幕截图。":::

1. 在图表的右上角，选择“固定到仪表板”。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="突出显示“固定到仪表板”按钮的屏幕截图。":::

1. 在“固定到仪表板”窗格中，选择“现有”选项卡 。选择“专用”，然后从仪表板下拉列表中选择“仪表板” 。 最后，选择“固定”将图表固定到 Azure 门户中的默认仪表板。 如果你不将图表固定到仪表板，则在退出指标资源管理器时，设置不会保留。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="显示“固定到仪表板”设置的屏幕截图。":::

## <a name="set-up-metric-alerts"></a>设置指标警报

现在，我们将设置根据“发送的遥测消息数”和“使用的消息总数”这两个指标触发的警报 。

“发送的遥测消息数”是跟踪消息吞吐量和避免受到限制的良好监视指标。 对于免费层中的 IoT 中心，带宽限制限制为 100 条消息/秒。对于单个设备，我们无法达到这种吞吐量，因此我们将设置在 5 分钟内消息数超过 1000 条时触发的警报。 在生产中，可以根据 IoT 中心的层、版本和单元数量将该信号设置为更重要的值。

“使用的消息总数”跟踪每天使用的消息数。 此指标每天在 00:00 UTC 重置。 如果你超出了每日配额，超过了某个阈值，IoT 中心将不再接受消息。 对于免费层中的 IoT 中心，每日消息配额为 8000。 我们将设置警报，使它在消息总数超过 4000 条（即配额的 50%）时触发。 实际上，你可能会将此百分比设置为更高的值。 每日配额值取决于 IoT 中心的层、版本和单元数。

有关 IoT 中心配额和限制的更多信息，请参阅[配额和限制](iot-hub-devguide-quotas-throttling.md)。

设置指标警报：

1. 在 Azure 门户中转到 IoT 中心。

1. 在“监视”下，选择“警报” 。 然后选择“新建警报规则”。  随即打开“创建警报规则”窗格。

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="显示“创建警报规则”窗格的屏幕截图。":::

    在“创建警报规则”窗格中，有四个部分：

    * “范围”已经设置为 IoT 中心，因此我们将不讨论此部分。
    * “条件”设置触发警报的信号和条件。
    * “操作”配置警报触发时发生的情况。
    * “警报规则详细信息”用于设置警报的名称和说明。

1. 首先，配置触发警报的条件。

    1. 在 **条件** 下，选择 **添加条件**。 在“配置信号逻辑”窗格中，在搜索框中键入“遥测”，然后选择“发送的遥测消息数” 。

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="显示选择指标的屏幕截图。":::

    1. 在“配置信号逻辑”窗格中，在“警报逻辑”下设置或确认以下字段（可以忽略图表） ：

       **阈值**：静态。

       **操作员**：大于。

       **聚合类型**：总计。

       **阈值**：1000。

       **聚合粒度(时间范围)** ：5分钟。

       **评估频率**：每隔 1 分钟

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="显示警报条件设置的屏幕截图。":::

       这些设置将信号设置为合计 5 分钟内的消息数。 此总数将每分钟评估一次，如果前 5 分钟的总计超过 1000 条消息，则将触发警报。

       选择“完成”保存信号逻辑。

1. 现在，配置警报的操作。

    1. 返回 **创建警报规则** 窗格，在 **操作** 下，选择 **添加操作组**。 在“选择要附加到此警报规则的操作组”窗格上，选择“创建操作组” 。

    1. 在“创建操作组”窗格上的“基本信息”选项卡下，为操作组指定一个名称和一个显示名称 。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="显示“创建操作组”窗格的“基本”选项卡的屏幕截图。":::

    1. 选择“通知”选项卡。对于“通知类型”，从下拉列表中选择“电子邮件/短信/推送/语音” 。 随即打开“电子邮件/短信/推送/语音”窗格。

    1. 在“电子邮件/短信/推送/语音”窗格中，选择电子邮件并输入你的电子邮件地址，然后选择“确定” 。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="显示电子邮件地址设置的屏幕截图。":::

    1. 返回“通知”窗格，输入通知的名称。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="显示已完成的“通知”窗格的屏幕截图。":::

    1. （可选）如果选择“操作”选项卡，然后选择“操作类型”下拉列表，则可以看到可以通过警报触发的操作类型 。 对于本文，我们将仅使用通知，因此你可以忽略此选项卡下的设置。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="显示“操作”窗格中可用操作类型的屏幕截图。":::

    1. 选择“查看并创建”选项卡，验证设置，然后选择“创建” 。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="显示“查看并创建”窗格的屏幕截图。":::

    1. 返回“创建警报规则”窗格，请注意，新操作组已添加到警报的操作中。  

1. 最后配置警报规则详细信息并保存警报规则。

    1. 在“创建警报规则”窗格的“警报规则详细信息”下，输入警报的名称和说明；例如，“5 分钟内超过 1000 条消息时发出警报”。 确保选中了“创建时启用警报规则”。 已完成的警报规则将类似于此屏幕截图。

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="显示已完成的“创建警报规则”窗格的屏幕截图。":::

    1. 选择“创建警报规则”，保存新规则。

1. 现在针对“已使用的消息总数”设置另一警报。  如果你希望在使用的消息数接近 IoT 中心的每日配额时（此时，IoT 中心将开始拒绝消息）发送警报，则此指标非常有用。 按照之前所做的步骤操作，但有以下不同之处。

    * 对于“配置信号逻辑”窗格上的信号，选择“使用的消息总数” 。

    * 在“配置信号逻辑”窗格中，设置或确认以下字段（可以忽略图表）：

       **阈值**：静态。

       **操作员**：大于。

       **聚合类型**：最大值。

       **阈值**：4000。

       **聚合粒度(时间范围)** ：1 分钟。

       **评估频率**：每隔 1 分钟

       这些设置将信号设置为当消息数量达到 4000 条时触发。 该指标每分钟计算一次。

    * 为警报规则指定操作时，只需选择先前创建的操作组。

    * 对于警报详细信息，请选择与以前不同的名称和描述。

1. 选择 IoT 中心左侧窗格中“监视”下的“警报” 。 现在，在“警报”窗格顶部的菜单上选择“管理警报规则” 。 此时将打开“规则”窗格。 现在应会看到两个警报：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="显示包含新警报规则的“规则”窗格的屏幕截图。":::

1. 关闭“规则”窗格。

通过这些设置，当 5 分钟内发送的消息数超过 1000 条时，以及当使用的消息总数超过 4000 条（免费层中 IoT 中心每日配额的 50%）时，警报将被触发并且你会收到电子邮件通知。

## <a name="run-the-simulated-device-app"></a>运行模拟设备应用

在[设置资源](#set-up-resources)部分，你注册了一个设备标识，用于模拟使用 IoT 设备。 在本部分，下载一个 .NET 控制台应用（用于模拟向 IoT 中心发送设备到云消息的设备），将其配置为将这些消息发送到 IoT 中心，然后运行它。

> [!IMPORTANT]
>
> IoT 中心最多需要 10 分钟才能完全配置和启用警报。 在配置上一个警报和运行模拟设备应用之间，至少等待 10 分钟。

下载 [IoT 设备模拟](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)的解决方案。 可通过此链接下载包含多个应用程序的存储库；要查找的内容位于 iot-hub/Quickstarts/simulated-device/ 中。

1. 在本地终端窗口中，导航到解决方案的根文件夹。 然后导航到 **iot-hub\Quickstarts\simulated-device** 文件夹。

1. 在所选文本编辑器中打开 SimulatedDevice.cs 文件。

    1. 将 `s_connectionString` 变量的值替换为你运行脚本以设置资源时记下的设备连接字符串。

    1. 在 `SendDeviceToCloudMessagesAsync` 方法中，将 `Task.Delay` 从 1000 更改为 1，这样就会将发送消息的间隔时间从 1 秒更改为 0.001 秒。 缩短此延迟会增加发送的消息数。 （你可能不会获得每秒 100 条消息的消息速率。）

        ```csharp
        await Task.Delay(1);
        ```

    1. 将更改保存到 SimulatedDevice.cs。

1. 在本地终端窗口中，运行以下命令以安装模拟设备应用程序所需的包：

    ```cmd/sh
    dotnet restore
    ```

1. 在本地终端窗口中，运行以下命令，生成并运行模拟设备应用程序：

    ```cmd/sh
    dotnet run
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="显示模拟设备输出的屏幕截图。":::

让应用程序运行至少 10-15 分钟。 理想情况下，让它运行到停止发送消息为止（大约 20-30 分钟）。 如果你超过了 IoT 中心的每日消息配额，并且它已停止接受更多消息时，就会发生这种情况。

> [!NOTE]
> 如果设备应用在停止发送消息后长时间保持运行状态，则可能会出现异常。 你可以安全地忽略此异常并关闭应用窗口。

## <a name="view-metrics-chart-on-your-dashboard"></a>查看仪表板上的指标图表

1. 在 Azure 门户的左上角，打开门户菜单，然后选择“仪表板”。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="如何选择仪表板的屏幕截图。":::

1. 找到先前固定的图表，然后单击图表数据外部磁贴上的任何位置以展开它。 图表上显示发送的遥测消息数和使用的消息总数。 最新的数字显示在图表的底部。 可以在图表中移动光标以查看特定时间的指标值。 你还可以在图表顶部更改时间值和粒度，将数据缩小或扩展到感兴趣的时间段。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="显示指标图表的屏幕截图。":::

   在此场景下，模拟设备的消息吞吐量不足以导致 IoT 中心限制其消息数。 在实际涉及限制的场景中，你可能会看到发送的遥测消息数在有限的时间内超过了 IoT 中心的限制。 这是为了适应突发流量。 有关详细信息，请参阅[流量调整](iot-hub-devguide-quotas-throttling.md#traffic-shaping)。

## <a name="view-the-alerts"></a>查看警报

如果发送的消息数超出了你在警报规则中设置的限制，你会开始收到电子邮件警报。

若要查看是否有任何活动警报，请选择 IoT 中心左侧窗格中“监视”下的“警报” 。 “警报”窗格显示在指定时间范围内按严重性排序的已触发警报数。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="显示警报摘要的屏幕截图。":::

选择严重性为 Sev 3 的行。 “所有警报”窗格打开并列出已触发的 Sev 3 警报。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="显示“所有警报”窗格的屏幕截图。":::

选择其中一个警报以查看警报详细信息。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="显示警报详细信息的屏幕截图。":::

检查收件箱中来自 Microsoft Azure 的电子邮件。 主题行将描述触发的警报。 例如“Azure:*已激活，严重性:3，5 分钟内超过 1000 条消息时发出警报”* 。 正文将与下图类似：

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="电子邮件的屏幕截图，显示警报已触发。":::

## <a name="view-azure-monitor-logs"></a>查看 Azure Monitor 日志

在[收集连接和设备遥测的日志](#collect-logs-for-connections-and-device-telemetry)部分中，你创建了一个诊断设置，将 IoT 中心发出的连接和设备遥测操作资源日志发送到 Azure Monitor 日志。 在本部分中，针对 Azure Monitor 日志运行 Kusto 查询，观察发生的任何错误。

1. 在 Azure 门户中 IoT 中心左窗格的“监视”下，选择“日志” 。 关闭初始的“查询”窗口（如果它打开）。

1. 在“新建查询”窗格中，选择“查询”选项卡，然后展开“IoT 中心”以查看默认查询的列表 。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="IoT 中心默认查询的屏幕截图。":::

1. 选择“错误摘要”查询。 该查询将显示在“查询编辑器”窗格中。 在编辑器窗格中选择“运行”并观察查询结果。 展开其中一行可查看详细信息。

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="“错误摘要”查询返回的日志的屏幕截图。":::

   > [!NOTE]
   > 如果没有看到任何错误，请尝试运行“最近连接的设备”查询。 这应该会针对模拟设备返回一行。

## <a name="clean-up-resources"></a>清理资源

若要删除在本教程中创建的所有资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在此示例下，它会删除 IoT 中心、Log Analytics 工作区和资源组本身。 如果已将指标图表固定到仪表板，则需手动删除这些图表，方法是：单击每个图表右上角的三个点，然后选择“删除”。 删除图表后，请务必保存更改。

若要删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令。

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何执行以下任务，以便使用 IoT 中心指标和日志：

> [!div class="checklist"]
>
> * 使用 Azure CLI 创建 IoT 中心，注册模拟设备，并创建 Log Analytics 工作区。  
> * 将 IoT 中心连接和设备遥测资源日志发送到 Log Analytics 工作区中的 Azure Monitor 日志。
> * 使用指标资源管理器根据选定的指标创建图表，并将其固定到仪表板。
> * 创建指标警报，使你可在重要条件满足时得到电子邮件通知。
> * 下载并运行一个应用，该应用模拟 IoT 设备将消息发送到 IoT 中心的情形。
> * 在你的条件满足时，查看警报。
> * 查看仪表板上的指标图表。
> * 查看 Azure Monitor 日志中的 IoT 中心错误和操作。

转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
> [从后端服务配置设备](tutorial-device-twins.md)