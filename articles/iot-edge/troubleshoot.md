---
title: 故障排除 - Azure IoT Edge | Microsoft Docs
description: 通过本文了解 Azure IoT Edge 的标准诊断技能，例如检索组件状态和日志
author: kgremban
ms.author: kgremban
ms.date: 05/04/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3bca470114b5fb22409d86c333e92f93155759f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735150"
---
# <a name="troubleshoot-your-iot-edge-device"></a>排除 IoT Edge 设备故障

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

如果在环境中运行 Azure IoT Edge 时遇到问题，请将本文作为指南进行故障排除和诊断。

## <a name="run-the-check-command"></a>运行“check”命令

排查 IoT Edge 问题时，第一步应该是使用 `check` 命令，针对常见问题运行一系列配置和连接性测试。 `check` 命令在[版本 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) 及更高版本中提供。

>[!NOTE]
>如果 IoT Edge 设备位于代理服务器后面，则故障排除工具无法运行连接性检查。

可以运行 `check` 命令（如下所示），也可以包括 `--help` 标志，以便查看选项的完整列表：

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
在 Linux 上：

```bash
sudo iotedge check
```

在 Windows 上：

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

故障排除工具将运行多个检查，这些检查分为以下三个类别：

* “配置检查”将检查妨碍 IoT Edge 设备连接到云的详细情况，包括配置文件和容器引擎出现的问题。
* “连接性检查”将验证 IoT Edge 运行时能否访问主机设备上的端口，以及所有 IoT Edge 组件能否连接到 IoT 中心。 如果 IoT Edge 设备位于代理后面，则这组检查将返回错误。
* “生产准备情况检查”将寻找建议的生产最佳做法，例如设备证书颁发机构 (CA) 颁发证书的状态以及模块日志文件配置。

