---
title: 关于在 Windows 上创建 Azure IoT Edge 设备的快速入门 | Microsoft Docs
description: 本快速入门介绍如何创建 IoT Edge 设备，然后从 Azure 门户远程部署预生成的代码。
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 71e38059aceb7da63f3545610b9acfe48c5d3150
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663207"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>快速入门：将第一个 IoT Edge 模块部署到 Windows 设备（预览版）

在本快速入门中通过将容器化代码部署到 Linux on Windows IoT Edge 设备来试用 Azure IoT Edge。 IoT Edge 允许你远程管理设备上的代码，这样你就可以将更多工作负荷发送到 Edge。 对于本快速入门，我们建议你使用自己的设备，看看使用 Azure IoT Edge for Linux on Windows 有多么简单。

此快速入门介绍如何：

* 创建 IoT 中心。
* 将 IoT Edge 设备注册到 IoT 中心。
* 在设备上安装并运行 IoT Edge for Linux on Windows 运行时。
* 以远程方式将模块部署到 IoT Edge 设备并发送遥测数据。

![关系图 - 设备和云架构的快速入门](./media/quickstart/install-edge-full.png)

本快速入门将逐步介绍如何设置 Azure IoT Edge for Linux on Windows 设备。 然后，将模块从 Azure 门户部署到设备。 在本快速入门中所使用的模块为模拟传感器，可以生成温度、湿度和压强数据。 其他 Azure IoT Edge 教程均以本教程中通过部署额外模块（这些模块通过分析模拟数据来获得业务见解）所执行的操作为基础。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free)。

