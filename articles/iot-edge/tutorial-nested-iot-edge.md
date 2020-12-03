---
title: 教程 - 创建 IoT Edge 设备的层次结构 - Azure IoT Edge
description: 本教程演示如何使用网关创建 IoT Edge 设备的层次结构。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 28b34ecaf51406b35c67d3838714691390f5adf7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453060"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>教程：创建 IoT Edge 设备的层次结构（预览版）

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
> * 使用 API 代理模块，通过下层设备上的单个端口安全地路由 HTTP 流量。

在本教程中，定义了以下网络层：

* **顶层**：此层的 IoT Edge 设备可直接连接到云。

* **下层**：此层的 IoT Edge 设备无法直接连接到云。 它们需要通过一个或多个中间 IoT Edge 设备来发送和接收数据。

为简单起见，本教程使用双设备层次结构。 一台设备是 topLayerDevice，表示位于层次结构顶层的设备，该设备可以直接连接到云。 此设备也称为“父设备”。 另一台设备是 lowerLayerDevice，表示位于层次结构下层的设备，该设备无法直接连接到云。 此设备也称为“子设备”。 可添加额外的下层设备来表示生产环境。 任何额外下层设备的配置都将遵循 lowerLayerDevice 的配置。

## <a name="prerequisites"></a>先决条件

若要创建 IoT Edge 设备的层次结构，你将需要：

