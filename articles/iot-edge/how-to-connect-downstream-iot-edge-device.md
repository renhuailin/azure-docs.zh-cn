---
title: 连接下游 IoT Edge 设备 - Azure IoT Edge | Microsoft Docs
description: 如何将 IoT Edge 设备配置为连接到 Azure IoT Edge 网关设备。
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 2fbb03ae08d1146b51a4a73f1b2260443c1609d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722123"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>将下游 IoT Edge 设备连接到 Azure IoT Edge 网关

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

本文说明了如何在 IoT Edge 网关和下游 IoT Edge 设备之间建立信任连接。

在网关方案中，IoT Edge 设备既可以是网关，也可以是下游设备。 可以将多个 IoT Edge 网关分层，以创建设备的层次结构。 下游设备（或子设备）可以通过其网关设备（或父设备）进行身份验证以及发送或接收消息。

网关层次结构中的 IoT Edge 设备有两种不同的配置，本文对这两种配置都进行了介绍。 第一种配置是顶层 IoT Edge 设备。 当多个 IoT Edge 设备互相连接时，任何没有父设备而是直接连接到 IoT 中心的设备都会被视为顶层设备。 此设备负责处理其下的所有设备的请求。 其他配置适用于层次结构的下层中的任何 IoT Edge 设备。 这些设备可能是其他下游 IoT 和 IoT Edge 设备的网关，但也需要通过其自己的父设备路由任何通信。

某些网络体系结构只允许层次结构中的顶层 IoT Edge 设备连接到云。 在此配置中，层次结构的下层中的所有 IoT Edge 设备只能与其网关设备（或父设备）和任何下游设备（或子设备）通信。

本文中的所有步骤都基于[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)一文中的步骤，这篇文章介绍了如何将 IoT Edge 设备设置为下游 IoT 设备的网关。 同样的基本步骤适用于所有网关方案：

* **身份验证**：为网关层次结构中的所有设备创建 IoT 中心标识。
* **授权**：设置 IoT 中心内的父/子关系，以授权子设备连接到其父设备，就像连接到 IoT 中心一样。
* 网关发现：确保子设备能够在本地网络上找到其父设备。
* 安全连接：使用属于同一链的受信任证书建立安全连接。

## <a name="prerequisites"></a>先决条件

* 免费的或标准的 IoT 中心。
* 至少两个 IoT Edge 设备，一个要作为顶层设备，另外一个或多个要作为下层设备。 如果没有可用的 IoT Edge 设备，则可[在 Ubuntu 虚拟机上运行 Azure IoT Edge](how-to-install-iot-edge-ubuntuvm.md)。
* 如果使用 Azure CLI 来创建和管理设备，则必须安装了带有 Azure IoT 扩展 v0.10.6 或更高版本的 Azure CLI v2.3.1。

本文提供详细的步骤和选项，以便帮助你创建适用于你的方案的网关层次结构。 如需引导式教程，请参阅[使用网关创建 IoT Edge 设备的层次结构](tutorial-nested-iot-edge.md)。

## <a name="create-a-gateway-hierarchy"></a>创建网关层次结构

请通过为方案中的 IoT Edge 设备定义父/子关系来创建 IoT Edge 网关层次结构。 可以在创建新设备标识时设置父设备，也可以管理现有设备标识的父项和子项。

设置父/子关系的步骤可以授权子设备连接到其父设备，就像连接到 IoT 中心一样。

只有 IoT Edge 设备可以是父设备，但是 IoT Edge 设备和 IoT 设备都可以是子设备。 父设备可以有多个子设备，但子设备只能有一个父设备。 通过将父/子集链接在一起来创建网关层次结构，使一个设备的子设备是另一个设备的父设备。

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，可以在创建新设备标识时或通过编辑现有设备管理父/子关系。

创建新 IoT Edge 设备时，可以从该中心的现有 IoT Edge 设备的列表中选择父设备和子设备。

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 从导航菜单中选择“IoT Edge”。
1. 选择“添加 IoT Edge 设备”  。
1. 除了设置设备 ID 和身份验证设置，还可以选择“设置父设备”或“选择子设备”。
1. 选择要用作父设备或子设备的设备。

还可以为现有设备创建或管理父/子关系。

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 从导航菜单中选择“IoT Edge”。
1. 从 IoT Edge 设备的列表中选择要管理的设备。
1. 选择“设置父设备”或“管理子设备”。
1. 添加或删除任何父设备或子设备。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 的 [azure-iot](/cli/azure/iot) 扩展提供管理 IoT 资源的命令。 可以在创建新设备标识时或通过编辑现有设备管理 IoT 和 IoT Edge 设备的父/子关系。

