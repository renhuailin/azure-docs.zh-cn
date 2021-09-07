---
title: 快速入门：在 Linux 上创建 Azure IoT Edge 设备 | Microsoft Docs
description: 本快速入门介绍如何在 Linux 上创建 IoT Edge 设备，然后从 Azure 门户远程部署预生成的代码。
author: kgremban
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d06a4af7d9280eb3bab076ff103968d94fd38839
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215403"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-virtual-linux-device"></a>快速入门：将第一个 IoT Edge 模块部署到虚拟 Linux 设备

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

本快速入门通过将容器化代码部署到虚拟 Linux IoT Edge 设备来测试 Azure IoT Edge。 IoT Edge 允许你远程管理设备上的代码，这样你就可以将更多工作负荷发送到 Edge。 对于本快速入门，我们建议使用 Azure 虚拟机作为 IoT Edge 设备，这样可以快速创建测试计算机，并且可以在完成后将其删除。

此快速入门介绍如何：

* 创建 IoT 中心。
* 将 IoT Edge 设备注册到 IoT 中心。
* 在虚拟设备上安装并启动 IoT Edge 运行时。
* 以远程方式将模块部署到 IoT Edge 设备。

![关系图 - 设备和云架构的快速入门](./media/quickstart-linux/install-edge-full.png)

本快速入门指导你创建配置为 IoT Edge 设备的 Linux 虚拟机。 然后，将模块从 Azure 门户部署到设备。 在本快速入门中所使用的模块为模拟传感器，可以生成温度、湿度和压强数据。 其他 Azure IoT Edge 教程均以本教程中通过部署额外模块（这些模块通过分析模拟数据来获得业务见解）所执行的操作为基础。

如果没有可用的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

云资源：

* 一个资源组，用于管理在本快速入门中使用的所有资源。 在本快速入门和后续教程中，我们使用示例资源组名称 IoTEdgeResources。

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

使用 Azure CLI 创建 IoT 中心，启动快速入门。

![关系图 - 在云中创建 IoT 中心](./media/quickstart-linux/create-iot-hub.png)

免费级的 IoT 中心适用于此快速入门。 如果曾经用过 IoT 中心并且创建了一个中心，则可使用该 IoT 中心。

以下代码在资源组“IoTEdgeResources”中创建免费的“F1”中心 。 将 `{hub_name}` 替换为 IoT 中心的唯一名称。 创建 IoT 中心可能需要数分钟的时间。

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   如果由于订阅中已经有一个免费的中心而出现错误，请将 SKU 更改为 **S1**。 每个订阅仅能有一个免费 IoT 中心。 如果出现一条错误，指示 IoT 中心名称不可用，则表明他人已使用具有该名称的中心。 请尝试一个新名称。

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

使用新创建的 IoT 中心注册 IoT Edge 设备。

![关系图 - 使用 IoT 中心标识注册设备](./media/quickstart-linux/register-device.png)

为 IoT Edge 设备创建设备标识，以便它可以与 IoT 中心通信。 设备标识存在于云中，而将物理设备关联到设备标识时，则使用唯一的设备连接字符串。

由于 IoT Edge 设备的行为和托管方式与典型 IoT 设备不同，请使用 `--edge-enabled` 标志声明此标识，使之用于 IoT Edge 设备。

1. 在 Azure Cloud Shell 中输入以下命令，以便在中心创建名为“myEdgeDevice”的设备。

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   如果收到有关 iothubowner 策略密钥的错误，请确保 Cloud Shell 正在运行最新版 azure-iot 扩展。

2. 查看设备的连接字符串，该字符串将物理设备与其在 IoT 中心的标识链接在一起。 它包含 IoT 中心名称、设备名称，以及用于对这两者之间的连接进行身份验证的共享密钥。 设置 IoT Edge 设备时，我们将在下一部分中再次引用此连接字符串。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

   ![查看 CLI 输出中的连接字符串](./media/quickstart/retrieve-connection-string.png)

## <a name="configure-your-iot-edge-device"></a>配置 IoT Edge 设备

创建具有 Azure IoT Edge 运行时的虚拟机。

![关系图 - 在设备上启动运行时](./media/quickstart-linux/start-runtime.png)

IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 每次 IoT Edge 设备启动并通过启动 IoT Edge 代理启动设备时，*IoT Edge 安全守护程序* 就会启动。 *IoT Edge 代理* 协助部署和监视 IoT Edge 设备（包括 IoT Edge 中心）的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。

