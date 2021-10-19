---
title: 教程 - 创建 IoT Edge 设备的层次结构 - Azure IoT Edge
description: 本教程演示如何使用网关创建 IoT Edge 设备的层次结构。
author: v-tcassi
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: dc878b0f1a843d8212cd6541338510f8eff4b56c
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714934"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>教程：创建 IoT Edge 设备的层次结构

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

在层次结构层中组织的网络之间部署 Azure IoT Edge 节点。 层次结构中的每一层都是一个网关设备，用于处理来自其下一层中设备的消息和请求。

你可构建设备的层次结构，使得只有顶层可连接到云，而下层只能与相邻的南北层通信。 大多数工业网络遵循 [ISA-95 标准](https://en.wikipedia.org/wiki/ANSI/ISA-95)，而这种网络分层是其基础。

本教程的目的是创建一个模拟简化生产环境的 IoT Edge 设备的层次结构。 最后，你会通过层次结构下载容器映像，从而将[模拟温度传感器模块](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)部署到无 Internet 访问的下层设备。

为实现此目标，本教程将指导你创建 IoT Edge 设备的层次结构，将 IoT Edge 运行时容器部署到设备，并在本地配置设备。 在本教程中，你将使用自动化配置工具执行以下操作：

> [!div class="checklist"]
>
> * 创建和定义 IoT Edge 设备层次结构中的关系。
> * 在层次结构中的设备上配置 IoT Edge 运行时。
> * 在设备层次结构中安装一致的证书。
> * 向层次结构中的设备添加工作负载。
> * 使用 [IoT Edge API 代理模块](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)，通过下层设备中的单个端口安全地路由 HTTP 流量。

>[!TIP]
>本教程包括混合使用手动和自动化步骤来展示嵌套的 IoT Edge 功能。
>
>要了解设置 IoT Edge 设备层次结构的完全自动化步骤，可以遵循已脚本化的[适用于工业 IoT 的 Azure IoT Edge 示例](https://aka.ms/iotedge-nested-sample)。 这一已编写了脚本的方案将 Azure 虚拟机部署为预配置设备以模拟工厂环境。
>
>要深入了解创建和管理 IoT Edge 设备层次结构的手动步骤，请参阅 [IoT Edge 设备网关层次结构操作指南](how-to-connect-downstream-iot-edge-device.md)。

在本教程中，定义了以下网络层：

* **顶层**：此层的 IoT Edge 设备可直接连接到云。

* **下层**：位于顶层下面的层的 IoT Edge 设备无法直接连接到云。 它们需要通过一个或多个中间 IoT Edge 设备来发送和接收数据。

为简单起见，本教程使用双设备层次结构（见下图）。 一台设备是顶层设备，表示位于层次结构顶层的设备，该设备可以直接连接到云。 此设备也称为“父设备”。 另一台设备是下层设备，表示位于层次结构下层的设备，该设备无法直接连接到云。 可以根据需要添加更多的下层设备来表示生产环境。 下层设备也称为“子设备”。

![教程层次结构，其中包含两个设备：顶层设备和下层设备](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>先决条件

若要创建 IoT Edge 设备的层次结构，你将需要：

* 一台具有 Internet 连接的计算机（Windows 或 Linux）。
* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 使用安装了 Azure IoT 扩展 v0.10.6 或更高版本的 Azure CLI v2.3.1 的 Azure Cloud Shell 中的 Bash Shell。 本教程使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 如果你不熟悉 Azure Cloud Shell，[请查看快速入门以了解详细信息](./quickstart-linux.md#prerequisites)。
  * 若要查看 Azure CLI 模块和扩展的当前版本，请运行 [az version](/cli/azure/reference-index?#az_version)。
* 一个要为层次结构中的每个设备配置为 IoT Edge 设备的 Linux 设备。 本教程使用两个设备。 如果没有可用设备，可以使用以下命令为层次结构中的每个设备创建 Azure 虚拟机。

   替换以下命令中的占位符文本，并运行两次（每个虚拟机一次）。 每个虚拟机都需要一个唯一的 DNS 前缀，该前缀也将用作其名称。 DNS 前缀必须符合以下正则表达式：`[a-z][a-z0-9-]{1,61}[a-z0-9]`。

   ```azurecli
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   虚拟机使用 SSH 密钥对用户进行身份验证。 如果不熟悉如何创建和使用 SSH 密钥，可按照 [Azure 中 Linux VM 的 SSH 公钥-私钥密钥对说明](../virtual-machines/linux/mac-create-ssh-keys.md)进行操作。

   IoT Edge 版本 1.2 预装了此 ARM 模板，因此用户不必在虚拟机上手动安装这些资产。 如果要在自己的设备上安装 IoT Edge，请参阅[安装 Azure IoT Edge for Linux（版本 1.2）](how-to-install-iot-edge.md)或[将 IoT Edge 更新为版本 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)。

   使用此 ARM 模板成功创建虚拟机时，将输出虚拟机的 `SSH` 句柄和完全限定域名 (`FQDN`)。 在后续步骤中，将使用 SSH 句柄以及每个虚拟机的 FQDN 或 IP 地址进行配置，因此请记录此信息。 下图提供了一个示例输出。

   >[!TIP]
   >你还可以在 Azure 门户上查找 IP 地址和 FQDN。 对于 IP 地址，请导航到虚拟机列表，并记下公共 IP 地址字段。 对于 FQDN，请转到每个虚拟机的概述页面，并查找 DNS 名称字段。

   ![创建虚拟机时会输出 JSON，其中包含虚拟机的 SSH 句柄](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* 请确保以下端口对于所有设备均为开放入站（最下层设备 443、5671、8883 除外）：
  * 443：在父边缘中心和子边缘中心之间用于 REST API 调用，并拉取 docker 容器映像。
  * 5671、8883：用于 AMQP 和 MQTT。

  有关详细信息，请参阅[如何使用 Azure 门户向虚拟机开放端口](../virtual-machines/windows/nsg-quickstart-portal.md)。

## <a name="configure-your-iot-edge-device-hierarchy"></a>配置 IoT Edge 设备层次结构

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>创建 IoT Edge 设备的层次结构

IoT Edge 设备构成层次结构的各个层。 本教程将创建包含以下两个 IoT Edge 设备的层次结构：顶层设备及其子设备（下层设备） 。 可根据需要创建其他子设备。

要创建和配置 IoT Edge 设备的层次结构，请使用 `iotedge-config` 工具。 此工具可简化层次结构的配置，它将多个步骤压缩为以下两个部分并自动完成：

1. 设置云配置并准备每个设备的配置，其中包括：

   * 在 IoT 中心创建设备
   * 设置父子关系以授权设备间的通信
   * 为每个设备生成证书链以在它们之间建立安全通信
   * 为每个设备生成配置文件

1. 安装每个设备的配置，其中包括：

   * 在每个设备上安装证书
   * 为每个设备应用配置文件

`iotedge-config` 工具还会自动将模块部署到 IoT Edge 设备。

要使用 `iotedge-config` 工具创建和配置层次结构，请在 Azure CLI 中执行以下步骤：

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中，为教程的资源创建目录：

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. 下载配置工具和配置模板的发行版：

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   这会在教程目录中创建 `iotedge_config_cli_release` 文件夹。

   用于创建设备层次结构的模板文件是在 `iotedge_config.yaml` 文件，可在 `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` 中找到。 同一目录下的 `deploymentLowerLayer.json` 是 JSON 部署文件，其中包含有关要部署到下层设备的模块的说明。 `deploymentTopLayer.json` 文件也是部署文件，但适用于上层设备，因为部署到每个设备的模块不同。 `device_config.toml` 文件是 IoT Edge 设备配置的模板，用于为层次结构中的设备自动生成配置捆绑包。

   要查看 `iotedge-config` 工具的源代码和脚本，请查看 [GitHub 上的 Azure-Samples 存储库](https://github.com/Azure-Samples/iotedge_config_cli)。

1. 打开教程配置模板，并用自己的信息进行编辑：

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   在 iothub 部分，用自己的信息填充 `iothub_hostname` 和 `iothub_name` 字段。 此信息可在 Azure 门户上 IoT 中心的概述页面找到。

   在可选的 certificates 部分，可以用证书和密钥的绝对路径填充相应的字段。 如果将这些字段留空，脚本会自动生成自签名测试证书供你使用。 如果不熟悉如何在网关应用场景下使用证书，请参阅[操作指南的证书部分](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)。

   在 configuration 部分，`template_config_path` 是指向用于创建设备配置的 `device_config.toml` 模板的路径。 此 `default_edge_agent` 字段确定下层设备将拉取的 Edge 代理映像以及从何处拉取。

   在 edgedevices 部分，对于生产应用场景，可以编辑层次结构树，以反映所需的结构。 就本教程而言，请接受默认树。 每个设备都有一个 `device_id` 字段，你可以在其中对设备命名。 还有一个 `deployment` 字段，该字段指定该设备的部署 JSON 的路径。

   还可以通过 Azure 门户或 Azure Cloud Shell 在 IoT 中心手动注册 IoT Edge 设备。 要了解操作步骤，请参阅[注册 IoT Edge 设备操作指南](how-to-register-device.md)。

   也可以手动定义父子关系。 有关详细信息，请参阅操作指南的[创建网关层次结构](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy)部分。

   ![配置文件的 edgedevices 部分可用于定义层次结构](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. 保存并关闭文件：

   `CTRL + S`, `CTRL + Q`

1. 为教程资源目录中的配置捆绑包创建输出目录：

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. 导航到 `iotedge_config_cli_release` 目录，然后运行该工具以创建 IoT Edge 设备的层次结构：

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   如果使用 `--output` 标记，该工具会在所选目录中创建设备证书、证书捆绑包和日志文件。 设置 `-f` 标记后，该工具会自动查找 IoT 中心内现有的 IoT Edge 设备并将其删除，以避免错误并使中心干净整洁。

   配置工具会创建 IoT Edge 设备，并设置它们之间的父子关系。 此外，它还会创建证书以供设备使用。 如果提供了部署 JSON 的路径，该工具会自动为设备创建这些部署，但这不是必需的步骤。 最后，该工具将为设备生成配置捆绑包，并将它们放在输出目录中。 要全面了解配置工具执行的步骤，请参阅输出目录中的日志文件。

   ![执行这些步骤时，该脚本将显示层次结构的拓扑](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

仔细检查脚本的拓扑输出是否正确。 确认层次结构正确以后，便可继续操作。

### <a name="configure-the-iot-edge-runtime"></a>配置 IoT Edge 运行时

除了预配你的设备之外，配置步骤还使用你之前创建的证书在层次结构中的设备之间建立受信任的通信。 这些步骤还会开始建立层次结构的网络结构。 顶层设备会保持 Internet 连接，以便从云中拉取其运行时的映像，而下层设备则会通过顶层设备进行路由，以便访问这些映像。

要配置 IoT Edge 运行时，需要将安装脚本创建的配置捆绑包应用到设备。 上层设备与下层设备的配置稍有不同，因此请注意要应用到每个设备的设备配置文件 。

1. 每个设备都需要其相应的配置捆绑包。 可使用 U 盘或通过[安全文件复制操作](https://www.ssh.com/ssh/scp/)将配置捆绑包移动到每个设备上。

   请确保向每个设备发送正确的配置捆绑包。

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~` 表示配置文件夹将放在虚拟机上的主目录中。

1. 登录到虚拟机，将配置捆绑包应用到设备：

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. 在每个设备上，解压缩配置捆绑包。 首先需要安装 zip：

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. 在每个设备上，将配置捆绑包应用到设备：

   ```bash
   sudo ./install.sh
   ```

   在上层设备中，会提示你输入主机名。 在下层设备中，会要求你提供主机名和父级的主机名。 对于每个提示，请提供相应的 IP 或 FQDN。 你可以使用其中任意一个，但请在所有设备上保持一致。 安装脚本的输出如下图所示。

   如果你想要更深入地了解对设备配置文件所做的修改，请参阅[操作指南的在设备上配置 IoT Edge 部分](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)。

  ![安装配置捆绑包会更新设备上的 config.toml 文件，并自动重启所有 IoT Edge 服务](./media/tutorial-nested-iot-edge/configuration-install-output.png)

如果已正确完成上述步骤，可以检查设备是否已正确配置。

在设备上运行配置和连接性检查。 对于上层设备：

   ```bash
   sudo iotedge check
   ```

对于下层设备，需要在命令中手动传递诊断映像：

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:443/azureiotedge-diagnostics:1.2
   ```

在上层设备中，你会看到一个输出，其中包含多个传递评估。 可能会显示有关日志策略的一些警告，具体取决于网络情况和 DNS 策略。

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

确认每个设备上的配置都正确以后，便可继续操作。

## <a name="deploy-modules-to-your-devices"></a>将模块部署到设备

创建设备时会自动生成设备的模块部署。 创建上层设备和下层设备后，`iotedge-config-cli` 工具会为它们提供部署 JSON。 在每个设备上配置 IoT Edge 运行时时，模块部署处于挂起状态。 配置运行时后，才开始对上层设备的部署。 这些部署完成后，下层设备可能会使用 IoT Edge API 代理模块来拉取它所需的映像 。

在 [Azure Cloud Shell](https://shell.azure.com/) 中，可以查看上层设备的部署 JSON，了解部署到设备的模块：

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

除了 IoT Edge 代理和 IoT Edge 中心这两个运行时模块外，上层设备还会接收 Docker 注册表模块和 IoT Edge API 代理模块    。

Docker 注册表模块指向现有的 Azure 容器注册表。 在本例中，`REGISTRY_PROXY_REMOTEURL` 指向 Microsoft Container Registry。 Docker 注册表默认侦听端口 5000。

IoT Edge API 代理模块可将 HTTP 请求路由到其他模块，使下层设备能拉取容器映像或将 blob 推送到存储。 在本教程中，它在端口 443 上进行通信，并配置为将 Docker 容器映像拉取请求路由发送到端口 5000 上的 Docker 注册表模块。 此外，所有 blob 存储都将请求路由上传到端口 11002 上的 AzureBlobStorageonIoTEdge 模块。 有关 IoT Edge API 代理模块及其配置方法的详细信息，请参阅模块的[操作指南](how-to-configure-api-proxy-module.md)。

要查看如何通过 Azure 门户或 Azure Cloud Shell 创建与此类似的部署，请参阅[操作指南的上层设备部分](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)。

在 [Azure Cloud Shell](https://shell.azure.com/) 中，可以查看下层设备的部署 JSON，了解部署到设备的模块：

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

你可以在 `systemModules` 下面看到，下层设备的运行时模块设置为从 `$upstream:443`（而不是 `mcr.microsoft.com`）进行拉取，这与上层设备相同 。 下层设备将 Docker 映像请求发送到端口 443 上的 IoT Edge API 代理模块，因为它无法直接从云中拉取映像 。 另一个部署到下层设备的模块（模拟温度传感器模块）也向 `$upstream:443` 发出其映像请求 。

要查看如何通过 Azure 门户或 Azure Cloud Shell 创建与此类似的部署，请参阅[操作指南的下层设备部分](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)。

你可以使用以下命令查看模块的状态：

   ```azurecli
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   此命令将输出所有 edgeAgent 报告属性。 下面是一些有助于监视设备状态的命令：runtime status、runtime start time、runtime last exit time、runtime restart count   。

你还可以在 [Azure 门户](https://ms.portal.azure.com/)上查看模块的状态。 导航到 IoT 中心的 IoT Edge 部分，查看设备和模块。

对模块部署满意后，便可继续操作。

## <a name="view-generated-data"></a>查看生成的数据

你推送的模拟温度传感器模块会生成示例环境数据。 它将发送包含环境温度和湿度、机器温度和压力以及时间戳的消息。

还可以通过 [Azure Cloud Shell](https://shell.azure.com/) 查看这些消息：

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>故障排除

运行 `iotedge check` 命令来验证配置并解决错误。

即使子计算机没有直接访问 Internet 的权限，也可以在嵌套层次结构中运行 `iotedge check`。

从下层运行 `iotedge check` 时，程序将尝试通过端口 443 从父计算机拉取映像。

```bash
sudo iotedge check --diagnostics-image-name $upstream:443/azureiotedge-diagnostics:1.2
```

`azureiotedge-diagnostics` 值是从与注册表模块链接的容器注册表中拉取的。 本教程将其默认设置为 https://mcr.microsoft.com:

| 名称 | 值 |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

如果使用的是专用容器注册表，请确保容器注册表中包含所有映像（IoTEdgeAPIProxy、edgeAgent、edgeHub、模拟温度传感器映像和诊断映像）。

## <a name="clean-up-resources"></a>清理资源

可以删除本文中创建的本地配置和 Azure 资源，以避免收费。

若要删除资源，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 

2. 选择包含 IoT Edge 测试资源的资源组的名称。 

3. 查看包含在资源组中的资源的列表。 若要删除这一切，可以选择“删除资源组”。 如果只需删除部分内容，可以单击要单独删除的每个资源。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已将两个 IoT Edge 设备配置为网关，并设置了一个作为另一个设备的父设备。 然后，演示了如何使用 IoT Edge API 代理模块通过网关将容器映像拉取到子设备上。 要了解详细信息，请参阅[代理模块用法操作指南](how-to-configure-api-proxy-module.md)。

要详细了解如何使用网关创建 IoT Edge 设备的层次结构，请参阅[连接下游 IoT Edge 设备操作指南](how-to-connect-downstream-iot-edge-device.md)。

若要了解 Azure IoT Edge 如何为企业创建更多解决方案，请继续学习其他教程。

> [!div class="nextstepaction"]
> [将 Azure 机器学习模型作为一个模块部署](tutorial-deploy-machine-learning.md)