[az iot hub device-identity](/cli/azure/iot/hub/device-identity) 命令集用于管理给定设备的父/子关系。

`create` 命令包含用于在创建设备时添加子设备和设置父设备的参数。

其他 device-identity 命令（包括 `add-children`、`list-children`、`remove-children`、`get-parent` 和 `set-parent`）用于管理现有设备的父/子关系。

---

>[!NOTE]
>如果要以编程方式建立父子关系，可以使用 C#、Java 或 Node.js [IoT 中心服务 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
>
>下面是使用 C# SDK [分配子设备的示例](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs)。 `RegistryManager_AddAndRemoveDeviceWithScope()` 任务演示如何以编程方式创建三层的层次结构。 IoT Edge 设备位于第一层，作为父项。 另一个 IoT Edge 设备位于第二层，同时充当父项和子项。 最后，IoT 设备处于第三层，作为最低层子设备。

## <a name="prepare-certificates"></a>准备证书

必须在同一网关层次结构中的所有设备上安装一致的证书链，才能在它们之间建立安全通信。 层次结构中的每个设备，无论是 IoT Edge 设备还是 IoT 叶设备，都需要同一根 CA 证书的副本。 然后，层次结构中的每个 IoT Edge 设备都会使用该根 CA 证书作为其设备 CA 证书的根。

使用此设置时，每个下游 IoT Edge 设备或 IoT 叶设备都可以通过验证其连接到的 edgeHub 是否具有由共享根 CA 证书签名的服务器证书，来验证其父设备的标识。

<!-- TODO: certificate graphic -->

创建以下证书：

* 根 CA 证书，是给定网关层次结构中所有设备的最顶层共享证书。 此证书安装在所有设备上。
* 要包括在根证书链中的任何中间证书。
* 设备 CA 证书及其私钥，由根证书和中间证书生成。 网关层次结构中的每个 IoT Edge 设备都需要独一无二的设备 CA 证书。

可以使用自签名证书颁发机构颁发的证书，也可以从 Baltimore、Verisign、DigiCert 或 GlobalSign 等受信任的商业证书颁发机构购买一个证书。

如果没有自己的可用证书，可以[创建用于测试 IoT Edge 设备功能的演示证书](how-to-create-test-certificates.md)。 请按该文中的步骤创建一组根证书和中间证书，然后为每个设备创建 IoT Edge 设备 CA 证书。

## <a name="configure-iot-edge-on-devices"></a>在设备上配置 IoT Edge

将 IoT Edge 设置为网关的步骤非常类似于将 IoT Edge 设置为下游设备的步骤。

若要启用网关发现，需要为每个 IoT Edge 网关设备配置 hostname，供其子设备用来在本地网络上查找它。 需要为每个下游 IoT Edge 设备配置用来连接到的 parent_hostname。 如果单个 IoT Edge 设备既是父设备又是子设备，则它需要这两个参数。

若要启用安全连接，需要为网关方案中的每个 IoT Edge 设备配置独一无二的设备 CA 证书以及由网关层次结构中的所有设备共享的根 CA 证书的副本。

你应该已在设备上安装了 IoT Edge。 如果没有，请按照以下步骤[在 IoT 中心内注册 IoT Edge 设备](how-to-register-device.md)，然后[安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)。