在运行时配置期间，你提供设备连接字符串。 这是从 Azure CLI 检索的字符串。 此字符串将物理设备与 Azure 中的 IoT Edge 设备标识关联在一起。

### <a name="deploy-the-iot-edge-device"></a>部署 IoT Edge 设备

本部分使用 Azure 资源管理器模板来创建新的虚拟机，并在其上安装 IoT Edge 运行时。 如果要改为使用自己的 Linux 设备，则可以按照[安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的安装步骤进行操作，然后返回本快速入门。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

使用以下 CLI 命令基于预生成的 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 模板创建 IoT Edge 设备。

* 对于 bash 或 Cloud Shell 用户，请将以下命令复制到文本编辑器中，将占位符文本替换为自己的信息，然后将其复制到 bash 或 Cloud Shell 窗口中：

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://aka.ms/iotedge-vm-deploy" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* 对于 PowerShell 用户，请将以下命令复制到 PowerShell 窗口中，然后将占位符文本替换为自己的信息：

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://aka.ms/iotedge-vm-deploy" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

使用以下 CLI 命令基于预生成的 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy/tree/1.2.0) 模板创建 IoT Edge 设备。

* 对于 bash 或 Cloud Shell 用户，请将以下命令复制到文本编辑器中，将占位符文本替换为自己的信息，然后将其复制到 bash 或 Cloud Shell 窗口中：

   ```azurecli-interactive
   az deployment group create \
   --resource-group IoTEdgeResources \
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' \
   --parameters adminUsername='azureUser' \
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) \
   --parameters authenticationType='password' \
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```