>[!NOTE]
>IoT Edge for Linux on Windows 现提供[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

云资源：

* 一个资源组，用于管理在本快速入门中使用的所有资源。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge 设备：

* 设备需要是 1809 或更高版本的 Windows 电脑或服务器
* 至少 4 GB 内存，建议 8 GB 内存
* 10 GB 可用磁盘空间

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建 IoT 中心，启动快速入门。

![关系图 - 在云中创建 IoT 中心](./media/quickstart/create-iot-hub.png)

免费级的 IoT 中心适用于此快速入门。 如果曾经用过 IoT 中心并且创建了一个中心，则可使用该 IoT 中心。

以下代码将在资源组 `IoTEdgeResources` 中创建免费的 F1 中心。 将 `{hub_name}` 替换为 IoT 中心的唯一名称。 创建 IoT 中心可能需要数分钟的时间。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   如果由于订阅中已经有一个免费的中心而出现错误，请将 SKU 更改为 **S1**。 如果出现一条错误，指示 IoT 中心名称不可用，则表明他人已使用具有该名称的中心。 请尝试一个新名称。

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用新创建的 IoT 中心注册 IoT Edge 设备。

![关系图 - 使用 IoT 中心标识注册设备](./media/quickstart/register-device.png)

为模拟设备创建设备标识，以便它可以与 IoT 中心通信。 设备标识存在于云中，而将物理设备关联到设备标识时，则使用唯一的设备连接字符串。

由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请使用 `--edge-enabled` 标志声明此标识，使之用于 IoT Edge 设备。

1. 在 Azure Cloud Shell 中输入以下命令，以便在中心创建名为“myEdgeDevice”的设备。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   如果收到有关 iothubowner 策略密钥的错误，请确保 Cloud Shell 正在运行最新版 azure-iot 扩展。

2. 查看设备的连接字符串，该字符串将物理设备与其在 IoT 中心的标识链接在一起。 它包含 IoT 中心名称、设备名称，以及用于对这两者之间的连接进行身份验证的共享密钥。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. 复制 JSON 输出中 `connectionString` 键的值并保存。 该值为设备连接字符串。 在下一部分配置 IoT Edge 运行时中，将用到此连接字符串。

   ![从 CLI 输出中检索连接字符串](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>安装和启动 IoT Edge 运行时

在设备上安装 IoT Edge for Linux on Windows，并使用设备连接字符串对其进行配置。

![关系图 - 在设备上启动 IoT Edge 运行时](./media/quickstart/start-runtime.png)

1. [下载 Windows Admin Center](https://aka.ms/WACDownloadEFLOW)。

1. 按照安装向导在设备上设置 Windows Admin Center。

1. 进入 Windows Admin Center 后，在屏幕的右上方，选择“设置”齿轮图标

1. 从“设置”菜单的“网关”下，选择“扩展”

1. 选择“源”选项卡，然后选择“添加” 。

1. 在文本框中输入 https://aka.ms/wac-insiders-feed ，然后选择“添加”。

1. 添加源后，导航到“可用扩展”选项卡。系统可能需要一段时间来更新扩展列表。

1. 从“可用扩展”列表中选择“Azure IoT Edge” 

1. 安装该扩展

1. 安装扩展后，在屏幕的左上角选择“Windows Admin Center”，导航到主仪表板页面。

1. 你将看到代表正在运行 Windows Admin Center 的电脑的本地主机连接。

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="屏幕截图 - Windows Admin 起始页":::

1. 选择 **添加** 。

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="屏幕截图 - Windows Admin 起始页的“添加”按钮":::

1. 找到 Azure IoT Edge 磁贴，然后选择“新建”。 随即将启动安装向导。

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="屏幕截图 - Azure IoT Edge For Linux on Windows 磁贴":::

1. 继续完成安装向导，接受 EULA，然后选择“下一步”

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="屏幕截图 - 向导中的“欢迎使用”":::

1. 选择“可选的诊断数据”以提供扩展诊断数据，帮助 Microsoft 监视和维护服务质量，然后单击“下一步: 部署”

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="屏幕截图 - 诊断数据":::

1. 在“选择目标设备”屏幕中，选择所需的目标设备，验证它是否满足最低要求。 在本快速入门中，我们要将 IoT Edge 安装在本地设备上，因此选择 localhost 连接。 确认后，选择“下一步”继续

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="屏幕截图 - 选择目标设备":::

1. 通过选择“下一步”，接受默认设置。

1. 部署屏幕显示下载包、安装包、配置主机和最终设置 Linux VM 的进程。  如果部署成功，将看到以下内容：

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="屏幕截图 - 向导中的“部署成功”":::

1. 单击“下一步:连接”，继续执行最后一个步骤 - 使用 IoT 中心实例中的设备 ID 预配 Azure IoT Edge 设备。

1. 从 Azure IoT 中心内的设备复制连接字符串，将其粘贴到设备连接字符串字段中。 然后选择“使用所选方法进行预配”。

    > [!NOTE]
    > 请参阅上一部分中的步骤 3“[注册 IoT Edge 设备](#register-an-iot-edge-device)”来检索连接字符串。

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="屏幕截图 - 向导中的“预配”":::

1. 完成预配后，选择“完成”，即可完成并返回到 Windows Admin Center 起始屏幕。 你现在应该可以看到你的设备被列为 IoT Edge 设备。

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="屏幕截图 - Windows Admin Center Azure IoT Edge 设备":::

1. 选择你的 Azure IoT Edge 设备，可查看其仪表板。 你应该会看到 Azure IoT 中心内设备孪生的工作负载已经部署。 “IoT Edge 模块列表”应显示一个正在运行的模块 edgeAgent，而“IoT Edge 状态”应显示“活动(正在运行)”   。

IoT Edge 设备现在已配置好。 它可以运行云部署型模块了。

## <a name="deploy-a-module"></a>部署模块

从云端管理 Azure IoT Edge 设备，部署将遥测数据发送到 IoT 中心的模块。

![关系图 - 将模块从云部署到设备](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户部署 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。

在这种情况下，推送的模块会生成示例环境数据，你可以在稍后使用该数据进行测试。 模拟传感器正在监视一台计算机和该计算机周围的环境。 例如，该传感器可能位于服务器机房中、工厂地板上或风力涡轮机上。 该消息包括环境温度和湿度、机器温度和压力以及时间戳。 IoT Edge 教程使用此模块创建的数据作为测试数据进行分析。

导航到 Windows Admin Center 中的命令外壳，确认从云中部署的模块正在 IoT Edge 设备上运行。

1. 连接到新建的 IoT Edge 设备

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="屏幕截图 - 连接设备":::

2. 在“概述”页，你将看到“IoT Edge 模块列表”和“IoT Edge 状态”，你可以在此处查看已部署的各种模块以及设备状态  。  

3. 在“工具”下，选择“命令外壳” 。 命令外壳是一种 PowerShell 终端，它自动使用 ssh（安全外壳）连接到 Windows 电脑上 Azure IoT Edge 设备的 Linux VM。

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="屏幕截图 - 命令外壳":::

4. 若要在设备上验证这三个模块，请运行以下 bash 命令：

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="屏幕截图 - 命令外壳列表":::

5. 查看从温度传感器模块发送到云的消息。

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >引用模块名称时，IoT Edge 命令区分大小写。

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="屏幕截图 - 温度传感器":::

也可使用 [Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看到达 IoT 中心的消息。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 IoT Edge 教程，可以使用在本快速入门中注册并设置的设备。 否则，可删除已创建的 Azure 资源，避免产生费用。

如果是在新资源组中创建的虚拟机和 IoT 中心，则可删除该组以及所有关联的资源。 仔细检查资源组的内容，确保没有要保留的内容。 如果不希望删除整个组，则可改为删除单个资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。

删除 **IoTEdgeResources** 组。 可能需要花费几分钟来删除资源组。

```azurecli-interactive
az group delete --name IoTEdgeResources
```

可以通过查看资源组列表来确认已删除该资源组。

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>干净清除 Azure IoT Edge for Linux on Windows

你可以通过 Windows Admin Center 中的仪表板扩展从 IoT Edge 设备上卸载 Azure IoT Edge for Linux on Windows。

1. 在 Windows Admin Center 中连接到 Azure IoT Edge for Linux on Windows 设备连接。 此时将加载 Azure 仪表板工具扩展。
2. 选择“卸载”。 删除 Azure IoT Edge for Linux on Windows 后，Windows Admin Center 将导航到起始页，并从列表中删除 Azure IoT Edge 设备连接项。

从 Windows 系统中删除 Azure IoT Edge 的另一种方法是：在 IoT Edge 设备上，转到“开始” > “设置” > “应用” > “Azure IoT Edge” > “卸载”    。 这样将从 IoT Edge 设备中删除 Azure IoT Edge，但会保留 Windows Admin Center 中的连接。 也可从“设置”菜单卸载 Windows Admin Center。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 IoT Edge 设备并使用 Azure IoT Edge 云接口将代码部署到该设备上。 现在，你有了一个可以生成其环境的原始数据的测试设备。

下一步是设置本地开发环境，使你可以开始创建运行业务逻辑的 IoT Edge 模块。

> [!div class="nextstepaction"]
> [开始开发 IoT Edge 模块](tutorial-develop-for-linux.md)