本部分的步骤引用了本文前面讨论的根 CA 证书与设备 CA 证书和私钥。 如果已在其他设备上创建了这些证书，请让这些证书在此设备上可用。 可通过物理方式（例如使用 U 盘）传输这些文件，还可以通过服务（例如 [Azure Key Vault](../key-vault/general/overview.md)）或功能（例如[安全文件复制](https://www.ssh.com/ssh/scp/)）这样做。

使用以下步骤在设备上配置 IoT Edge。

请确保用户“iotedge”对保存证书和密钥的目录拥有读取权限。

1. 在此 IoT Edge 设备上安装根 CA 证书。

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. 更新证书存储。

   ```bash
   sudo update-ca-certificates
   ```

   此命令应输出：已向 /etc/ssl/certs 添加一个证书。

1. 打开 IoT Edge 配置文件。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >如果设备上尚不存在配置文件，请使用以下命令基于模板文件创建该文件：
   >
   >```bash
   >sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   >```

1. 在该配置文件中找到 Hostname 节。 将包含 `hostname` 参数的行取消注释，并将值更新为 IoT Edge 设备的完全限定的域名 (FQDN) 或 IP 地址。

   此参数的值是下游设备用于连接到该网关的值。 hostname 默认使用计算机名称，但若要连接下游设备，则需使用 FQDN 或 IP 地址。

   使用少于 64 个字符的 hostname，这是服务器证书公用名称的字符限制。

   与网关层次结构中的 hostname 模式保持一致。 请使用 FQDN 或 IP 地址，但不能同时使用这二者。

1. 如果此设备是子设备，请找到“Parent hostname”节。 将 `parent_hostname` 参数取消注释并更新为父设备的 FQDN 或 IP 地址，使之与父设备的配置文件中作为主机名提供的内容匹配。

1. 找到“Trust bundle cert”节。 将 `trust_bundle_cert` 参数取消注释并更新为设备上根 CA 证书的文件 URI。

1. 验证 IoT Edge 设备在启动时将使用正确版本的 IoT Edge 代理。

   找到“Default Edge Agent”节，并验证映像值是否为 IoT Edge 版本 1.2。 如果不是，请更新：

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. 在配置文件中到“Edge CA certificate”节。 将该节中的行取消注释，并提供 IoT Edge 设备上证书和密钥文件的文件 URI 路径。

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. 保存 (`Ctrl+O`) 并关闭 (`Ctrl+X`) 该配置文件。

1. 如果以前使用过 IoT Edge 的任何其他证书，请删除以下两个目录中的文件，以确保新证书得到应用：

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. 单击“应用”以应用更改。

   ```bash
   sudo iotedge config apply
   ```

1. 检查配置中是否有任何错误。

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge 检查工具使用容器来执行某些诊断检查。 若要在下游 IoT Edge 设备上使用此工具，请确保它们可以访问 `mcr.microsoft.com/azureiotedge-diagnostics:latest`，或将容器映像置于专用容器注册表中。

## <a name="network-isolate-downstream-devices"></a>网络隔离的下游设备

本文中到目前为止的步骤可将 IoT Edge 设备设置为网关或下游设备，并在其间创建一个信任连接。 网关设备处理下游设备与 IoT 中心之间的交互，包括身份验证和消息路由。 部署到下游 IoT Edge 设备的模块仍可创建其自己的到云服务的连接。

某些网络体系结构（例如那些遵循 ISA-95 标准的网络体系结构）会尝试最大程度地减少 Internet 连接数。 在这些方案中，你可以配置没有直接 Internet 连接的下游 IoT Edge 设备。 除了通过网关设备路由 IoT 中心通信之外，下游 IoT Edge 设备还可以依赖网关设备进行所有的云连接。

此网络配置要求仅网关层次结构顶层的 IoT Edge 设备能够直接连接到云。 下层中的 IoT Edge 设备只能与其父设备或子设备通信。 网关设备上的特殊模块可启用此方案，这些模块包括：

* API 代理模块：在任何 IoT Edge 网关（在其下有另一个 IoT Edge 设备）上都是必需的。 这意味着，它必须位于网关层次结构的每个层（底层除外）。 此模块使用 [nginx](https://nginx.org) 反向代理在单个端口上通过网络层路由 HTTP 数据。 它的可配置性高（可以通过模块孪生和环境变量进行配置），因此可以根据网关方案要求对其进行调整。

* 可以在网关层次结构顶层的 IoT Edge 网关上部署 Docker 注册表模块。 此模块负责检索和缓存那些代表下层中的所有 IoT Edge 设备的容器映像。 在顶层部署此模块的替代方法是使用本地注册表，或者手动将容器映像加载到设备上，并将模块拉取策略设置为 never。

* 可以在网关层次结构顶层的 IoT Edge 网关上部署 IoT Edge 上的 Azure Blob 存储。 此模块负责上传那些代表下层中的所有 IoT Edge 设备的 blob。 上传 blob 的功能还为下层中的 IoT Edge 设备启用了有用的故障排除功能，例如模块日志上传和支持包上传。

### <a name="network-configuration"></a>网络配置

对于顶层中的每个网关设备，网络操作员需要执行以下操作：

* 提供静态 IP 地址或完全限定的域名 (FQDN)。
* 授权通过端口 443 (HTTPS) 和5671 (AMQP) 进行的从此 IP 地址到 Azure IoT 中心主机名的出站通信。
* 授权通过端口 443 (HTTPS) 进行的从此 IP 地址到 Azure 容器注册表主机名的出站通信。

  API 代理模块一次只能处理到一个容器注册表的连接。 建议拥有所有容器映像，包括 Microsoft 容器注册表 (mcr.microsoft.com) 提供的公共映像，这些映像存储在专用容器注册表中。

对于下层中的每个网关设备，网络操作员需要执行以下操作：

* 提供静态 IP 地址。
* 授权通过端口 443 (HTTPS) 和5671 (AMQP) 进行的从此 IP 地址到父网关的 IP 地址的出站通信。

### <a name="deploy-modules-to-top-layer-devices"></a>将模块部署到顶层设备

除了可以在设备上运行的任何工作负荷模块外，网关层次结构顶层的 IoT Edge 设备还有一组必须部署到其中的必需模块。

API 代理模块已经过设计，可以通过自定义来处理最常见的网关方案。 本文提供了一个以基本配置设置模块的示例。 有关详细信息和示例，请参阅[配置适用于网关层次结构方案的 API 代理模块](how-to-configure-api-proxy-module.md)。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 从导航菜单中选择“IoT Edge”。
1. 从 IoT Edge 设备的列表中选择要配置的顶层设备。
1. 选择“设置模块”。
1. 在“IoT Edge 模块”部分选择“添加”，然后选择“市场模块”  。
1. 搜索并选择“IoT Edge API 代理”模块。
1. 从已部署模块的列表中选择 API 代理模块的名称，并更新以下模块设置：
   1. 在“环境变量”选项卡中，将 NGINX_DEFAULT_PORT 的值更新为 `443`。
   1. 在“容器创建选项”选项卡中，将端口绑定更新为引用端口 443。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   这些更改将 API 代理模块配置为在端口 443 上进行侦听。 若要防止端口绑定冲突，需要将 edgeHub 模块配置为不在端口 443 上进行侦听。 改为让 API 代理模块在端口 443 上路由任何 edgeHub 流量。

1. 选择“运行时设置”并查找 edgeHub 模块创建选项。 删除端口 443 的端口绑定，保留端口 5671 和 8883 的绑定。

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 选择“保存”，将更改保存到运行时设置。
1. 再次选择“添加”，然后选择“IoT Edge 模块” 。
1. 提供以下值，以便将 Docker 注册表模块添加到你的部署：
   1. IoT Edge 模块名称：`registry`
   1. “模块设置”选项卡中的“映像 URI”：`registry:latest` 
   1. 在“环境变量”选项卡上，添加以下环境变量：

      * **名称**：`REGISTRY_PROXY_REMOTEURL` **值**：希望此注册表模块映射到的容器注册表的 URL。 例如，`https://myregistry.azurecr`。

        注册表模块只能映射到一个容器注册表，因此，建议将所有容器映像置于单个专用容器注册表中。

      * **名称**：`REGISTRY_PROXY_USERNAME` **值**：用于向容器注册表进行身份验证的用户名。

      * **名称**：`REGISTRY_PROXY_PASSWORD` **值**：用于向容器注册表进行身份验证的密码。

   1. 在“容器创建选项”选项卡上，粘贴以下内容：

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

1. 选择“添加”，将模块添加到部署中。
1. 选择“下一步:路由”以转到下一步。
1. 若要允许来自下游设备的设备到云的消息到达 IoT 中心，请包括将所有消息传递到 IoT 中心的路由。 例如：
    1. **名称**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 选择“查看 + 创建”，转到最后一步。
1. 选择“创建”以部署到设备。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中创建一个部署 JSON 文件。 例如：

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
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": "{}"
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
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

   此部署文件将 API 代理模块配置为侦听端口 443。 为防止端口绑定冲突，该文件将 edgeHub 模块配置为不侦听端口 443。 改为让 API 代理模块在端口 443 上路由任何 edgeHub 流量。

1. 输入以下命令，创建到 IoT Edge 设备的部署：

   ```azurecli
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>将模块部署到下层设备

除了可以在设备上运行的任何工作负荷模块外，网关层次结构下层的 IoT Edge 设备还有一个必须部署到其中的必需模块。

#### <a name="route-container-image-pulls"></a>路由容器映像拉取

在讨论网关层次结构中的 IoT Edge 设备所需的代理模块之前，务必了解下层中的 IoT Edge 设备如何获取其模块映像。

如果下层设备无法连接到云，但你希望它们照常拉取模块映像，则必须将网关层次结构的顶层设备配置为处理这些请求。 顶层设备需要运行一个映射到容器注册表的 Docker 注册表模块。 然后，配置 API 代理模块，以便将容器请求路由到该模块。 本文前面的部分已讨论这些详细信息。 在此配置中，下层设备不应指向云容器注册表，而应指向在顶层运行的注册表。

例如，下层设备应调用 `$upstream:443/azureiotedge-api-proxy:1.0`，而不应调用 `mcr.microsoft.com/azureiotedge-api-proxy:1.0`。

$upstream 参数指向下层设备的父设备，因此，请求会以路由的形式通过所有层，直到到达顶层，而顶层的代理环境会将容器请求路由到注册表模块。 此示例中的 `:443` 端口应替换为父设备上的 API 代理模块正在侦听的端口。

API 代理模块只能路由到一个注册表模块，每个注册表模块只能映射到一个容器注册表。 因此，下层设备需要拉取的任何映像都必须存储在单个容器注册表中。

如果不希望下层设备通过网关层次结构发出模块拉取请求，则可通过另一个选项管理本地注册表解决方案。 也可在创建部署之前将模块映像推送到设备上，然后将 imagePullPolicy 设置为 never。

#### <a name="bootstrap-the-iot-edge-agent"></a>启动 IoT Edge 代理

IoT Edge 代理是在任意 IoT Edge 设备上启动的第一个运行时组件。 需确保任何下游 IoT Edge 设备可以在启动时访问 edgeAgent 模块映像，然后可以访问部署并启动其余的模块映像。

在进入 IoT Edge 设备上的配置文件以提供设备的身份验证信息、证书和父主机名时，也请更新 edgeAgent 容器映像。

如果顶级网关设备配置为处理容器映像请求，请将 `mcr.microsoft.com` 替换为父主机名和 API 代理侦听端口。 在部署清单中，可以将 `$upstream` 用作快捷方式，但这需要 edgeHub 模块来处理路由，而该模块此时尚未启动。 例如：

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

如果使用本地容器注册表，或在设备上手动提供容器映像，请相应地更新配置文件。

#### <a name="configure-runtime-and-deploy-proxy-module"></a>配置运行时和部署代理模块

需要 API 代理模块来路由云和任何下游 IoT Edge 设备之间的所有通信。 层次结构底层中的 IoT Edge 设备（没有下游 IoT Edge 设备）不需要此模块。

API 代理模块已经过设计，可以通过自定义来处理最常见的网关方案。 本文简要介绍了以基本配置设置模块的步骤。 有关详细信息和示例，请参阅[配置适用于网关层次结构方案的 API 代理模块](how-to-configure-api-proxy-module.md)。

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 从导航菜单中选择“IoT Edge”。
1. 从 IoT Edge 设备的列表中选择要配置的下层设备。
1. 选择“设置模块”。
1. 在“IoT Edge 模块”部分选择“添加”，然后选择“市场模块”  。
1. 搜索并选择“IoT Edge API 代理”模块。
1. 从已部署模块的列表中选择 API 代理模块的名称，并更新以下模块设置：
   1. 在“模块设置”选项卡中，更新“映像 URI”的值。  将 `mcr.microsoft.com` 替换为 `$upstream:443`。
   1. 在“环境变量”选项卡中，将 NGINX_DEFAULT_PORT 的值更新为 `443`。
   1. 在“容器创建选项”选项卡中，将端口绑定更新为引用端口 443。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   这些更改将 API 代理模块配置为在端口 443 上进行侦听。 若要防止端口绑定冲突，需要将 edgeHub 模块配置为不在端口 443 上进行侦听。 改为让 API 代理模块在端口 443 上路由任何 edgeHub 流量。

1. 选择“运行时设置”。
1. 更新 edgeHub 模块设置：

   1. 在“映像”字段中，将 `mcr.microsoft.com` 替换为 `$upstream:443`。
   1. 在“创建选项”字段中，删除端口 443 的端口绑定，保留端口 5671 和 8883 的绑定。

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 更新 edgeAgent 模块设置：
   1. 在“映像”字段中，将 `mcr.microsoft.com` 替换为 `$upstream:443`。

1. 选择“保存”，将更改保存到运行时设置。
1. 在完成时选择“下一步:路由”以转到下一步。
1. 若要允许来自下游设备的设备到云的消息到达 IoT 中心，请包括将所有消息传递到 `$upstream` 的路由。 如果使用的是下层设备，则 upstream 参数指向父设备。 例如：
    1. **名称**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 选择“查看 + 创建”，转到最后一步。
1. 选择“创建”以部署到设备。

## <a name="next-steps"></a>后续步骤

[如何将 IoT Edge 设备用作网关](iot-edge-as-gateway.md)

[配置适用于网关层次结构方案的 API 代理模块](how-to-configure-api-proxy-module.md)