* 对于 PowerShell 用户，请将以下命令复制到 PowerShell 窗口中，然后将占位符文本替换为自己的信息：

   ```azurecli
   az deployment group create `
   --resource-group IoTEdgeResources `
   --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" `
   --parameters dnsLabelPrefix='<REPLACE_WITH_VM_NAME>' `
   --parameters adminUsername='azureUser' `
   --parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name <REPLACE_WITH_HUB_NAME> -o tsv) `
   --parameters authenticationType='password' `
   --parameters adminPasswordOrKey="<REPLACE_WITH_PASSWORD>"
   ```
:::moniker-end
<!-- end 1.2 -->

此模板采用以下参数：

| 参数 | 说明 |
| --------- | ----------- |
| **resource-group** | 将在其中创建资源的资源组。 使用本文中使用的默认 IoTEdgeResources，或在订阅中提供现有资源组的名称。 |
| **template-uri** | 指向我们所使用的资源管理器模板的指针。 |
| **dnsLabelPrefix** | 将用于创建虚拟机的主机名的字符串。 将占位符文本替换为虚拟机名称。 |
| **adminUsername** | 虚拟机的管理员帐户的用户名。 使用示例 azureUser，或提供新的用户名。 |
| **deviceConnectionString** | IoT 中心中设备标识的连接字符串，用于配置虚拟机上的 IoT Edge 运行时。 此参数中的 CLI 命令可获取连接字符串。 将占位符文本替换为 IoT 中心名称。 |
| **authenticationType** | 管理员帐户的身份验证方法。 本快速入门使用密码身份验证，但也可以将此参数设置为“sshPublicKey” 。 |
| **adminPasswordOrKey** | 管理员帐户的 SSH 密钥的密码或值。 将占位符文本替换为安全密码。 密码长度必须至少为 12 个字符，并包含以下四种字符中的三种字符：小写字符、大写字符、数字和特殊字符。 |

部署完成后，应会在 CLI 中收到 JSON 格式的输出，其中包含用于连接到虚拟机的 SSH 信息。 复制“输出”部分的“公共 SSH”条目的值 ：

   ![从输出中检索公共 SSH 值](./media/quickstart-linux/outputs-public-ssh.png)

### <a name="view-the-iot-edge-runtime-status"></a>查看 IoT Edge 运行时状态

此快速入门中的其余命令都在 Azure IoT Edge 上完成，以便查看设备发生的情况。 如果使用的是虚拟机，现请使用设置的管理员用户名和部署命令输出的 DNS 名称，连接到该计算机。 也可在 Azure 门户中虚拟机的“概述”页面上找到 DNS 名称。 使用以下命令连接到虚拟机。 将 `{admin username}` 和 `{DNS name}` 替换为自己的值。

   ```console
   ssh {admin username}@{DNS name}
   ```

连接到虚拟机后，验证是否已在 IoT Edge 设备上成功安装并配置运行时。

<!--1.1 -->
:::moniker range="iotedge-2018-06"

1. 查看 IoT Edge 安全守护程序是否正作为系统服务运行。

   ```bash
   sudo systemctl status iotedge
   ```

   ![查看作为系统服务运行的 IoT Edge 守护程序](./media/quickstart-linux/iotedged-running.png)

   >[!TIP]
   >需要提升权限才能运行 `iotedge` 命令。 安装 IoT Edge 运行时后从计算机中注销并第一次重新登录后，你的权限将自动更新。 在此之前，请在命令前使用 `sudo`。

2. 若需排查服务问题，请检索服务日志。

   ```bash
   journalctl -u iotedge
   ```

3. 查看在 IoT Edge 设备上运行的所有模块。 由于此服务是第一次运行，因此只会看到 **edgeAgent** 模块在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。

   ```bash
   sudo iotedge list
   ```

   ![查看设备上的一个模块](./media/quickstart-linux/iotedge-list-1.png)
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 检查 IoT Edge 是否正在运行。 如果 IoT Edge 正在运行，以下命令应该会返回“正常”状态，否则就会提供任何服务错误。

   ```bash
   sudo iotedge system status
   ```

   >[!TIP]
   >需要提升权限才能运行 `iotedge` 命令。 安装 IoT Edge 运行时后从计算机中注销并第一次重新登录后，你的权限将自动更新。 在此之前，请在命令前使用 `sudo`。

2. 若需排查服务问题，请检索服务日志。

   ```bash
   sudo iotedge system logs
   ```

3. 查看在 IoT Edge 设备上运行的所有模块。 由于此服务是第一次运行，因此只会看到 **edgeAgent** 模块在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。

   ```bash
   sudo iotedge list
   ```

:::moniker-end
<!-- end 1.2 -->

IoT Edge 设备现在已配置好。 它可以运行云部署型模块了。

## <a name="deploy-a-module"></a>部署模块

从云端管理 Azure IoT Edge 设备，部署将遥测数据发送到 IoT 中心的模块。

![关系图 - 将模块从云部署到设备](./media/quickstart-linux/deploy-module.png)

<!-- [!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2 -->

Azure IoT Edge 的主要功能之一是从云中将代码部署到 IoT Edge 设备。 *IoT Edge 模块* 是以容器形式实现的可执行程序包。 在本部分中，你将直接从 Azure IoT 中心的 [Azure 市场的 IoT Edge 模块部分](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)部署一个预建的模块。

在本部分中部署的模块模拟一个传感器并发送生成的数据。 开始使用 IoT Edge 时，此模块是非常有用的代码段，因为你可以使用模拟的数据进行开发和测试。 如果要确切了解此模块的功能，则可以查看[模拟的温度传感器源代码](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs)。

按照以下步骤启动设置模块向导，从 Azure 市场部署第一个模块。

1. 登录 [Azure 门户](https://portal.azure.com)并转到 IoT 中心。

1. 从左侧菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 选择设备列表中目标设备的设备 ID。

   创建新的 IoT Edge 设备时，它将在 Azure 门户中显示状态代码 `417 -- The device's deployment configuration is not set`。 此状态正常，表示设备已准备好接收模块部署。

1. 在上方栏中，选择“设置模块”。

   ![显示正在选择“设置模块”的屏幕截图。](./media/quickstart/select-set-modules.png)

### <a name="modules"></a>模块

向导的第一步是选择要在设备上运行的模块。

在“IoT Edge 模块”下，打开“添加”下拉菜单，然后选择“市场模块”  。

   ![显示“添加”下拉菜单的屏幕截图。](./media/quickstart/add-marketplace-module.png)

在 IoT Edge 模块市场中，搜索并选择 `Simulated Temperature Sensor` 模块。 该模块会添加到“IoT Edge 模块”部分，并处于所需的运行状态。

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

选择“运行时设置”，打开 edgeHub 和 edgeAgent 模块的设置。 在此设置部分，你可以通过添加环境变量或更改创建选项来管理运行时模块。

更新 edgeHub 和 edgeAgent 模块的“映像”字段，以使用版本标记 1.2。 例如：

* `mcr.microsoft.com/azureiotedge-hub:1.2`
* `mcr.microsoft.com/azureiotedge-agent:1.2`

选择“保存”，将更改应用到运行时模块。

:::moniker-end
<!--end 1.2-->

在完成时选择“下一步:”路由以继续执行向导的下一步。

   ![显示在添加模块后继续下一步的屏幕截图。](./media/quickstart/view-temperature-sensor-next-routes.png)