IoT Edge 检查工具使用容器运行其诊断。 容器映像 (`mcr.microsoft.com/azureiotedge-diagnostics:latest`)，可通过 [Microsoft 容器注册表](https://github.com/microsoft/containerregistry)获取。 如果需要在不直接访问 Internet 的情况下运行对设备的检查，你的设备需要具有对容器映像的访问权限。

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

在使用嵌套 IoT Edge 设备的方案中，可以通过路由通过父设备拉取的映像来访问子设备上的诊断映像。

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

有关此工具运行的每个诊断检查的信息，包括可在出现错误或警告时执行的操作，请参阅 [IoT Edge 排除故障检查](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)。

## <a name="gather-debug-information-with-support-bundle-command"></a>通过“support-bundle”命令收集调试信息

需要从 IoT Edge 设备收集日志时，最方便的方法是使用 `support-bundle` 命令。 默认情况下，此命令收集模块、IoT Edge 安全管理器和容器引擎日志、`iotedge check` JSON 输出和其他有用的调试信息。 它将它们压缩成单个文件，便于共享。 `support-bundle` 命令在[版本 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) 及更高版本中提供。

运行带 `--since` 标志的 `support-bundle` 命令，指定要从过去获取日志的时间。 例如，`6h` 会获取过去 6 小时的日志，`6d` 会获取过去 6 天的日志，`6m` 会获取过去 6 分钟的日志，依此类推。 包括 `--help` 标志即可查看选项的完整列表。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

在 Linux 上：

```bash
sudo iotedge support-bundle --since 6h
```

在 Windows 上：

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

默认情况下，`support-bundle` 命令在调用该命令的目录中创建名为 support_bundle.zip 的 zip 文件。 使用标志 `--output` 为输出指定其他路径或文件名。

有关命令的详细信息，请参阅其帮助信息。

```bash/cmd
iotedge support-bundle --help
```

还可以使用内置直接方法调用 [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)，将 support-bundle 命令的输出上传到 Azure Blob 存储。

> [!WARNING]
> `support-bundle` 命令的输出可能包含主机、设备和模块名称、模块记录的信息，等等。如果在公共论坛中共享输出，请注意这一点。

## <a name="review-metrics-collected-from-the-runtime"></a>查看从运行时收集的指标

IoT Edge 运行时模块会生成各种指标，帮助你监视和了解 IoT Edge 设备的运行状况。 将指标收集器模块添加到部署中，来收集这些指标并将其发送到云，从而简化监视。

有关详细信息，请参阅[收集和传输指标](how-to-collect-and-transport-metrics.md)。

## <a name="check-your-iot-edge-version"></a>检查 IoT Edge 版本

如果运行的是较旧版本的 IoT Edge，则升级可能会解决你的问题。 `iotedge check` 工具将检查 IoT Edge 安全守护程序是否是最新版本，但不会检查 IoT Edge 集中心和代理模块的版本。 若要检查设备上的运行时模块的版本，请使用 `iotedge logs edgeAgent` 和 `iotedge logs edgeHub` 命令。 模块启动时，版本号会在日志中显示。

有关如何升级设备的说明，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>验证设备上是否安装了 IoT Edge

可通过[监视 edgeAgent 模块孪生](./how-to-monitor-module-twins.md)验证设备上是否安装了 IoT Edge。

若要获取最新的 edgeAgent 模块孪生，请从 [Azure Cloud Shell](https://shell.azure.com/) 运行以下命令：

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name>
   ```

此命令将输出所有 edgeAgent [报告属性](./module-edgeagent-edgehub.md)。 以下是一些有用的监视设备状态的信息：

* 运行时状态
* 运行时开始时间
* 运行时上次退出时间
* 运行时重启计数

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>检查 IoT Edge 安全管理器的状态及其日志

[IoT Edge 安全管理器](iot-edge-security-manager.md) 负责在启动时初始化 IoT Edge 系统和预配设备等操作。 如果 IoT Edge 未启动，则安全管理器日志可能会提供有用的信息。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
在 Linux 上：

* 查看 IoT Edge 安全管理器的状态：

   ```bash
   sudo systemctl status iotedge
   ```

* 查看 IoT Edge 安全管理器的日志：

   ```bash
   sudo journalctl -u iotedge -f
   ```

* 查看 IoT Edge 安全管理器的更详细日志：

  1. 编辑 IoT Edge 守护程序设置：

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. 更新以下行：

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. 重启 IoT Edge 安全守护程序：

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

在 Windows 上：

* 查看 IoT Edge 安全管理器的状态：

   ```powershell
   Get-Service iotedge
   ```

* 查看 IoT Edge 安全管理器的日志：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* 仅查看最后 5 分钟的 IoT Edge 安全管理器日志：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* 查看 IoT Edge 安全管理器的更详细日志：

  1. 添加系统级环境变量：

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. 重启 IoT Edge 安全守护程序：

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* 查看 IoT Edge 系统服务的状态：

   ```bash
   sudo iotedge system status
   ```

* 查看 IoT Edge 系统服务的日志：

   ```bash
   sudo iotedge system logs -- -f
   ```

* 启用调试级别日志以查看更详细的 IoT Edge 系统服务日志：

  1. 启用调试级别日志。

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. 调试后切换回默认的信息级别日志。

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>检查容器日志是否有问题

IoT Edge 安全守护程序运行后，请查看容器日志以检测问题。 先查看你的已部署容器，然后查看构成 IoT Edge 运行时的容器：edgeAgent 和 edgeHub。 IoT Edge 代理日志通常提供有关每个容器的生命周期的信息。 IoT Edge 中心日志提供有关消息传送和路由的信息。

可从多个位置检索容器日志：

* 在 IoT Edge 设备上，运行以下命令以查看日志：

  ```cmd
  iotedge logs <container name>
  ```

* 在 Azure 门户上，使用内置的故障排除工具。 [在 Azure 门户中对 IoT Edge 设备进行监视和故障排除](troubleshoot-in-portal.md)

* 使用 [UploadModuleLogs 直接方法](how-to-retrieve-iot-edge-logs.md#upload-module-logs)将模块的日志上传到 Azure Blob 存储。

## <a name="clean-up-container-logs"></a>清理容器日志

默认情况下，Moby 容器引擎不会设置容器日志大小限制。 一段时间后，这可能会导致设备中填满了日志，因此出现磁盘空间不足的情况。 如果大型容器日志影响 IoT Edge 设备性能，请使用以下命令强制删除容器及其相关日志。

如果仍在进行故障排除，请等到检查容器日志后再执行此步骤。

>[!WARNING]
>如果在 edgeHub 容器有未交付的消息积压工作 (backlog) 且未设置[主机存储](how-to-access-host-storage-from-module.md)时强制删除该容器，则未交付的消息将丢失。

```cmd
docker rm --force <container name>
```

对于正在进行的日志维护和生产方案，请[施加日志大小限制](production-checklist.md#place-limits-on-log-size)。

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>查看通过 IoT Edge 中心的消息

<!--1.1 -->
:::moniker range="iotedge-2018-06"

查看通过 IoT Edge 中心的消息，并通过来自运行时容器的详细日志收集见解。 若要在这些容器上启用详细日志，请在 yaml 配置文件中设置 `RuntimeLogLevel`。 若要打开该文件，请执行以下操作：

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

默认情况下，`agent` 元素将类似于以下示例：

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

将 `env: {}` 替换为：

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 文件不能包含制表符作为缩进。 请改用 2 个空格。 顶级项不能有前导空格。

保存该文件并重启 IoT Edge 安全管理器。

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

查看通过 IoT Edge 中心的消息，并通过来自运行时容器的详细日志收集见解。 若要在这些容器上启用详细日志，请在部署清单中设置 `RuntimeLogLevel` 环境变量。

若要查看通过 IoT Edge 中心的消息，请将 edgeHub 模块的 `RuntimeLogLevel` 环境变量设置为 `debug`。

edgeHub 和 edgeAgent 模块都具有此运行时日志环境变量，默认值设置为 `info`。 此环境变量可采用以下值：

* fatal
* error
* warning
* info
* debug
* verbose

<!-- end 1.2 -->
:::moniker-end

还可以检查在 IoT 中心与 IoT 设备之间发送的消息。 使用[适用于 Visual Studio Code 的 Azure IoT 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看这些消息。 有关详细信息，请参阅 [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)（通过 Azure IoT 进行开发时的顺手工具）。

## <a name="restart-containers"></a>重启容器

在为了解信息而调查日志和消息后，可以尝试重启容器。

在 IoT Edge 设备上，使用以下命令重启模块：

```cmd
iotedge restart <container name>
```

重启 IoT Edge 运行时容器：

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

还可从 Azure 门户远程重启模块。 有关详细信息，请参阅[在 Azure 门户中对 IoT Edge 设备进行监视和故障排除](troubleshoot-in-portal.md)。

## <a name="check-your-firewall-and-port-configuration-rules"></a>检查防火墙和端口配置规则

Azure IoT Edge 允许使用支持的 IoT 中心协议从本地服务器来与 Azure 云通信，具体请参阅[选择通信协议](../iot-hub/iot-hub-devguide-protocols.md)。 为了增强安全性，Azure IoT Edge 与 Azure IoT 中心之间的信道始终配置为出站。 此配置基于[服务辅助通信模式](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)，可最大限度地减少恶意实体可探知的攻击面。 入站通信仅在特定情况下需要，其中 Azure IoT 中心需要将消息推送到 Azure IoT Edge 设备。 使用安全的 TLS 通道来保护云到设备的消息，并且可以使用 X.509 证书和 TPM 设备模块来增强其保护。 Azure IoT Edge 安全管理器控制这种通信的建立方式，具体请参阅 [IoT Edge 安全管理器](../iot-edge/iot-edge-security-manager.md)。

IoT Edge 提供增强的配置来保护 Azure IoT Edge 运行时和已部署的模块，但仍依赖于底层计算机和网络配置。 因此，必须确保设置适当的网络和防火墙规则来保护 Edge 与云之间的通信。 为托管 Azure IoT Edge 运行时的底层服务器配置防火墙规则时，可参考下表中的指导：

|协议|端口|传入|传出|指南|
|--|--|--|--|--|
|MQTT|8883|阻止（默认）|阻止（默认）|<ul> <li>使用 MQTT 作为通信协议时，请将传出（出站）端口配置为“打开”。<li>IoT Edge 不支持将端口 1883 用于 MQTT。 <li>应阻止传入（入站）连接。</ul>|
|AMQP|5671|阻止（默认）|打开（默认）|<ul> <li>IoT Edge 的默认通信协议。 <li> 如果未为其他支持的协议配置 Azure IoT Edge，或者 AMQP 是所需的通信协议，则必须将此端口配置为“打开”。<li>IoT Edge 不支持将端口 5672 用于 AMQP。<li>当 Azure IoT Edge 使用不同的受 IoT 中心支持的协议时，请阻止此端口。<li>应阻止传入（入站）连接。</ul></ul>|
|HTTPS|443|阻止（默认）|打开（默认）|<ul> <li>将传出（出站）配置为在 443 上打开以进行 IoT Edge 预配。 使用手动脚本或 Azure IoT 设备预配服务 (DPS) 时，此配置是必需的。 <li>只应针对特定的方案打开传入（入站）连接： <ul> <li>  如果透明网关中的叶设备可能发送方法请求。 在这种情况下，无需向外部网络打开端口 443，即可连接到 IoT 中心或通过 Azure IoT Edge 提供 IoT 中心服务。 因此，传入规则可限制为只能从内部网络打开传入（入站）连接。 <li> 适用于客户端到设备 (C2D) 的方案。</ul><li>IoT Edge 不支持将端口 80 用于 HTTP。<li>如果无法在企业中配置非 HTTP 协议（例如 AMQP 或 MQTT），消息可通过 WebSocket 发送。 将这种情况下，将使用端口 443 进行 WebSocket 通信。</ul>|

## <a name="next-steps"></a>后续步骤

认为在 IoT Edge 平台中发现了 bug？ [提交问题](https://github.com/Azure/iotedge/issues)，以便我们可以持续改进。

如果你还有其他问题，请创建[支持请求](https://portal.azure.com/#create/Microsoft.Support)以获取帮助。
