---
title: 连接下游 IoT Edge 设备-Azure IoT Edge |Microsoft Docs
description: 如何将 IoT Edge 设备配置为连接到 Azure IoT Edge 网关设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 37c237cdaf6c0d4f766d4b2e39c10e3e96215463
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187827"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>将下游 IoT Edge 设备连接到 Azure IoT Edge 网关 (预览) 

本文介绍如何在 IoT Edge 网关和下游 IoT Edge 设备之间建立可信连接。

>[!NOTE]
>此功能要求运行 Linux 容器的 IoT Edge 1.2 版本，该版本为公共预览版。

在网关方案中，IoT Edge 设备既可以是网关，也可以是下游设备。 可以将多个 IoT Edge 网关分层，以创建设备的层次结构。 下游 (或子) 设备可以通过其网关 (或父) 设备进行身份验证和发送或接收消息。

网关层次结构中 IoT Edge 设备有两种不同的配置，本文同时提供这两种配置。 第一层是 IoT Edge 设备的 **顶层** 。 当多个 IoT Edge 设备彼此连接时，任何没有父设备但直接连接到 IoT 中心的设备都被视为位于顶层。 此设备负责处理来自其下的所有设备的请求。 其他配置适用于层次结构中 **较低层** 的任何 IoT Edge 设备。 这些设备可能是其他下游 IoT 和 IoT Edge 设备的网关，还需要通过其自己的父设备路由任何通信。

某些网络体系结构要求仅层次结构中的顶层 IoT Edge 设备可以连接到云。 在此配置中，层次结构的较低层中的所有 IoT Edge 设备只能与其网关 (或父) 设备以及任何下游 (或子) 设备通信。

本文中的所有步骤都是在 [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)的情况下构建的，这会将 IoT Edge 设备设置为下游 IoT 设备的网关。 相同的基本步骤适用于所有网关方案：

* **身份验证**：为网关层次结构中的所有设备创建 IoT 中心标识。
* **授权**：设置 IoT 中心内的父/子关系，授权子设备连接到其父设备，如连接到 IoT 中心。
* **网关发现**：确保子设备能够在本地网络上找到其父设备。
* **安全连接**：使用属于同一链的受信任证书建立安全连接。

## <a name="prerequisites"></a>先决条件

* 免费或标准 IoT 中心。
* 至少两个 **IoT Edge 设备**，一个设备是顶层设备，一个或多个较低层设备。 如果没有可用 IoT Edge 设备，则可以 [在 Ubuntu 虚拟机上运行 Azure IoT Edge](how-to-install-iot-edge-ubuntuvm.md)。
* 如果使用 Azure CLI 来创建和管理设备，请在安装了 Azure IoT extension 0.10.6 或更高版本的情况下使用 Azure CLI 的版本 v4.0。

本文提供详细的步骤和选项，可帮助你为方案创建适当的网关层次结构。 有关指导教程，请参阅 [使用网关创建 IoT Edge 设备的层次结构](tutorial-nested-iot-edge.md)。

## <a name="create-a-gateway-hierarchy"></a>创建网关层次结构

通过为方案中的 IoT Edge 设备定义父/子关系，创建 IoT Edge 网关层次结构。 你可以在创建新的设备标识时设置父设备，也可以管理现有设备标识的父项和子项。

设置父/子关系的步骤授权子设备连接到其父设备，如连接到 IoT 中心。

只有 IoT Edge 设备可以是父设备，但是 IoT Edge 设备和 IoT 设备都可以是子级。 父项可以有多个子项，但子级只能有一个父级。 通过将父/子集链接在一起来创建网关层次结构，以便一个设备的子级是另一个设备的父项。

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，你可以在创建新的设备标识或编辑现有设备时管理父/子关系。

创建新 IoT Edge 设备时，可以选择从该集线器中现有 IoT Edge 设备的列表中选择 "父" 和 "子设备"。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心。
1. 从导航菜单中选择 " **IoT Edge** "。
1. 选择“添加 IoT Edge 设备”  。
1. 除了设置设备 ID 和身份验证设置，还可以 **设置父设备** 或选择 " **子设备**"。
1. 选择要作为父项或子节点的一个或一些设备。

你还可以创建或管理现有设备的父/子关系。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心。
1. 从导航菜单中选择 " **IoT Edge** "。
1. 从 **IoT Edge 设备** 的列表中选择要管理的设备。
1. 选择 " **设置父设备** " 或 " **管理子设备**"。
1. 添加或删除任何父或子设备。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