### <a name="routes"></a>路由

在“路由”选项卡上，删除默认路由（路由），然后选择“下一步:   查看 + 创建”继续执行向导的下一步。

   >[!Note]
   >路由使用名称和值对构造而成。 应在此页上看到两条路由。 默认路由（路由）会将所有消息发送到 IoT 中心（称为 `$upstream`）。 从 Azure 市场添加模块时，将自动创建第二条路由 (SimulatedTemperatureSensorToIoTHub)。 此路由用于将所有来自模拟温度模块中的消息发送到 IoT 中心。 可以删除默认路由，因为在这种情况下它是多余的。

   ![显示删除默认路由并转到下一步的屏幕截图。](./media/quickstart/delete-route-next-review-create.png)

### <a name="review-and-create"></a>查看 + 创建

查看 JSON 文件，然后选择“创建”。 JSON 文件定义你部署到 IoT Edge 设备的所有模块。 你将看到“SimulatedTemperatureSensor”模块以及“edgeAgent”和“edgeHub”这两个运行时模块  。

   >[!Note]
   >在将新部署提交到 IoT Edge 设备时，不会向设备推送任何内容。 相反，设备会定期对 IoT 中心进行查询以获取任何新指令。 如果设备找到了更新的部署清单，则它会使用关于新部署的信息来从云中拉取模块映像，然后开始在本地运行模块。 此过程可能需要几分钟。

创建模块部署详细信息后，向导会将你返回到设备详细信息页。 在“模块”选项卡上查看部署状态。

应会看到三个模块：$edgeAgent、$edgeHub 和 SimulatedTemperatureSensor  。 如果其中一个或多个模块在“在部署中指定”下为“是”，但在“由设备报告”下不是，则意味着 IoT Edge 设备仍在启动它们  。 等待几分钟，然后刷新页面。

   ![显示已部署模块列表中模拟温度传感器的屏幕截图。](./media/quickstart/view-deployed-modules.png)

## <a name="view-generated-data"></a>查看生成的数据

此快速入门中，创建了新的 IoT Edge 设备，并在该设备上安装了 IoT Edge 运行时。 然后，使用了 Azure 门户部署 IoT Edge 模块，使其在不更改设备本身的情况下在设备上运行。

在这种情况下，推送的模块会生成示例环境数据，你可以在稍后使用该数据进行测试。 模拟传感器正在监视一台计算机和该计算机周围的环境。 例如，该传感器可能位于服务器机房中、工厂地板上或风力涡轮机上。 该消息包括环境温度和湿度、机器温度和压力以及时间戳。 IoT Edge 教程使用此模块创建的数据作为测试数据进行分析。

再次在 IoT Edge 设备上打开命令提示符，或者从 Azure CLI 使用 SSH 连接。 确认从云中部署的模块正在 IoT Edge 设备上运行：

   ```bash
   sudo iotedge list
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
   ![查看设备上的三个模块](./media/quickstart-linux/iotedge-list-2-version-201806.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
   ![查看设备上的三个模块](./media/quickstart-linux/iotedge-list-2-version-202011.png)
:::moniker-end

查看从温度传感器模块发送的消息：

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >引用模块名称时，IoT Edge 命令区分大小写。

   ![查看模块中的数据](./media/quickstart-linux/iotedge-logs.png)

也可使用 [Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看到达 IoT 中心的消息。

## <a name="clean-up-resources"></a>清理资源

若要继续学习 IoT Edge 教程，可以使用在本快速入门中注册并设置的设备。 否则，可删除已创建的 Azure 资源，避免产生费用。

如果是在新资源组中创建的虚拟机和 IoT 中心，则可删除该组以及所有关联的资源。 仔细检查资源组的内容，确保没有要保留的内容。 如果不希望删除整个组，则可改为删除单个资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。

删除 **IoTEdgeResources** 组。 可能需要花费几分钟来删除资源组。

```azurecli-interactive
az group delete --name IoTEdgeResources --yes
```

可以通过查看资源组列表来确认已删除该资源组。

```azurecli-interactive
az group list
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 IoT Edge 设备并使用 Azure IoT Edge 云接口将代码部署到该设备上。 现在，你有了一个可以生成其环境的原始数据的测试设备。

下一教程将介绍如何从 Azure 门户监视设备的活动和运行状况。

> [!div class="nextstepaction"]
> [监视 IoT Edge 设备](tutorial-monitor-with-workbooks.md)
