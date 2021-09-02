---
title: 准备在生产环境中部署解决方案 - Azure IoT Edge
description: 了解如何将 Azure IoT Edge 解决方案从开发环境转移到生产环境，包括使用适当的证书设置设备以及为将来的代码更新制定部署计划。
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 964c3f0bb346b3c2606af1227b558d06071bfe20
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728821"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>准备在生产环境中部署 IoT Edge 解决方案

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

如果已准备好将 IoT Edge 解决方案从开发环境转移到生产环境，请确保对其进行适当的配置，使其持续保持良好的性能。

本文中提供的信息并非面面俱到。 为帮助你优先处理某些任务，每个部分首先会提供一些列表，将准备工作划分为两个部分：转移到生产环境之前要实施的“重要说明”，以及需要知道的“有用提示”。 

## <a name="device-configuration"></a>设备配置

IoT Edge 设备的类型多种多样，其中包括 Raspberry Pi、便携式计算机、服务器上运行的虚拟机，等等。 可通过物理方式或虚拟连接来访问设备，而设备也有可能长时间处于隔离状态。 不管通过什么方式进行访问，都需要确保它在配置后能够正常使用。

* 重要说明
  * 安装生产证书
  * 创建设备管理计划
  * 使用 Moby 作为容器引擎

* **有用提示**
  * 选择上游协议

### <a name="install-production-certificates"></a>安装生产证书

生产环境中的每个 IoT Edge 设备上需要安装设备证书颁发机构 (CA) 证书。 然后，在配置文件中向 IoT Edge 运行时声明该 CA 证书。 对于开发和测试场景，如果配置文件中没有声明证书，则 IoT Edge 运行时会创建临时证书。 但是，这些临时证书将在三个月后过期，并且对于生产方案而言并不安全。 对于生产方案，你应该提供自己的设备 CA 证书，不管是自签名证书颁发机构颁发的证书，还是从商业证书颁发机构购买的证书。

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 目前存在一个 libiothsm 限制，会阻止使用在 2038 年 1 月 1 日或之后到期的证书。

:::moniker-end

若要了解设备 CA 证书的作用，请参阅 [Azure IoT Edge 如何使用证书](iot-edge-certs.md)。

若要详细了解如何在 IoT Edge 设备上安装证书并从配置文件引用这些证书，请参阅[在 IoT Edge 设备上管理证书](how-to-manage-device-certificates.md)。

### <a name="have-a-device-management-plan"></a>创建设备管理计划

在将任何设备投放到生产环境之前，应该知道如何管理将来的更新。 对于 IoT Edge 设备，要更新的组件列表可能包括：

* 设备固件
* 操作系统库
* 容器引擎，例如 Moby
* IoT Edge
* CA 证书

有关详细信息，请参阅[更新 IoT Edge 运行时](how-to-update-iot-edge.md)。 更新 IoT Edge 的当前方法需要通过物理方式或 SSH 方式访问 IoT Edge 设备。 若要更新许多设备，请考虑在脚本中添加更新步骤，或使用 Ansible 等自动化工具。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作为容器引擎

容器引擎是任何 IoT Edge 设备的必备组件。 生产环境中仅支持 Moby 引擎。 其他容器引擎（例如 Docker）确实也能在 IoT Edge 上正常运行，但最好是将其用于开发。 与 Azure IoT Edge 配合使用时，可以重新分配 Moby 引擎，Microsoft 将为此引擎提供服务。

### <a name="choose-upstream-protocol"></a>选择上游协议

可同时为 IoT Edge 代理和 IoT Edge 中心配置用于与 IoT 中心进行上游通信的协议（确定所用端口）。 默认协议为 AMQP，但可以根据网络设置更改协议。

两个运行时模块都包含 **UpstreamProtocol** 环境变量。 该变量的有效值为：

* MQTT
* AMQP
* MQTTWS
* AMQPWS

请在设备本身的配置文件中配置 IoT Edge 代理的 UpstreamProtocol 变量。 例如，如果 IoT Edge 设备位于阻止 AMQP 端口的代理服务器后面，则可能需要将 IoT Edge 代理配置为使用基于 WebSocket 的 AMQP (AMQPWS)，这样才能与 IoT 中心建立初始连接。