适用于 Azure CLI 的 [azure iot](/cli/azure/ext/azure-iot) 扩展提供了用于管理 iot 资源的命令。 你可以在创建新的设备标识或编辑现有设备时管理 IoT 和 IoT Edge 设备的父/子关系。

[Az iot 中心设备标识](/cli/azure/ext/azure-iot/iot/hub/device-identity)命令集使你可以管理给定设备的父/子关系。

此 `create` 命令包含用于在设备创建时添加子设备和设置父设备的参数。

其他设备标识命令（包括 `add-children` 、 `list-children` 、和 `remove-children` `get-parent` `set-parent` ）允许你管理现有设备的父/子关系。

---

## <a name="prepare-certificates"></a>准备证书

必须在同一网关层次结构中的所有设备上安装一致的证书链，才能在它们之间建立安全通信。 无论是 IoT Edge 设备还是 IoT 叶设备，层次结构中的每个设备都需要相同的根 CA 证书的副本。 然后，层次结构中的每个 IoT Edge 设备都会使用该根 CA 证书作为其设备 CA 证书的根。

使用此设置时，每个下游 IoT Edge 设备或 IoT 叶设备可以通过验证其连接到的 edgeHub 是否具有由共享根 CA 证书签名的服务器证书来验证其父项的标识。

<!-- TODO: certificate graphic -->

创建以下证书：

* **根 CA 证书**，是指定网关层次结构中所有设备的最顶层共享证书。 此证书安装在所有设备上。
* 要包括在根证书链中的任何 **中间证书** 。
* 由根证书和中间证书生成的 **设备 CA 证书** 及其 **私钥**。 网关层次结构中的每个 IoT Edge 设备都需要一个唯一的设备 CA 证书。

>[!NOTE]
>目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

你可以使用自签名证书颁发机构，也可以从巴尔的摩、Verisign、Digicert 或 GlobalSign 等受信任的商业证书颁发机构购买证书颁发机构。

