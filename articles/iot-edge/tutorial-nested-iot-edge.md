---
title: 教程 - 创建 IoT Edge 设备的层次结构 - Azure IoT Edge
description: 本教程演示如何使用网关创建 IoT Edge 设备的层次结构。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: c1b30a1eafe9af92c1ef3f81773d213ccf96555c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462025"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>教程：创建 IoT Edge 设备的层次结构（预览版）

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

在层次结构层中组织的网络之间部署 Azure IoT Edge 节点。 层次结构中的每一层都是一个网关设备，用于处理来自其下一层中设备的消息和请求。

>[!NOTE]
>此功能要求运行 Linux 容器的 IoT Edge 1.2 版本，该版本为公共预览版。

你可构建设备的层次结构，使得只有顶层可连接到云，而下层只能与相邻的南北层通信。 大多数工业网络遵循 [ISA-95 标准](https://en.wikipedia.org/wiki/ANSI/ISA-95)，而这种网络分层是其基础。

本教程的目的是创建一个模拟生产环境的 IoT Edge 设备的层次结构。 最后，你会通过层次结构下载容器映像，从而将[模拟温度传感器模块](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)部署到无 Internet 访问的下层设备。

为实现此目标，本教程将指导你创建 IoT Edge 设备的层次结构，将 IoT Edge 运行时容器部署到设备，并在本地配置设备。 在本教程中，你将了解：

> [!div class="checklist"]
>
> * 创建和定义 IoT Edge 设备层次结构中的关系。
> * 在层次结构中的设备上配置 IoT Edge 运行时。
> * 在设备层次结构中安装一致的证书。
> * 向层次结构中的设备添加工作负载。
> * 使用 [IoT Edge API 代理模块](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)，通过下层设备中的单个端口安全地路由 HTTP 流量。

在本教程中，定义了以下网络层：

* **顶层**：此层的 IoT Edge 设备可直接连接到云。

* **下层**：位于顶层下面的层的 IoT Edge 设备无法直接连接到云。 它们需要通过一个或多个中间 IoT Edge 设备来发送和接收数据。

为简单起见，本教程使用双设备层次结构（见下图）。 一台设备是顶层设备，表示位于层次结构顶层的设备，该设备可以直接连接到云。 此设备也称为“父设备”。 另一台设备是下层设备，表示位于层次结构下层的设备，该设备无法直接连接到云。 可以根据需要添加额外的下层设备来表示生产环境。 下层设备也称为“子设备”。 任何额外下层设备的配置都将遵循 **下层设备** 的配置。

![教程层次结构，其中包含两个设备：顶层设备和下层设备](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>先决条件

若要创建 IoT Edge 设备的层次结构，你将需要：

* 一台具有 Internet 连接的计算机（Windows 或 Linux）。
* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 安装了 Azure IoT 扩展 v0.10.6 或更高版本的 Azure CLI v2.3.1。 本教程使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 如果你不熟悉 Azure Cloud Shell，[请查看快速入门以了解详细信息](./quickstart-linux.md#prerequisites)。
  * 若要查看 Azure CLI 模块和扩展的当前版本，请运行 [az version](/cli/azure/reference-index?#az_version)。
* 一个要为层次结构中的每个设备配置为 IoT Edge 设备的 Linux 设备。 本教程使用两个设备。 如果没有可用设备，可以替换以下命令中的占位符文本，然后运行它，以便为层次结构中的每个设备创建 Azure 虚拟机：

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* 请确保以下端口已打开入站：8000、443、5671、8883：
  * 8000：用于通过 API 代理拉取 Docker 容器映像。
  * 443：在 REST API 调用的父边缘中心和子边缘中心之间使用。
  * 5671、8883：用于 AMQP 和 MQTT。

  有关详细信息，请参阅[如何使用 Azure 门户向虚拟机开放端口](../virtual-machines/windows/nsg-quickstart-portal.md)。

>[!TIP]
>若要自动查看 IoT Edge 设备的层次结构的设置方式，可以遵循已编写了脚本的[适用于工业 IoT 的 Azure IoT Edge 示例](https://aka.ms/iotedge-nested-sample)。 这一已编写了脚本的方案将 Azure 虚拟机部署为预配置设备以模拟工厂环境。
>
>若要继续逐步创建示例层次结构，请继续完成以下教程步骤。

## <a name="configure-your-iot-edge-device-hierarchy"></a>配置 IoT Edge 设备层次结构

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>创建 IoT Edge 设备的层次结构

IoT Edge 设备构成层次结构的各个层。 本教程将创建包含以下两个 IoT Edge 设备的层次结构：顶层设备及其子设备（下层设备） 。 可根据需要创建更多的子设备。

若要创建 IoT Edge 设备，可以使用 Azure 门户或 Azure CLI。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 选择“+ 添加 IoT Edge 设备”。 此设备将为顶层设备，因此请输入适当的唯一设备 ID。 选择“保存”。

1. 再次选择“+ 添加 IoT Edge 设备”。 此设备将是一个下层设备，因此请输入一个适当且独一无二的设备 ID。

1. 选择“设置父设备”，从设备列表中选择顶层设备，然后选择“确定” 。 选择“保存”。

   ![为下层设备设置父项](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中输入以下命令，以在中心创建一个 IoT Edge 设备。 此设备将为顶层设备，因此请输入适当的唯一设备 ID：

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   如果成功创建设备，则会输出设备的 JSON 配置。

   ![成功创建设备后的 JSON 输出](./media/tutorial-nested-iot-edge/json-success-output.png)

1. 输入以下命令，以便创建一个下层 Edge 设备。 如果希望层次结构中有更多的层，则可创建多个下层设备。 请确保为每个设备提供独一无二的设备标识。

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 输入以下命令，定义 **顶层设备** 与每个 **下层设备** 之间的父子关系。 请确保为层次结构中的每个下层设备运行此命令。

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   此命令没有显式输出。

---

接下来，请记下每个 IoT Edge 设备的连接字符串。 稍后将用到它们。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的“IoT Edge”部分。

1. 单击设备列表中某个设备的设备 ID。

1. 在“主连接字符串”字段上选择“复制”，并将其记录到所选位置 。

1. 对所有其他设备重复此操作。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中，为每个设备输入以下命令以检索设备的连接字符串，并将其记录到你选择的位置：

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

如果已正确完成上述步骤，则可使用以下步骤，在 Azure 门户或 Azure CLI 中检查父子关系是否正确。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心的“IoT Edge”部分。

1. 在设备列表中单击下层设备的设备 ID。

1. 在设备的详细信息页上，应会看到“父设备”字段旁边列出了 **顶层设备** 的标识。

   [子设备确认的父设备](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

还可以通过 **顶层设备** 确认层次结构关系中的子设备。

1. 在设备列表中单击顶层设备的设备 ID。

1. 选择顶部的“管理子设备”选项卡。

1. 在设备列表下，你应该会看到你的 **下层设备**。

   [父设备确认的子设备](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中，可以通过获取父设备确认的子设备的标识，来验证子设备是否已成功建立与父设备的关系。 这会输出父设备的 JSON 配置，如下图所示：

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

还可以通过查询 **顶层设备** 来确认层次结构关系中的子设备。

1. 列出父设备知道的子设备：

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

确认层次结构正确以后，便可继续操作。

### <a name="create-certificates"></a>创建证书

[网关方案](iot-edge-as-gateway.md)中的所有设备需要一个共享证书，目的是在它们之间建立安全连接。 使用以下步骤为此方案中的两个设备创建演示证书。

若要在 Linux 设备上创建演示证书，需要克隆生成脚本并将其设置为在 bash 中本地运行。

1. 克隆 IoT Edge Git 存储库，其中包含用于生成演示证书的脚本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. 导航到要在其中工作的目录。 所有证书和密钥文件都将在此目录中创建。

1. 将克隆的 IoT Edge 存储库中的配置文件和脚本文件复制到该工作目录。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. 创建根 CA 证书和一个中间证书。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此脚本命令会创建多个证书和密钥文件，但我们将使用以下文件作为网关层次结构的根 CA 证书：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. 使用以下命令创建两组 IoT Edge 设备 CA 证书和私钥：一组用于顶层设备，另一组用于下层设备。 为 CA 证书提供易记的名称以区分它们。

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   此脚本命令会创建多个证书和密钥文件，但我们将在每个 IoT Edge 设备上使用以下证书和密钥对，并在 config 文件中引用它们：

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

每个设备都需要根 CA 证书的副本，以及自己的设备 CA 证书和私钥的副本。 可使用 U 盘或[安全文件复制](https://www.ssh.com/ssh/scp/)将证书移动到每个设备上。

1. 传输证书后，为每个设备安装根 CA。

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   此命令的输出应显示已在 `/etc/ssl/certs` 中添加一个证书。

   [指出成功安装证书的消息](./media/tutorial-nested-iot-edge/certificates-success-output.png)

如果已正确完成上述步骤，则可检查是否已在 `/etc/ssl/certs` 中安装证书，具体方法是导航到该目录并搜索已安装的证书。

1. 导航到 `/etc/ssl/certs`：

   ```bash
   cd /etc/ssl/certs
   ```

1. 列出已安装的证书，针对 `azure` 执行 `grep`：

   ```bash
   ll | grep azure
   ```

   应该会看到关联到根 CA 证书的证书哈希，以及关联到 `usr/local/share` 目录中的副本的根 CA 证书。

   [进行 Azure 证书搜索的结果](./media/tutorial-nested-iot-edge/certificate-list-results.png)

确认证书已安装在每个设备上以后，便可继续操作。

### <a name="install-iot-edge-on-the-devices"></a>在设备上安装 IoT Edge

安装 IoT Edge 版本 1.2 运行时映像以后，设备便可访问要按设备的层次结构形式运行时所需的功能。

若要安装 IoT Edge，需要安装相应的存储库配置、必备组件和必要的版本资产。

1. 安装与设备操作系统匹配的存储库配置。

   * **Ubuntu Server 18.04**：

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**：

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. 将生成的列表复制到 sources.list.d 目录。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. 安装 Microsoft GPG 公钥。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. 更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

1. 安装 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

1. 安装 IoT Edge 和 IoT 标识服务。

   ```bash
   sudo apt-get install aziot-edge
   ```

如果已正确完成上述步骤，则会看到 Azure IoT Edge 横幅消息，其中要求你在层次结构中的每个设备上更新 Azure IoT Edge 配置文件 `/etc/aziot/config.toml`。 如果是这样，则可继续操作。

### <a name="configure-the-iot-edge-runtime"></a>配置 IoT Edge 运行时

除了预配你的设备之外，配置步骤还使用你之前创建的证书在层次结构中的设备之间建立受信任的通信。 这些步骤还会开始建立层次结构的网络结构。 顶层设备会保持 Internet 连接，以便从云中拉取其运行时的映像，而下层设备则会通过顶层设备进行路由，以便访问这些映像。

若要配置 IoT Edge 运行时，需要修改配置文件的多个组件。 配置在 **顶层设备** 和 **下层设备** 之间稍有不同，因此，对于每个步骤，要注意你是在编辑哪个设备的配置文件。 为设备配置 IoT Edge 运行时包含四个步骤，所有步骤均通过编辑 IoT Edge 配置文件来完成：

* 通过将设备的连接字符串添加到配置文件，手动预配每个设备。

* 通过将配置文件指向设备 CA 证书、设备 CA 私钥和根 CA 证书，完成设备证书的设置。

* 使用 IoT Edge 代理启动系统。

* 更新顶层设备的 hostname 参数，并更新下层设备的 hostname 参数和 parent_hostname 参数    。

完成这些步骤，然后重启 IoT Edge 服务来配置设备。

>[!TIP]
>在 Nano 中浏览该配置文件时，可以使用 **Ctrl + W** 在文件中搜索关键字。

1. 在每个设备上，根据包含的模板创建配置文件。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. 对于顶层设备，请查找 Hostname 节 。 对包含 `hostname` 参数的行取消注释，并将值更新为 IoT Edge 设备的完全限定的域名 (FQDN) 或 IP 地址。 在层次结构中的设备之间一致使用你选择的任何值。

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

1. 对于 **下层** 中的任何 IoT Edge 设备，请找到“Parent hostname”节。 对包含 `parent_hostname` 参数的行取消注释，并将值更新，使之指向父设备的 FQDN 或 IP。 使用置于父设备的 hostname 字段中的确切值。 对于顶层的 IoT Edge 设备，请将此参数保持已被注释禁止。

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > 对于具有多个下层的层次结构，请将 parent_hostname 字段更新为直接上层设备的 FQDN。

1. 找到文件的“Trust bundle cert”节。 对包含 `trust_bundle_cert` 参数的行取消注释，并将值更新为指向网关层次结构中所有设备共享的根 CA 证书的文件 URI 路径。

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. 找到文件的 **Provisioning** 节。 将 **Manual provisioning with connection string** 所对应的行取消注释。 对于层次结构中的每个设备，请使用 IoT Edge 设备的连接字符串更新 **connection_string** 的值。

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. 找到 **Default Edge Agent** 节。

   * 对于 **顶层** 设备，请将 edgeAgent 映像值更新为 Azure 容器注册表中的模块的公共预览版本。
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * 对于 **下层** 中的每个 IoT Edge 设备，请更新 edgeAgent 映像，使之指向父设备，父设备后跟 API 代理所侦听的端口。 在下一部分，你将向父设备部署 API 代理模块。
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. 找到 **Edge CA certificate** 节。 将此节的前三行取消注释。 然后，更新两个参数，使之指向你在上一部分创建并移至 IoT Edge 设备的设备 CA 证书和设备 CA 私钥文件。 提供采用 `file:///<path>/<filename>` 格式的文件 URI 路径，例如 `file:///certs/iot-edge-device-ca-top-layer-device.key.pem`。

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >请确保使用 **完整链** CA 证书路径和文件名填充 `device_ca_cert` 字段。

1. 保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

1. 在配置文件中输入预配信息后，应用所做的更改：

   ```bash
   sudo iotedge config apply
   ```

继续操作之前，请确保已更新层次结构中每个设备的配置文件。 你已根据自己的层次结构配置了一个 **顶层设备**，以及一个或多个 **下层设备**。

如果已正确完成上述步骤，可以检查设备是否已正确配置。

1. 在设备上运行配置和连接性检查：

   ```bash
   sudo iotedge check
   ```

在 **顶层设备** 上，应会看到一个输出，其中包含多个“通过”评估和至少一个警告。 检查是否存在 `latest security daemon` 时，系统会发出警告，告知你另一个 IoT Edge 版本是最新的稳定版本，因为 IoT Edge 版本 1.2 是公开预览版。 可能会显示有关日志策略的其他警告，并根据你的网络的情况显示有关 DNS 策略的其他警告。

在 **下层设备** 上，应该会显示类似于顶层设备的输出，但会显示一条额外的警告，指示无法从上游拉取 EdgeAgent 模块。 这是可以接受的，因为 IoT Edge API 代理模块和 Docker 容器注册表模块（将由下层设备用来拉取映像）尚未部署到 **顶层设备**。

下面显示了 `iotedge check` 的示例输出：

[示例配置和连接性结果](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

确认每个设备上的配置都正确以后，便可继续操作。

## <a name="deploy-modules-to-the-top-layer-device"></a>将模块部署到顶层设备

模块用于完成将 IoT Edge 运行时部署到设备，并进一步对层次结构进行结构定义。 IoT Edge API 代理模块可以通过下层设备中的单个端口安全地路由 HTTP 流量。 通过将映像请求路由到顶层设备，Docker 注册表模块使得下层设备可以访问 Docker 映像的存储库。

若要将模块部署到顶层设备，可以使用 Azure 门户或 Azure CLI。

>[!NOTE]
>剩余步骤（配置 IoT Edge 运行时和部署工作负荷）不在 IoT Edge 设备上完成。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://ms.portal.azure.com/)中：

1. 导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 在设备列表中单击顶层 Edge 设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

1. 选择齿轮图标旁边的“运行时设置”。

1. 在“Edge 中心”下的映像字段中输入 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`。

   ![编辑 Edge 中心的映像](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. 将以下环境变量添加到 Edge 中心模块：

    | 名称 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![编辑 Edge 中心的环境变量](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. 在“Edge 代理”下的映像字段中输入 `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`。 选择“保存”。

1. 将 Docker 注册表模块添加到顶层设备。 选择“+ 添加”，然后从下拉列表中选择“IoT Edge 模块” 。 为 Docker 注册表模块提供名称 `registry`，并为映像 URI 输入 `registry:latest`。 接下来，添加环境变量并创建选项，以将本地注册表模块指向 Microsoft 容器注册表，以便从中下载容器映像并在 registry:5000 中提供这些映像。

1. 在“环境变量”选项卡下，输入以下环境变量名称/值对：

    | 名称 | 值 |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. 在“容器创建选项”选项卡下，输入以下 JSON：

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. 接下来，将 API 代理模块添加到顶层设备。 选择“+ 添加”，然后从下拉列表中选择“市场模块” 。 搜索 `IoT Edge API Proxy` 并选择该模块。 默认情况下，IoT Edge API 代理使用端口 8000，并被配置为使用端口 5000 上名为 `registry` 的注册表模块。

1. 依次选择“查看 + 创建”和“创建”来完成部署 。 顶层设备的 IoT Edge 运行时（可访问 Internet）将拉取并运行 IoT Edge 中心和 IoT Edge 代理的公共预览版配置。

   ![包含 Edge 中心、Edge 代理、注册表模块和 API 代理模块的完整部署](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中输入以下命令，以创建 deployment.json 文件：

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. 将以下 JSON 内容复制到 deployment.json 中，保存文件并关闭。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 输入以下命令，创建到顶层 Edge 设备的部署：

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

如果你已正确完成上述步骤，则 **顶层设备** 应该会报告以下四个模块：IoT Edge API 代理模块、Docker 容器注册表模块和两个系统模块，正如 **部署中指定的那样**。 设备接收其新部署并启动模块可能需要数分钟。 请刷新页面，直至看到温度传感器模块以“由设备报告”方式列出。 设备报告了模块后，你就可以继续操作了。

## <a name="deploy-modules-to-the-lower-layer-device"></a>将模块部署到下层设备

模块还可用作下层设备的工作负荷。 模拟温度传感器模块可创建示例遥测数据，以便通过设备层次结构提供正常的数据流。

若要将模块部署到下层设备，可以使用 Azure 门户或 Azure CLI。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://ms.portal.azure.com/)中：

1. 导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 在 IoT Edge 设备列表中单击下层设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

1. 选择齿轮图标旁边的“运行时设置”。

1. 在“Edge 中心”下的映像字段中输入 `$upstream:8000/azureiotedge-hub:1.2.0-rc4`。

1. 将以下环境变量添加到 Edge 中心模块：

    | 名称 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 在“Edge 代理”下的映像字段中输入 `$upstream:8000/azureiotedge-agent:1.2.0-rc4`。 选择“保存”。

1. 添加温度传感器模块。 选择“+ 添加”，然后从下拉列表中选择“市场模块” 。 搜索 `Simulated Temperature Sensor` 并选择该模块。

1. 在“IoT Edge 模块”下，选择刚添加的 `Simulated Temperature Sensor` 模块，并更新其映像 URI 以指向 `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`。

1. 依次选择“保存”、“查看 + 创建”和“创建”来完成部署  。

   ![包含 Edge 中心、边缘代理和模拟温度传感器的完整部署](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中输入以下命令，以创建 deployment.json 文件：

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. 将以下 JSON 内容复制到 deployment.json 中，保存文件并关闭。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 输入以下命令，创建一组到下层 Edge 设备的模块部署：

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

`azureiotedge-diagnostics` 值是从与注册表模块链接的容器注册表中拉取的。 本教程将其默认设置为 https://mcr.microsoft.com:

| 名称 | Value |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

如果使用的是专用容器注册表，请确保容器注册表中包含所有映像（例如 IoTEdgeAPIProxy、edgeAgent、edgeHub 和诊断）。

## <a name="view-generated-data"></a>查看生成的数据

你推送的模拟温度传感器模块会生成示例环境数据。 它将发送包含环境温度和湿度、机器温度和压力以及时间戳的消息。

可以使用 [Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看到达 IoT 中心的消息。

还可以通过 [Azure Cloud Shell](https://shell.azure.com/) 查看这些消息：

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>清理资源

可以删除本文中创建的本地配置和 Azure 资源，以避免收费。

若要删除资源，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 

2. 选择包含 IoT Edge 测试资源的资源组的名称。 

3. 查看包含在资源组中的资源的列表。 若要删除这一切，可以选择“删除资源组”。 如果只需删除部分内容，可以单击要单独删除的每个资源。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已将两个 IoT Edge 设备配置为网关，并设置了一个作为另一个设备的父设备。 然后，演示了如何通过网关将容器映像拉取到子设备上。

若要了解 Azure IoT Edge 如何为企业创建更多解决方案，请继续学习其他教程。

> [!div class="nextstepaction"]
> [将 Azure 机器学习模型作为一个模块部署](tutorial-deploy-machine-learning.md)
