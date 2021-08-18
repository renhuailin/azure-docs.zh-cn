---
title: 为设备配置网络代理 - Azure IoT Edge | Microsoft Docs
description: 如何将 Azure IoT Edge 运行时和所有面向 Internet 的 IoT Edge 模块配置为通过代理服务器进行通信。
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 09e39f658d0504dc8539f24c79853e03c5555f1b
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231091"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>将 IoT Edge 设备配置为通过代理服务器进行通信

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 设备将发送 HTTPS 请求以与 IoT 中心进行通信。 如果设备已连接到使用代理服务器的网络，则需要将 IoT Edge 运行时配置为通过该服务器进行通信。 如果代理服务器发出不通过 IoT Edge 中心路由的 HTTP 或 HTTPS 请求，则可能还会影响各个 IoT Edge 模块。

本文将引导你完成以下四个步骤，来配置并管理代理服务器后面的 IoT Edge 设备：

1. [**在设备上安装 IoT Edge 运行时**](#install-iot-edge-through-a-proxy)

   IoT Edge 安装脚本从 Internet 提取包和文件，因此，设备需要通过代理服务器通信，以发出这些请求。 对于 Windows 设备，安装脚本还会提供脱机安装选项。

   此步骤是首次设置 IoT Edge 设备时对其进行配置的一次性过程。 更新 IoT Edge 运行时时，也需要使用相同的连接。

2. [在设备上配置 IoT Edge 和容器运行时](#configure-iot-edge-and-moby)

   IoT Edge 负责与 IoT 中心通信。 容器运行时负责容器管理，因此会与容器注册表通信。 这两个组件都需要通过代理服务器发出 Web 请求。

   此步骤是首次设置 IoT Edge 设备时对其进行配置的一次性过程。

3. [在设备上的配置文件中配置 IoT Edge 代理属性](#configure-the-iot-edge-agent)

   IoT Edge 守护程序最初会启动 edgeAgent 模块。 然后，edgeAgent 模块从 IoT 中心检索部署清单，并启动其他所有模块。 要使 IoT Edge 代理能够与 IoT 中心建立初始连接，请在设备本身上手动配置 edgeAgent 模块环境变量。 建立初始连接后，可以远程配置 edgeAgent 模块。

   此步骤是首次设置 IoT Edge 设备时对其进行配置的一次性过程。

4. [**针对将来的所有模块部署设置环境变量，使任何模块都可以通过代理进行通信**](#configure-deployment-manifests)

   设置 IoT Edge 设备并通过代理服务器将其连接到 IoT 中心后，需要在将来的所有模块部署中保持该连接。

   此步骤是远程执行的持续过程，目的是在每次更换新模块或更新部署后，设备仍可通过代理服务器通信。

## <a name="know-your-proxy-url"></a>知道你的代理 URL

在开始执行本文中的任何步骤之前，需要知道你的代理 URL。

代理 URL 采用以下格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol** 是 HTTP 或 HTTPS。 Docker 守护程序可以根据容器注册表设置使用任一协议，但 IoT Edge 守护程序和运行时容器应始终使用 HTTP 连接到代理。

* **proxy_host** 是代理服务器的地址。 如果代理服务器要求进行身份验证，则可采用 **user**:**password**\@**proxy_host** 格式将凭据作为 proxy_host 的一部分提供。

* **proxy_port** 是代理用来响应网络流量的网络端口。

## <a name="install-iot-edge-through-a-proxy"></a>通过代理安装 IoT Edge

无论 IoT Edge 设备是在 Windows 还是 Linux 上运行，都需要通过代理服务器访问安装包。 请据所用的操作系统，遵循相应的步骤通过代理服务器安装 IoT Edge 运行时。

### <a name="linux-devices"></a>Linux 设备

若要在 Linux 设备上安装 IoT Edge 运行时，请将包管理器配置为通过代理服务器访问安装包。 例如，[设置 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)。 配置包管理器后，请按照[安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的说明照常进行操作。

### <a name="windows-devices-using-iot-edge-for-linux-on-windows"></a>在 Windows 使用 Linux IoT Edge 的 Windows 设备

如果要在 Windows 上使用 Linux IoT Edge 安装 IoT Edge 运行时，则默认会在 Linux 虚拟机上安装 IoT Edge。 无需其他安装或更新步骤。

### <a name="windows-devices-using-windows-containers"></a>使用 Windows 容器的 Windows 设备

若要在 Windows 设备上安装 IoT Edge 运行时，需要两次通过代理服务器执行操作。 第一个连接用于下载安装程序脚本文件，第二个连接用于在安装过程中下载必需的组件。 可以在 Windows 设置中配置代理信息，或直接在 PowerShell 命令中包含代理信息。

以下步骤演示使用 `-proxy` 参数安装 Windows 的示例：

1. Invoke-WebRequest 命令需要获得代理信息才能访问安装程序脚本。 然后，Deploy-IoTEdge 命令需要获得代理信息才能下载安装文件。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Initialize-IoTEdge 命令不需经过代理服务器，因此第二步仅需 Invoke-WebRequest 的代理信息。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果拥有不能包含在 URL 中的复杂代理服务器凭据，请使用 `-InvokeWebRequestParameters` 中的 `-ProxyCredential` 参数。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

有关代理参数的详细信息，请参阅 [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 有关 Windows 安装参数的详细信息，请参阅 [Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="configure-iot-edge-and-moby"></a>配置 IoT Edge 和 Moby

IoT Edge 依赖于 IoT Edge 设备上运行的两个守护程序。 Moby 守护程序发出 Web 请求，以从容器注册表中拉取容器映像。 IoT Edge 守护程序发出 Web 请求，以与 IoT 中心进行通信。

Moby 和 IoT Edge 守护程序都需要配置为使用代理服务器持续获得设备功能。 需在最初设置设备期间，在 IoT Edge 设备上执行此步骤。

### <a name="moby-daemon"></a>Moby 守护程序

由于 Moby 是基于 Docker 的，因此若要使用环境变量配置 Moby 守护程序，请参阅 Docker 文档。 大多数容器注册表（包括 DockerHub 和 Azure 容器注册表）支持 HTTPS 请求，因此，你应当设置的变量为 **HTTPS_PROXY**。 如果要从不支持传输层安全性 (TLS) 的注册表中拉取映像，则应当设置 **HTTP_PROXY**。

选择适用于 IoT Edge 设备操作系统的文章：

* [在 Linux 上配置 Docker 守护程序](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) Linux 设备上的 Moby 守护程序保留“Docker”这一名称。
* [在 Windows 上配置 Docker 守护程序](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) Windows 设备上的 Moby 守护程序名为 iotedge-moby。 之所以让这些名称保持不同，是因为可能会在 Windows 设备上并行运行 Docker Desktop 和 Moby。

### <a name="iot-edge-daemon"></a>IoT Edge 守护程序

IoT Edge 守护程序以类似的方式配置为 Moby 守护程序。 使用以下步骤根据所使用的操作系统为服务设置环境变量。

IoT Edge 守护程序始终使用 HTTPS 将请求发送到 IoT 中心。

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

在终端中打开编辑器以配置 IoT Edge 守护程序。

```bash
sudo systemctl edit iotedge
```

输入以下文本，将 \<proxy URL> 替换为代理服务器地址和端口。 然后，保存并退出。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

刷新服务管理器以选取 IoT Edge 的新配置。

```bash
sudo systemctl daemon-reload
```

重新启动 IoT Edge 以使更改生效。

```bash
sudo systemctl restart iotedge
```

确认已创建环境变量并加载了新配置。

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

在终端中打开编辑器以配置 IoT Edge 守护程序。

```bash
sudo systemctl edit aziot-edged
```

输入以下文本，将 \<proxy URL> 替换为代理服务器地址和端口。 然后，保存并退出。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

从版本 1.2 开始，IoT Edge 使用 IoT 标识服务通过 IoT 中心或 IoT 中心设备预配服务来处理设备预配。 在终端中打开编辑器，以配置 IoT 标识服务守护程序。

```bash
sudo systemctl edit aziot-identityd
```

输入以下文本，将 \<proxy URL> 替换为代理服务器地址和端口。 然后，保存并退出。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

刷新服务管理器以选取新配置。

```bash
sudo systemctl daemon-reload
```

重启 IoT Edge 系统服务，以使对这两个守护程序的更改都生效。

```bash
sudo iotedge system restart
```

验证是否已创建环境变量并加载新配置。

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows-using-iot-edge-for-linux-on-windows"></a>在 Windows 上使用 Linux IoT Edge 的 Windows

登录到 Windows 虚拟机上的 Linux IoT Edge：

```powershell
Connect-EflowVm
```

按照上述 Linux 部分中的相同步骤配置 IoT Edge 守护程序。

#### <a name="windows-using-windows-containers"></a>使用 Windows 容器的 windows

以管理员身份打开 PowerShell 窗口，运行以下命令来使用新的环境变量编辑注册表。 将 \<proxy url> 替换为代理服务器地址和端口。

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

重新启动 IoT Edge 以使更改生效。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>配置 IoT Edge 代理

IoT Edge 代理是在任意 IoT Edge 设备上启动的第一个模块。 它首次启动是基于 IoT Edge 配置文件中的信息。 IoT Edge 代理随后连接到 IoT 中心以检索部署清单，其中声明了应在设备上部署的其他模块。

需在最初设置设备期间，在 IoT Edge 设备上执行此步骤一次。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. 打开 IoT Edge 设备上的 config.yaml 文件。 在 Linux 系统上，此文件位于 /etc/iotedge/config.yaml。 在 Windows 系统上，此文件位于 C:\ProgramData\iotedge\config.yaml。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上，请使用 `sudo` 命令，然后在偏好的文本编辑器中打开该文件。 在 Windows 上，请以管理员身份打开记事本之类的文本编辑器，然后打开该文件。

2. 在 config.yaml 文件中，找到“Edge 代理模块规范”部分。 IoT Edge 代理定义包括可以在其中添加环境变量的 **env** 参数。

3. 删除作为 env 参数占位符的大括号，并在新行上添加新变量。 请记住，YAML 中的缩进为两个空格。

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. 默认情况下，IoT Edge 运行时使用 AMQP 与 IoT 中心通信。 某些代理服务器会阻止 AMQP 端口。 如果是这种情况，则还需要将 edgeAgent 配置为使用基于 WebSocket 的 AMQP。 添加第二个环境变量。

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![具有环境变量的 edgeAgent 定义](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. 将更改保存到 config.yaml 并关闭编辑器。 重新启动 IoT Edge 以使更改生效。

   * Windows 的 Linux 和 Linux IoT Edge

      ```bash
      sudo systemctl restart iotedge
      ```

   * 使用 Windows 容器的 Windows：

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. 打开 IoT Edge 设备上的配置文件 `/etc/aziot/config.toml`。 配置文件是受保护的，因此，你需要管理权限才能对其进行访问。 在 Linux 系统上，请使用 `sudo` 命令，然后在偏好的文本编辑器中打开该文件。

2. 在该配置文件中，找到 `[agent]` 节，其中包含 edgeAgent 模块要在启动时使用的所有配置信息。 IoT Edge 代理定义包括 `[agent.env]` 子节，可以在其中添加环境变量。

3. 将 https_proxy 参数添加到环境变量部分，并将代理 URL 设置为该参数的值。

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. 默认情况下，IoT Edge 运行时使用 AMQP 与 IoT 中心通信。 某些代理服务器会阻止 AMQP 端口。 如果是这种情况，则还需要将 edgeAgent 配置为使用基于 WebSocket 的 AMQP。 将 `UpstreamProtocol` 参数取消注释。

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. 保存更改并关闭编辑器。 应用最新更改。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>配置部署清单  

将 IoT Edge 设备配置为与代理服务器配合使用后，还需要在将来的部署清单中声明 HTTPS_PROXY 环境变量。 可以使用 Azure 门户向导或者通过编辑部署清单 JSON 文件，来编辑部署清单。

始终配置两个运行时模块（edgeAgent 和 edgeHub），以通过代理服务器进行通信，从而维持与 IoT 中心的连接。 如果从 edgeAgent 模块中删除代理信息，重新建立连接的唯一方法是编辑设备上的配置文件，如上一部分所述。

除了 edgeAgent 和 edgeHub 模块外，其他模块也可能需要代理配置。 需要访问 IoT 中心以外的 Azure 资源（例如 blob 存储）的模块必须已在部署清单文件中指定 HTTPS_PROXY 变量。

以下过程在 IoT Edge 设备的整个生命周期中适用。

### <a name="azure-portal"></a>Azure 门户

使用“设置模块”向导为 IoT Edge 设备创建部署时，每个模块都有一个可在其中配置代理服务器连接的“环境变量”部分。

若要配置 IoT Edge 代理和 IoT Edge 中心模块，请在向导的第一步中选择“运行时设置”。

![配置 Edge 运行时高级设置](./media/how-to-configure-proxy-support/configure-runtime.png)

将 **https_proxy** 环境变量添加到 IoT Edge 代理和 IoT Edge 中心模块定义。 如果在 IoT Edge 设备上的配置文件中包括 UpstreamProtocol 环境变量，也请将该变量添加到 IoT Edge 代理模块定义。

![设置 https_proxy 环境变量](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

添加到部署清单的所有其他模块都遵循相同的模式。

### <a name="json-deployment-manifest-files"></a>JSON 部署清单文件

如果使用 Visual Studio Code 中的模板或通过手动创建 JSON 文件来为 IoT Edge 设备创建部署，则可以将环境变量直接添加到每个模块定义。

使用以下 JSON 格式：

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

如果包括环境变量，模块定义应类似以下 edgeHub 示例：

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": "{}"
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

如果在 IoT Edge 设备的 confige.yaml 文件中包括 **UpstreamProtocol** 环境变量，也请将其添加到 IoT Edge 代理模块定义。

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="working-with-traffic-inspecting-proxies"></a>使用流量检查代理

如果你尝试使用的代理对受 TLS 保护的连接执行流量检查，请务必注意，使用 X.509 证书的身份验证无效。 IoT Edge 建立了一个 TLS 通道，该通道使用提供的证书和密钥进行了端到端加密。 如果断开该通道以进行流量检查，则代理无法使用正确的凭据重新建立该通道，并且 IoT 中心和 IoT 中心设备预配服务会返回“`Unauthorized`”错误。

若要使用执行流量检查的代理，你必须使用共享访问签名身份验证或将 IoT 中心和 IoT 中心设备预配服务添加到允许列表中以避免检查。

## <a name="next-steps"></a>后续步骤

了解有关 [IoT Edge 运行时](iot-edge-runtime.md)的角色详细信息。

使用 [Azure IoT Edge 的常见问题和解决方法](troubleshoot.md)解决安装和配置错误