IoT Edge 设备建立连接后，请务必在将来的部署中继续为两个运行时模块配置 UpstreamProtocol 变量。 [将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

## <a name="deployment"></a>部署

* **有用提示**
  * 与上游协议保持一致
  * 为系统模块设置主机存储
  * 减少 IoT Edge 中心使用的内存空间
  * 不要使用模块映像的调试版本

### <a name="be-consistent-with-upstream-protocol"></a>与上游协议保持一致

如果将 IoT Edge 设备上的 IoT Edge 代理配置为使用其他协议而不是默认的 AMQP，则应在所有将来的部署中声明同一协议。 例如，如果 IoT Edge 设备位于阻止 AMQP 端口的代理服务器后面，则你可能已将设备配置为通过基于 WebSocket 的 AMQP (AMQPWS) 进行连接。 将模块部署到设备时，请为 IoT Edge 代理和 IoT Edge 中心配置相同的 AMQPWS 协议，否则默认的 AMQP 将会替代设置，并阻止你重新连接。

只需为 IoT Edge 代理和 IoT Edge 中心模块配置 UpstreamProtocol 环境变量即可。 其他任何模块将采用运行时模块中设置的任何协议。

[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中提供了此过程的示例。

### <a name="set-up-host-storage-for-system-modules"></a>为系统模块设置主机存储

IoT Edge 中心和代理模块使用本地存储来保留状态，并允许在模块、设备和云之间传递消息。 为了提高可靠性和性能，请将系统模块配置为使用主机文件系统上的存储。

有关详细信息，请参阅[系统模块的主机存储](how-to-access-host-storage-from-module.md)。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>减少 IoT Edge 中心使用的内存空间

如果部署的受限设备的可用内存有限，可将 IoT Edge 中心配置为以更低的容量运行，并使用更少的磁盘空间。 不过，这些配置确实会限制 IoT Edge 中心的性能，因此，请根据具体的解决方案找到适当的平衡点。

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>在受限的设备上不要进行性能优化

IoT Edge 中心默认已进行性能优化，因此它会尝试分配较大的内存区块。 在 Raspberry Pi 等小型设备上，此配置可能会影响稳定性。 如果部署的设备的资源受限，建议在 IoT Edge 中心将 **OptimizeForPerformance** 环境变量设置为 **false**。

当 **OptimizeForPerformance** 设置为 **true** 时，MQTT 协议标头将使用 PooledByteBufferAllocator（具有更佳性能，但会分配更多内存）。 分配器在 32 位操作系统或内存不足的设备上不能很好地工作。 此外，如果针对性能进行了优化，RocksDb 会为其作为本地存储提供程序的角色分配更多内存。

有关详细信息，请参阅[小型设备的稳定性问题](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)。

#### <a name="disable-unused-protocols"></a>禁用未使用的协议

优化 IoT Edge 中心的性能并减少其内存用量的另一种方法是，针对未在解决方案中使用的所有协议禁用协议头。

协议头的配置方式是在部署清单中为 IoT Edge 中心模块设置布尔环境变量。 三个变量如下：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

所有三个变量都带有两条下划线，可设置为 true 或 false。

#### <a name="reduce-storage-time-for-messages"></a>减少消息的存储时间

如果出于任何原因无法将消息传送到 IoT 中心，IoT Edge 中心模块会暂时存储消息。 可以对 IoT Edge 中心保存未送达消息的时间进行配置，该时间过后就让这些消息过期。 如果设备上的内存不足，可在 IoT Edge 中心模块孪生中减小 **timeToLiveSecs** 值。

timeToLiveSecs 参数的默认值为 7200 秒，即 2 小时。

### <a name="do-not-use-debug-versions-of-module-images"></a>不要使用模块映像的调试版本

从测试方案转移到生产方案时，请记得从部署清单中删除调试配置。 确保部署清单中没有任何模块映像带有 **\.debug** 后缀。 如果添加了 create 选项用于公开模块中的调试端口，也请删除这些 create 选项。

## <a name="container-management"></a>容器管理

* 重要说明
  * 使用标记管理版本
* **有用提示**
  * 将运行时容器存储在专用注册表中

### <a name="use-tags-to-manage-versions"></a>使用标记管理版本

标记是一个 Docker 概念，可用于区分 Docker 容器的版本。 标记是附加在容器存储库末尾的后缀（如 1.1）。 例如 mcr.microsoft.com/azureiotedge-agent:1.1。 标记是可变的，随时可能更改为指向另一容器，因此，团队应该议定一种约定，以便今后在更新模块映像时遵循。

标记还可帮助你针对 IoT Edge 设备强制实施更新。 将模块的更新版本推送到容器注册表时，请递增标记。 然后，使用递增的标记将新部署推送到设备。 容器引擎将递增的标记识别为新版本，并将最新模块版本提取到设备。

#### <a name="tags-for-the-iot-edge-runtime"></a>IoT Edge 运行时的标记

IoT Edge 代理和 IoT Edge 中心映像使用与之关联的 IoT Edge 版本进行标记。 可通过两种不同的方法对运行时映像使用标记：

* **滚动更新标记** - 仅使用版本号的前两个值来获取匹配这些数字的最新映像。 例如，每当有新版本指向最新的 1.1.x 版时，都会更新 1.1。 如果 IoT Edge 设备的容器运行时重新提取映像，则运行时模块会更新到最新版本。 Azure 门户中的部署默认使用滚动更新标记。 *建议在开发时使用此方法。*

* **特定标记** - 使用版本号的所有三个值，以显式设置映像版本。 例如，1.1.0 在其初始版本发布后不会更改。 准备好更新时，可以在部署清单中声明新的版本号。 *建议在生产环境中使用此方法。*

### <a name="store-runtime-containers-in-your-private-registry"></a>将运行时容器存储在专用注册表中

你了解如何在专用 Azure 注册表中存储自定义代码模块的容器映像，但你也可以使用它来存储公共容器映像（例如将它用于 edgeAgent 和 edgHub 运行时模块）。 如果有很严格的防火墙限制，则可能需要执行此操作，因为这些运行时容器存储在 Microsoft 容器注册表 (MCR) 中。

使用 Docker pull 命令获取映像，并将其放入专用注册表中。 请注意，你将需要使用每个新版 IoT Edge 运行时来更新映像。

| IoT Edge 运行时容器 | Docker pull 命令 |
| --- | --- |
| [Azure IoT Edge 代理](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge 中心](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

接下来，请确保在 edgeAgent 和 edgeHub 系统模块的 deployment.template.json 文件中更新映像引用。 将 `mcr.microsoft.com` 替换为这两个模块的注册表名称和服务器。

* edgeAgent：

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub：

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>网络

* **有用提示**
  * 检查出站/入站配置
  * 允许从 IoT Edge 设备进行连接
  * 配置为通过代理进行通信

### <a name="review-outboundinbound-configuration"></a>检查出站/入站配置

Azure IoT 中心与 IoT Edge 之间的信道始终配置为出站。 对于大多数 IoT Edge 方案，只需建立三个连接。 容器引擎需要连接到保存模块映像的一个或多个容器注册表。 IoT Edge 运行时需要连接到 IoT 中心，以检索设备配置信息，以及发送消息和遥测数据。 如果使用自动预配，则 IoT Edge 需要连接到设备预配服务。 有关详细信息，请参阅[防火墙和端口配置规则](troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="allow-connections-from-iot-edge-devices"></a>允许从 IoT Edge 设备进行连接

如果网络设置要求显式允许从 IoT Edge 设备建立的连接，请查看以下 IoT Edge 组件列表：

* **IoT Edge 代理** 可能通过 WebSocket 来与 IoT 中心建立持久性 AMQP/MQTT 连接。
* **IoT Edge 中心** 可能通过 WebSocket 来与 IoT 中心建立一个持久性 AMQP 连接或多个 MQTT 连接。
* IoT Edge 服务会向 IoT 中心发出间歇性 HTTPS 调用。

在所有三种情况下，DNS 名称将与 \*azure-devices.net 模式匹配。

此外，**容器引擎** 通过 HTTPS 向容器注册表发出调用。 若要检索 IoT Edge 运行时容器映像，请使用 DNS 名称 mcr.microsoft.com。 容器引擎连接到部署中配置的其他注册表。

此清单可作为防火墙规则的入手点：

   | URL（\* = 通配符） | 出站 TCP 端口 | 使用情况 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器注册表 |
   | global.azure-devices-provisioning.net  | 443 | DPS 访问（可选） |
   | \*.azurecr.io | 443 | 个人和第三方容器注册表 |
   | \*.blob.core.windows.net | 443 | 从 blob 存储下载 Azure 容器注册表映像增量 |
   | \*.azure-devices.net | 5671、8883、443 | IoT 中心访问 |
   | \*.docker.io  | 443 | Docker 中心访问（可选） |

其中一些防火墙规则继承自 Azure 容器注册表。 有关详细信息，请参阅[配置规则以访问防火墙后的 Azure 容器注册表](../container-registry/container-registry-firewall-access-rules.md)。

> [!NOTE]
> 为了在 REST 和数据终结点之间提供一致的 FQDN，从 2020 年 6 月 15 日开始，Microsoft Container Registry 数据终结点将从 `*.cdn.mscr.io` 更改为 `*.data.mcr.microsoft.com`  
> 有关详细信息，请参阅 [Microsoft Container Registry 客户端防火墙规则配置](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)

如果你不想将防火墙配置为允许访问公共容器注册表，则可以在专用容器注册表中存储映像，如[在专用注册表中存储运行时容器](#store-runtime-containers-in-your-private-registry)中所述。

### <a name="configure-communication-through-a-proxy"></a>配置为通过代理进行通信

如果要在使用代理服务器的网络中部署设备，这些设备需要能够通过该代理进行通信，这样才能访问 IoT 中心和容器注册表。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解决方案管理

* **有用提示**
  * 设置日志和诊断
  * 施加日志大小限制
  * 考虑测试和 CI/CD 管道

### <a name="set-up-logs-and-diagnostics"></a>设置日志和诊断

在 Linux 上，IoT Edge 守护程序使用日志作为默认的日志记录驱动程序。 可以使用命令行工具 `journalctl` 查询守护程序日志。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
在 Windows 上，IoT Edge 守护程序使用 PowerShell 诊断。 使用 `Get-IoTEdgeLog` 可以查询守护程序的日志。 IoT Edge 模块使用 JSON 驱动程序（默认设置）进行日志记录。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

:::moniker-end
<!-- end 1.1 -->

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

从 1.2 版开始，IoT Edge 依赖于多个守护程序。 虽然可以使用 `journalctl` 单独查询每个守护程序的日志，但若要以便捷方式查询组合日志，则应使用 `iotedge system` 命令。

* 合并的 `iotedge` 命令：

  ```bash
  sudo iotedge system logs
  ```

* 等效 `journalctl` 命令：

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

测试 IoT Edge 部署时，通常可以访问设备来检索日志和进行故障排除。 在部署方案中，可能做不到这一点。 考虑如何收集有关生产环境中设备的信息。 一种做法是使用日志记录模块从其他模块收集信息，然后将其发送到云中。 日志记录模块的一个示例是 [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)，你也可以设计自己的模块。

### <a name="place-limits-on-log-size"></a>施加日志大小限制

默认情况下，Moby 容器引擎不会设置容器日志大小限制。 一段时间后，这可能会导致设备中填满了日志，因此出现磁盘空间不足的情况。 请考虑采用以下选项来防止这种情况：

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>选项：设置应用到所有容器模块的全局限制

可以在容器引擎日志选项中限制所有容器日志文件的大小。 以下示例将日志驱动程序设置为 `json-file`（建议），并对文件的大小和数量施加限制：

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

将此信息添加（或附加）到名为 `daemon.json` 的文件，然后将该文件放到以下位置：

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
| 平台 | 位置 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* `/etc/docker/`

:::moniker-end
<!-- end 1.2 -->

必须重启容器引擎才能使更改生效。

#### <a name="option-adjust-log-settings-for-each-container-module"></a>选项：调整每个容器模块的日志设置

可在每个模块的 **createOptions** 中执行此操作。 例如：

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Linux 系统上的其他选项

* 通过将 `journald` 设置为默认的日志记录驱动程序，将容器引擎配置为向 `systemd` [日记](https://docs.docker.com/config/containers/logging/journald/)发送日志。

* 安装 logrotate 工具，以便从设备中定期删除旧日志。 使用以下文件规范：

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>考虑测试和 CI/CD 管道

为使 IoT Edge 部署方案获得最大的效率，请考虑将生产部署集成到测试和 CI/CD 管道中。 Azure IoT Edge 支持多个 CI/CD 平台，包括 Azure DevOps。 有关详细信息，请参阅[向 Azure IoT Edge 进行持续集成和持续部署](how-to-continuous-integration-continuous-deployment.md)。

## <a name="security-considerations"></a>安全注意事项

* 重要说明
  * 管理对容器注册表的访问
  * 限制容器对主机资源的访问

### <a name="manage-access-to-your-container-registry"></a>管理对容器注册表的访问

在将模块部署到生产 IoT Edge 设备之前，请务必控制对容器注册表的访问，使外部用户无法访问容器映像或对其进行更改。 使用专用容器注册表管理容器映像。

教程和其他文档会指导你在 IoT Edge 设备上使用开发计算机上所用的相同容器注册表凭据。 这些说明旨在帮助你更轻松地设置测试和开发环境，在生产方案中请勿遵照这些说明。

为了更安全地访问注册表，可以使用[身份验证选项](../container-registry/container-registry-authentication.md)。 一种建议使用的常用身份验证方法是使用 Active Directory 服务主体，该方法非常适用于应用程序或服务，它以自动或无人值守（无头）方式拉取容器映像，就像 IoT Edge 设备所做的那样。

若要创建服务主体，请按[创建服务主体](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)中所述运行两个脚本。 这些脚本执行以下任务：

* 第一个脚本创建服务主体。 它输出服务主体 ID 和服务主体密码。 将这些值安全地存储在记录中。

* 第二个脚本创建要向服务主体授予的角色分配，以后可以根据需要运行这些角色分配。 对于 `role` 参数，建议应用 acrPull 用户角色。 有关角色列表，请参阅 [Azure 容器注册表角色和权限](../container-registry/container-registry-roles.md)。

若要使用服务主体进行身份验证，请提供你通过第一个脚本获取的服务主体 ID 和密码。 在部署清单中指定这些凭据。

* 对于用户名或客户端 ID，请指定服务主体 ID。

* 对于密码或客户端机密，请指定服务主体密码。

> [!NOTE]
> 实现增强的安全身份验证后，请禁用“管理员用户”设置，以便不再提供默认的用户名/密码访问权限。 在 Azure 门户的容器注册表中，从左窗格菜单的“设置”下选择“访问密钥”。

### <a name="limit-container-access-to-host-resources"></a>限制容器对主机资源的访问

若要平衡模块间的共享主机资源，建议限制每个模块的资源消耗。 这种限制可确保一个模块不能使用过多的内存或 CPU，并防止其他进程在设备上运行。 IoT Edge 平台默认不限制模块的资源，因为如要知道以最佳方式运行给定模块的资源需求量，则需要进行测试。

Docker 提供了一些约束，可用于限制内存和 CPU 使用率等资源。 有关详细信息，请参阅[内存、CPU 和 GPU 的运行时选项](https://docs.docker.com/config/containers/resource_constraints/)。

这些约束可以通过使用部署清单中的创建选项应用于各个模块。 若要了解详细信息，请参阅[如何配置 IoT Edge 模块的容器创建选项](how-to-use-create-options.md)。

## <a name="next-steps"></a>后续步骤

* 了解有关 [IoT Edge 自动部署](module-deployment-monitoring.md)的详细信息。
* 了解 IoT Edge 如何支持[持续集成和持续部署](how-to-continuous-integration-continuous-deployment.md)。