如果还没有要使用的证书，可以 [创建演示证书来测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。 按照该文章中的步骤创建一组根证书和中间证书，然后为每个设备创建 IoT Edge 设备 CA 证书。

## <a name="configure-iot-edge-on-devices"></a>在设备上配置 IoT Edge

将 IoT Edge 设置为网关的步骤非常类似于将 IoT Edge 设置为下游设备的步骤。

若要启用网关发现，需要将每个 IoT Edge 网关设备配置为其子设备将用于在本地网络上查找它的 **主机名** 。 需要使用 **parent_hostname** 来配置每个下游 IoT Edge 设备，以便连接到。 如果单个 IoT Edge 设备同时为父设备和子设备，则它需要这两个参数。

若要启用安全连接，需要为网关方案中的每个 IoT Edge 设备配置一个唯一的设备 CA 证书以及网关层次结构中的所有设备共享的根 CA 证书的副本。

你的设备上已安装 IoT Edge。 如果没有，请按照以下步骤 [安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md) ，然后通过 [对称密钥身份验证](how-to-manual-provision-symmetric-key.md) 或 [x.509 证书身份验证](how-to-manual-provision-x509.md)设置设备。

本部分中的步骤引用了本文前面部分介绍的 **根 CA 证书** 和 **设备 ca 证书和私钥** 。 如果在其他设备上创建了这些证书，请在此设备上使用这些证书。 可以物理方式传输文件，如使用 USB 驱动器、使用 [Azure Key Vault](../key-vault/general/overview.md)之类的服务，或者使用 [安全文件复制](https://www.ssh.com/ssh/scp/)等功能。

使用以下步骤在设备上配置 IoT Edge。

在 Linux 上，确保用户 **iotedge** 具有保存证书和密钥的目录的读取权限。

1. 在此 IoT Edge 设备上安装 **根 CA 证书** 。

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. 更新证书存储区。

   ```bash
   sudo update-ca-certificates
   ```

   此命令应输出一个证书已添加到/etc/ssl/certs。

1. 打开 IoT Edge 安全守护程序配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 查找 yaml 文件中的 " **证书** " 部分。 更新三个证书字段，使其指向证书。 提供采用 `file:///<path>/<filename>` 格式的文件 URI 路径。

   * **device_ca_cert**：此设备独有的设备 ca 证书的文件 URI 路径。
   * **device_ca_pk**：此设备独有的设备 ca 私钥的文件 URI 路径。
   * **trusted_ca_certs**：网关层次结构中所有设备共享的根 ca 证书的文件 URI 路径。

1. 查找 yaml 文件中的 **hostname** 参数。 将主机名更新为完全限定的域名 (FQDN) 或 IoT Edge 设备的 IP 地址。

   此参数的值是下游设备将用于连接到该网关的值。 默认情况下，主机名采用计算机名称，但需要使用 FQDN 或 IP 地址连接下游设备。

   使用少于64个字符的主机名，这是服务器证书公用名的字符限制。

   通过网关层次结构与主机名模式保持一致。 使用 Fqdn 或 IP 地址，但不能同时使用两者。

1. **如果此设备是子设备**，请查找 **parent_hostname** 参数。 将 **parent_hostname** 字段更新为父设备的 FQDN 或 IP 地址，并将提供的所有内容与父设备的 yaml 文件中的主机名匹配。

1. 此功能处于公共预览状态时，需要将 IoT Edge 设备配置为在启动时使用 IoT Edge 代理的公共预览版。

   找到 **代理** yaml 部分，并将 image 值更新为公共预览图像：

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 保存 (`Ctrl+O`) 并关闭 (`Ctrl+X`) yaml 文件。

1. 如果以前已使用任何其他证书用于 IoT Edge，请删除以下两个目录中的文件，以确保新证书得以应用：

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. 重新启动 IoT Edge 服务，以应用所做的更改。

   ```bash
   sudo systemctl restart iotedge
   ```

1. 检查配置中是否有任何错误。

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge 检查工具使用容器来执行某些诊断检查。 如果要在下游 IoT Edge 设备上使用此工具，请确保它们可以访问 `mcr.microsoft.com/azureiotedge-diagnostics:latest` ，或在专用容器注册表中具有容器映像。

## <a name="configure-runtime-modules-for-public-preview"></a>配置公共预览版的运行时模块

此功能处于公共预览模式时，需要将 IoT Edge 设备配置为使用 IoT Edge 运行时模块的公共预览版。 上一部分提供了在启动时配置 edgeAgent 的步骤。 还需要在设备的部署中配置运行时模块。

1. 将 edgeHub 模块配置为使用公共预览图像： `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` 。

1. 为 edgeHub 模块配置以下环境变量：

   | 名称 | 值 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 将 edgeAgent 模块配置为使用公共预览图像： `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` 。

## <a name="network-isolate-downstream-devices"></a>网络隔离下游设备

本文中到目前为止的步骤将 IoT Edge 设备设置为网关或下游设备，并在它们之间创建一个可信连接。 网关设备处理下游设备与 IoT 中心之间的交互，包括身份验证和消息路由。 部署到下游 IoT Edge 设备的模块仍可创建自己与云服务的连接。

某些网络体系结构与遵循 ISA-95 标准的网络体系结构进行搜索，以最大程度地减少 internet 连接的数量。 在这些情况下，你可以配置下游 IoT Edge 设备，而无需直接连接 internet。 除了通过其网关设备路由 IoT 中心通信之外，下游 IoT Edge 设备可依赖于所有云连接的网关设备。

此网络配置要求网关层次结构的顶层只有 IoT Edge 设备具有与云的直接连接。 在较低层中 IoT Edge 设备只能与其父设备或任何子设备通信。 网关设备上的特殊模块启用此方案，其中包括：

* **API 代理模块** 在任何在其下有另一个 IoT Edge 设备的 IoT Edge 网关上都是必需的。 也就是说，它必须位于网关层次结构的 *每个层* 上，底部层除外。 此模块使用 [nginx](https://nginx.org) 反向代理通过单个端口上的网络层路由 HTTP 数据。 它通过其模块克隆和环境变量可高度配置，因此可以进行调整以适应网关方案要求。

* 可在网关层次结构的 *顶层* 将 **Docker 注册表模块** 部署到 IoT Edge 网关。 此模块负责代表较低层中的所有 IoT Edge 设备检索和缓存容器映像。 在顶层部署此模块的替代方法是使用本地注册表，或者手动将容器映像加载到设备上，并将模块请求策略设置为 " **从不**"。

* 可在网关层次结构 *顶部* 的 IoT Edge 网关上部署 **IoT Edge 上的 Azure Blob 存储**。 此模块负责代表较低层中的所有 IoT Edge 设备上传 blob。 上传 blob 的功能还为更低层中的 IoT Edge 设备启用了有用的疑难解答功能，如模块日志上传和支持捆绑包上传。

### <a name="network-configuration"></a>网络配置

对于上图中的每个网关设备，网络操作员需要：

*  (FQDN) 提供静态 IP 地址或完全限定的域名。
* 通过端口 443 (HTTPS) 和 5671 (AMQP) ，授权从此 IP 地址到 Azure IoT 中心主机名的出站通信。
* 通过端口 443 (HTTPS) ，授权从此 IP 地址到 Azure 容器注册表主机名的出站通信。

  API 代理模块一次只能处理到一个容器注册表的连接。 建议将所有容器映像（包括 Microsoft 容器注册表提供的公共映像） (mcr.microsoft.com) ，存储在专用容器注册表中。

对于更低层中的每个网关设备，网络操作员需要：

* 提供静态 IP 地址。
* 通过端口 443 (HTTPS) 和 5671 (AMQP) ，授权从该 IP 地址到父网关的 IP 地址的出站通信。

### <a name="deploy-modules-to-top-layer-devices"></a>将模块部署到顶层设备

除了可以在设备上运行的任何工作负荷模块外，网关层次结构顶部的 IoT Edge 设备还包含一组必需的模块。

API 代理模块设计用于处理最常见的网关方案。 本文提供了有关如何在基本配置中设置模块的示例。 有关更多详细信息和示例，请参阅 [配置网关层次结构的 API 代理模块方案](how-to-configure-api-proxy-module.md) 。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心。
1. 从导航菜单中选择 " **IoT Edge** "。
1. 从 **IoT Edge 设备** 的列表中选择要配置的顶层设备。
1. 选择“设置模块”。
1. 在 **IoT Edge 模块** "部分中，选择" **添加** "，然后选择" **Marketplace 模块**"。
1. 搜索并选择 " **IOT EDGE API 代理** " 模块。
1. 从已部署的模块列表中选择 API 代理模块的名称，并更新以下模块设置：
   1. 在 " **环境变量** " 选项卡中，将 **NGINX_DEFAULT_PORT** 的值更新为 `443` 。
   1. 在容器的 " **创建选项** " 选项卡中，更新端口绑定以引用端口443。

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

   这些更改将 API 代理模块配置为侦听端口443。 若要防止端口绑定冲突，需要将 edgeHub 模块配置为不在端口443上侦听。 相反，API 代理模块会路由端口443上的任何 edgeHub 流量。

1. 选择 " **运行时设置** " 并查找 edgeHub 模块创建选项。 删除端口443的端口绑定，并为端口5671和8883保留绑定。

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

1. 选择 " **保存** " 以保存对运行时设置所做的更改。
1. 再次选择 " **添加** "，然后选择 " **IoT Edge 模块**"。
1. 提供以下值以将 Docker 注册表模块添加到你的部署：
   1. **IoT Edge 模块名称**： `registry`
   1. 在 " **模块设置** " 选项卡上， **图像 URI**： `registry:latest`
   1. 在 " **环境变量** " 选项卡上，添加以下环境变量：

      * **名称**： `REGISTRY_PROXY_REMOTEURL` **值**：你希望此注册表模块映射到的容器注册表的 URL。 例如，`https://myregistry.azurecr`。

        注册表模块只能映射到一个容器注册表，因此建议将所有容器映像置于单个专用容器注册表中。

      * **名称**： `REGISTRY_PROXY_USERNAME` **值**：用于对容器注册表进行身份验证的用户名。

      * **名称**： `REGISTRY_PROXY_PASSWORD` **值**：用于对容器注册表进行身份验证的密码。

   1. 在 **容器 "创建选项** " 选项卡上，粘贴：

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

1. 选择 " **添加** "，将模块添加到部署中。
1. 选择 " **下一步"：路由** 到下一步。
1. 若要允许来自下游设备的设备到云的消息到达 IoT 中心，请包含将所有消息传递到 IoT 中心的路由。 例如：
    1. **名称**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 选择 " **查看 + 创建** " 以前往最后一步。
1. 选择 " **创建** " 以部署到设备。

### <a name="deploy-modules-to-lower-layer-devices"></a>将模块部署到较低的层设备

除了可以在设备上运行的任何工作负荷模块外，网关层次结构的较低层中的 IoT Edge 设备还必须部署一个必需的模块。

#### <a name="route-container-image-pulls"></a>路由容器映像提取

在为网关层次结构中的 IoT Edge 设备讨论所需的代理模块之前，请务必了解更低层中 IoT Edge 设备如何获取其模块映像。

如果下层设备无法连接到云，但你希望它们像往常一样请求模块映像，则必须将网关层次结构的顶层设备配置为处理这些请求。 顶层设备需要运行映射到容器注册表的 Docker **注册表** 模块。 然后，配置 API 代理模块，将容器请求路由到该模块。 本文前面的部分将讨论这些详细信息。 在此配置中，低层设备不应指向云容器注册表，而是指向顶层运行的注册表。

例如， `mcr.microsoft.com/azureiotedge-api-proxy:latest` 较低的层设备应该调用，而不是调用 `$upstream:443/azureiotedge-api-proxy:latest` 。

**$Upstream** 参数指向较低层设备的父级，因此，请求将路由到所有层，直到它到达将代理环境路由容器请求到注册表模块的顶层。 `:443`应将此示例中的端口替换为父设备上的 API 代理模块正在侦听的端口。

API 代理模块只能路由到一个注册表模块，每个注册表模块只能映射到一个容器注册表。 因此，下层设备需要请求的任何映像都必须存储在单个容器注册表中。

如果你不希望较低层设备通过网关层次结构进行模块拉取请求，另一个选项是管理本地注册表解决方案。 或者，在创建部署之前将模块映像推送到设备上，然后将 **imagePullPolicy** 设置为 " **从不**"。

#### <a name="bootstrap-the-iot-edge-agent"></a>启动 IoT Edge 代理

IoT Edge 代理是要在任何 IoT Edge 设备上启动的第一个运行时组件。 你需要确保任何下游 IoT Edge 设备在启动时可以访问 edgeAgent 模块映像，然后他们可以访问部署并启动其余的模块映像。

进入 IoT Edge 设备上的 yaml 文件以提供其身份验证信息、证书和父主机名后，还会更新 edgeAgent 容器映像。

如果顶级网关设备配置为处理容器映像请求，请将替换 `mcr.microsoft.com` 为父主机名和 API 代理侦听端口。 在部署清单中，可以将 `$upstream` 用作快捷方式，但这需要 edgeHub 模块来处理路由，并且该模块此时未启动。 例如：

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc2"
    auth: {}
```

如果你使用的是本地容器注册表，或在设备上手动提供容器映像，请相应地更新 yaml 文件。

#### <a name="configure-runtime-and-deploy-proxy-module"></a>配置运行时和部署代理模块

**API 代理模块** 是路由云和任何下游 IoT Edge 设备之间的所有通信所必需的。 层次结构底部的 IoT Edge 设备（没有下游 IoT Edge 设备）不需要此模块。

API 代理模块设计用于处理最常见的网关方案。 本文简要介绍了在基本配置中设置模块的步骤。 有关更多详细信息和示例，请参阅 [配置网关层次结构的 API 代理模块方案](how-to-configure-api-proxy-module.md) 。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心。
1. 从导航菜单中选择 " **IoT Edge** "。
1. 从 **IoT Edge 设备** 的列表中选择要配置的下层设备。
1. 选择“设置模块”。
1. 在 **IoT Edge 模块** "部分中，选择" **添加** "，然后选择" **Marketplace 模块**"。
1. 搜索并选择 " **IOT EDGE API 代理** " 模块。
1. 从已部署的模块列表中选择 API 代理模块的名称，并更新以下模块设置：
   1. 在 " **模块设置** " 选项卡中，更新 " **映像 URI**" 的值。 将 `mcr.microsoft.com` 替换为 `$upstream:443`。
   1. 在 " **环境变量** " 选项卡中，将 **NGINX_DEFAULT_PORT** 的值更新为 `443` 。
   1. 在容器的 " **创建选项** " 选项卡中，更新端口绑定以引用端口443。

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

   这些更改将 API 代理模块配置为侦听端口443。 若要防止端口绑定冲突，需要将 edgeHub 模块配置为不在端口443上侦听。 相反，API 代理模块会路由端口443上的任何 edgeHub 流量。

1. 选择“运行时设置”。
1. 更新 edgeHub 模块设置：

   1. 在 " **映像** " 字段中，将替换 `mcr.microsoft.com` 为 `$upstream:443` 。
   1. 在 " **创建选项** " 字段中，删除端口443的端口绑定，并为端口5671和8883保留绑定。

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
   1. 在 " **映像** " 字段中，将替换 `mcr.microsoft.com` 为 `$upstream:443` 。

1. 选择 " **保存** " 以保存对运行时设置所做的更改。
1. 选择 " **下一步"：路由** 到下一步。
1. 若要允许来自下游设备的设备到云的消息到达 IoT 中心，请包含将所有消息传递到的路由 `$upstream` 。 如果下层设备，上游参数会指向父设备。 例如：
    1. **名称**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 选择 " **查看 + 创建** " 以前往最后一步。
1. 选择 " **创建** " 以部署到设备。

## <a name="next-steps"></a>后续步骤

[如何将 IoT Edge 设备用作网关](iot-edge-as-gateway.md)

[为网关层次结构方案配置 API 代理模块](how-to-configure-api-proxy-module.md)