* 一台具有 Internet 连接的计算机（Windows 或 Linux）。
* 两台要配置为 IoT Edge 设备的 Linux 设备。 如果没有可用的设备，可使用 [Azure 虚拟机](../virtual-machines/linux/index.yml)。
* 含有效订阅的 Azure 帐户。 如果还没有 [Azure 订阅](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 安装了 Azure IoT 扩展 v0.10.6 或更高版本的 Azure CLI v2.3.1。 本教程使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 如果你不熟悉 Azure Cloud Shell，[请查看快速入门以了解详细信息](./quickstart-linux.md#use-azure-cloud-shell)。

还可遵循已编写脚本的[适用于工业 IoT 的 Azure IoT Edge 示例](https://aka.ms/iotedge-nested-sample)来试用此方案，该示例将 Azure 虚拟机部署为预配置设备以模拟工厂环境。

## <a name="configure-your-iot-edge-device-hierarchy"></a>配置 IoT Edge 设备层次结构

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>创建 IoT Edge 设备的层次结构

第一步是创建 IoT Edge 设备，可通过 Azure 门户或 Azure CLI 完成。 本教程将创建只有以下两个 IoT Edge 设备的层次结构：topLayerDevice 及其子 lowerLayerDevice 。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 选择“+ 添加 IoT Edge 设备”。 此设备将为顶层设备，因此请输入适当的唯一设备 ID。 选择“保存”。

1. 再次选择“+ 添加 IoT Edge 设备”。 此设备将为 Edge 下层设备，因此请输入适当的唯一设备 ID。

1. 选择“设置父设备”，从设备列表中选择顶层设备，然后选择“确定” 。 选择“保存”。

   ![为下层设备设置父项](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中输入以下命令，以在中心创建一个 IoT Edge 设备。 此设备将为顶层设备，因此请输入适当的唯一设备 ID：

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 输入以下命令来创建子 IoT Edge 设备，并在设备之间创建父子关系：

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

请记下每个 IoT Edge 设备的连接字符串。 稍后将用到它们。

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
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   此脚本命令会创建多个证书和密钥文件，但我们将在每个 IoT Edge 设备上使用以下证书和密钥对，并在 config.yaml 文件中引用它们：

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

每个设备都需要根 CA 证书的副本，以及自己的设备 CA 证书和私钥的副本。 可使用 U 盘或[安全文件复制](https://www.ssh.com/ssh/scp/)将证书移动到每个设备上。

1. 传输证书后，为每个设备安装根 CA。

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   此命令应输出：已在 /etc/ssl/certs 中添加了一个证书。

### <a name="install-iot-edge-on-the-devices"></a>在设备上安装 IoT Edge

在两个设备上执行以下步骤来安装 IoT Edge。

1. 更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

1. 安装 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

1. 安装 hsmlib 和 IoT Edge 守护程序 <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/libiothsm-std_1.2.0.rc2-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/iotedge_1.2.0_rc2-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>配置 IoT Edge 运行时

在两个设备上执行以下步骤来配置 IoT Edge 运行时。 为设备配置 IoT Edge 运行时包含四个步骤，所有步骤均通过编辑 IoT Edge 配置文件来完成：

1. 通过将设备的连接字符串添加到配置文件，手动预配每个设备。

1. 通过将配置文件指向设备 CA 证书、设备 CA 私钥和根 CA 证书，完成设备证书的设置。

1. 使用 IoT Edge 代理启动系统。

1. 更新顶层设备的 hostname 参数，并更新下层设备的 hostname 参数和 parent_hostname 参数    。

完成这些步骤，然后重启 IoT Edge 服务来配置设备。

1. 在每个设备上，打开 IoT Edge 配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 找到文件的预配配置，并取消注释“使用连接字符串手动预配配置”部分。

1. 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 请确保注释掉任何其他预配部分。请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

1. 找到“证书”部分。 取消注释并更新三个证书字段，使这些字段指向你在上一节中创建并移动到 IoT Edge 设备的证书。 提供采用 `file:///<path>/<filename>` 格式的文件 URI 路径。

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. 对于顶层设备，查找 hostname 参数 。 将值更新为 IoT Edge 设备的完全限定的域名 (FQDN) 或 IP 地址。 在层次结构中的设备之间一致使用你选择的任何值。

   ```yml
   hostname: <device fqdn or IP>
   ```

1. 对于下层的 IoT Edge 设备，请更新配置文件以指向父设备的 FQDN 或 IP，与父设备 hostname 字段中的内容相匹配 。 对于顶层的 IoT Edge 设备，请注释禁止此参数。

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > 对于具有多个下层的层次结构，请将 parent_hostname 字段更新为直接上层设备的 FQDN。

1. 对于顶层设备，查找“代理”yaml 部分，并将映像值更新为 IoT Edge 代理的正确版本 。 在本例中，我们会将顶层 IoT Edge 代理指向 Azure 容器注册表，其中提供 IoT Edge 代理映像的公共预览版。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 对于下层的 IoT Edge 设备，请更新映像值的域名以指向父设备的 FQDN 或 IP，后跟 API 代理端口 8000。 你将在下一节中添加 API 代理模块。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

1. 在配置文件中输入预配信息后，重启守护程序：

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>将模块部署到顶层设备

可通过 Azure 门户或 Azure CLI，在云中完成 IoT Edge 运行时的配置和部署工作负载的剩余步骤。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://ms.portal.azure.com/)中：

1. 导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 在设备列表中单击顶层 Edge 设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

1. 选择齿轮图标旁边的“运行时设置”。

1. 在“Edge 中心”下的映像字段中输入 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2`。

   ![编辑 Edge 中心的映像](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. 将以下环境变量添加到 Edge 中心模块：

    | 名称 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![编辑 Edge 中心的环境变量](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. 在“Edge 代理”下的映像字段中输入 `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2`。 选择“保存”。

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
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
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
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
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

## <a name="deploy-modules-to-the-lower-layer-device"></a>将模块部署到下层设备

可使用 Azure 门户和 Azure CLI 将工作负载从云部署到下层设备。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://ms.portal.azure.com/)中：

1. 导航到 IoT 中心。

1. 从左窗格菜单中的“自动设备管理”下，选择“IoT Edge” 。

1. 在 IoT Edge 设备列表中单击下层设备的设备 ID。

1. 在上方栏中，选择“设置模块”。

1. 选择齿轮图标旁边的“运行时设置”。

1. 在“Edge 中心”下的映像字段中输入 `$upstream:8000/azureiotedge-hub:1.2.0-rc2`。

1. 将以下环境变量添加到 Edge 中心模块：

    | 名称 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 在“Edge 代理”下的映像字段中输入 `$upstream:8000/azureiotedge-agent:1.2.0-rc2`。 选择“保存”。

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
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
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

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

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

在本教程中，你已将两个 IoT Edge 设备配置为网关，并设置了一个作为另一个设备的父设备。 然后，演示了如何通过网关将容器映像拉取到子设备上。 还可遵循已编写脚本的[适用于工业 IoT 的 Azure IoT Edge 示例](https://aka.ms/iotedge-nested-sample)来试用此方案，该示例将 Azure 虚拟机部署为预配置设备以模拟工厂环境。

若要了解 Azure IoT Edge 如何为企业创建更多解决方案，请继续学习其他教程。

> [!div class="nextstepaction"]
> [将 Azure 机器学习模型作为一个模块部署](tutorial-deploy-machine-learning.md)