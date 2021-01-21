---
title: 安装适用于 Windows 的 Azure IoT Edge |Microsoft Docs
description: 在 Windows 设备上安装 Windows 容器 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
ms.openlocfilehash: 7857f93e8c767f270041bb6bf041447786ce19ff
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633759"
---
# <a name="install-and-manage-azure-iot-edge-for-windows"></a>安装和管理适用于 Windows 的 Azure IoT Edge

适用于 Windows 的 Azure IoT Edge 直接在主机 Windows 设备上运行，并使用 Windows 容器在边缘运行业务逻辑。

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

>[!NOTE]
>从版本1.2.0 的 Azure IoT Edge 开始，将不支持 Windows Azure IoT Edge。
>
>请考虑使用新方法在 Windows 设备上运行 IoT Edge，在 windows 上 Azure IoT Edge Linux。

<!-- TODO: link to EFLOW-->

设置 IoT Edge 设备的过程包括两个步骤。 第一步是安装运行时及其依赖项。 第二步是将设备连接到其在云中的标识，并使用 IoT 中心设置身份验证。

本文列出了在 Windows 设备上安装 Azure IoT Edge 运行时的步骤。 安装运行时时，可以选择使用 Linux 容器或 Windows 容器。 目前，生产方案仅支持 Windows 上的 Windows 容器。 Windows 上的 Linux 容器对于开发和测试方案非常有用，尤其是在 Windows 电脑上进行开发以部署到 Linux 设备时。

## <a name="prerequisites"></a>先决条件

* Windows 设备

  带有 Windows 容器的 IoT Edge 需要 Windows 版本 1809/内部版本 17762，这是最新的 [Windows 长期支持版本](/windows/release-information/)。 对于开发和测试方案，支持容器功能的任何 SKU（Pro、Enterprise、Server 等）都将起作用。 但是，在转到生产环境之前，请务必查看 [受支持的系统列表](support.md#operating-systems) 。

  带有 Linux 容器的 IoT Edge 可在满足 [Docker Desktop 要求](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)的任何 Windows 版本上运行。

* 设备上的容器支持

  Azure IoT Edge 依赖于 [OCI 兼容](https://www.opencontainers.org/)的容器引擎。 确保你的设备可支持容器。

  若要在虚拟机上安装 IoT Edge，请启用嵌套虚拟化并分配至少 2 GB 的内存。 就 Hyper-V 来说，第 2 代虚拟机已默认启用嵌套虚拟化。 如果使用 VMware，则可通过切换开关在虚拟机上启用此功能。

  若要在 IoT Core 设备上安装 IoT Edge，请在[远程 PowerShell 会话](/windows/iot-core/connect-your-device/powershell)中使用以下命令来检查设备是否支持 Windows 容器：

  ```powershell
  Get-Service vmcompute
  ```

* [已注册的设备 ID](how-to-register-device.md)

  如果已将设备注册为对称密钥身份验证，请将设备连接字符串准备就绪。

  如果已使用 x.509 自签名证书身份验证向设备注册，则至少要有一个用于注册设备的标识证书及其在设备上提供的匹配专用私钥。

## <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于 OCI 兼容的容器运行时。 对于生产方案，建议使用基于 Moby 的引擎。 Moby 引擎是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

对于生产方案，请使用安装脚本中包含的基于 Moby 的引擎。 无需额外步骤来安装引擎。

对于带有 Linux 容器的 IoT Edge，需要提供自己的容器运行时。 在继续之前，请在设备上安装 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) 并将其配置为[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

## <a name="install-the-iot-edge-security-daemon"></a>安装 IoT Edge 安全守护程序

>[!TIP]
>对于 IoT Core 设备，建议使用远程 PowerShell 会话来运行安装命令。 有关详细信息，请参阅[将 PowerShell 用于 Windows IoT](/windows/iot-core/connect-your-device/powershell)。

1. 以管理员身份运行 PowerShell。

   使用 PowerShell 的 AMD64 会话，不要使用 PowerShell(x86)。 如果不确定你使用的是哪种会话类型，请运行以下命令：

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 运行 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令，它将执行以下任务：

   * 检查 Windows 计算机使用的是否是受支持的版本。
   * 启用容器功能。
   * 下载 Moby 引擎和 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

   `Deploy-IoTEdge` 命令默认使用 Windows 容器。 如果要使用 Linux 容器，请添加 `ContainerOs` 参数：

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

3. 此时，IoT Core 设备可能会自动重启。 Windows 10 或 Windows Server 设备可能会提示你重启。 如果是这样，请立即重启设备。

在设备上安装 IoT Edge 时，可使用附加参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向本地目录以进行脱机安装。

有关这些附加参数的详细信息，请参阅 [Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="provision-the-device-with-its-cloud-identity"></a>用云标识预配设备

由于容器引擎和 IoT Edge 运行时已安装在你的设备上，你可以开始下一步，即用其云标识和身份验证信息设置设备。

根据要使用的身份验证类型，选择下一部分：

* [选项1：通过对称密钥进行身份验证](#option-1-authenticate-with-symmetric-keys)
* [选项2：用 x.509 证书进行身份验证](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>选项1：通过对称密钥进行身份验证

此时，IoT Edge 运行时安装在 Windows 设备上，并且你需要将设备设置为其云标识和身份验证信息。

本部分逐步讲解如何设置具有对称密钥身份验证的设备。 应已在 IoT 中心内注册设备，并从设备信息中检索连接字符串。 否则，请按照在 [IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤操作。

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * 如果使用的是 Linux 容器，则将 `-ContainerOs` 参数添加到标志。 使用先前运行的 `Deploy-IoTEdge` 命令，与所选容器选项保持一致。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * 如果将 IoTEdgeSecurityDaemon.ps1 脚本下载到设备上以进行脱机安装或特定版本安装，请确保参考该脚本的本地副本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 系统出现提示时，请提供在上一部分中检索到的设备连接字符串。 设备连接字符串将物理设备与 IoT 中心内的设备 ID 关联，并提供身份验证信息。

   该设备连接字符串采用以下格式（不包括引号）：`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

手动预配设备时，可以使用其他参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 声明特定的 edgeAgent 容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些附加参数的详细信息，请参阅 [Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

### <a name="option-2-authenticate-with-x509-certificates"></a>选项2：用 x.509 证书进行身份验证

此时，IoT Edge 运行时安装在 Windows 设备上，并且你需要将设备设置为其云标识和身份验证信息。

本部分逐步讲解使用 x.509 证书身份验证设置设备的步骤。 应已在 IoT 中心内注册设备，提供与 IoT Edge 设备上的证书和私钥相匹配的指纹。 否则，请按照在 [IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤操作。

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令在计算机上配置 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * 如果使用的是 Linux 容器，则将 `-ContainerOs` 参数添加到标志。 使用先前运行的 `Deploy-IoTEdge` 命令，与所选容器选项保持一致。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * 如果将 IoTEdgeSecurityDaemon.ps1 脚本下载到设备上以进行脱机安装或特定版本安装，请确保参考该脚本的本地副本。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 系统出现提示时，请提供以下信息：

   * **IotHubHostName**：设备将连接到的 IoT 中心的主机名。 例如 `{IoT hub name}.azure-devices.net`。
   * **DeviceId**：注册设备时提供的 ID。
   * **X509IdentityCertificate**：设备上标识证书的绝对路径。 例如 `C:\path\identity_certificate.pem`。
   * **X509IdentityPrivateKey**：所提供标识证书私钥文件的绝对路径。 例如 `C:\path\identity_key.pem`。

手动预配设备时，可以使用其他参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 声明特定的 edgeAgent 容器映像，并提供凭据（如果该映像位于专用注册表中）

有关这些附加参数的详细信息，请参阅 [Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="offline-or-specific-version-installation-optional"></a>脱机或特定版本安装 (可选) 

本部分中的步骤适用于标准安装步骤未涵盖的方案。 这可能包括：

* 脱机安装 IoT Edge
* 安装候选发布版本
* 安装最新版本以外的版本

在安装过程中，会下载三个文件：

* 一个 PowerShell 脚本，其中包含安装说明
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序 (iotedged)、Moby 容器引擎和 Moby CLI
* Visual C++ Redistributable Package（VC 运行时）安装程序

如果在安装过程中设备会脱机，或者要安装 IoT Edge 的特定版本，则可提前将这些文件下载到设备。 当安装时，请将安装脚本指向包含已下载文件的目录。 安装程序首先会检查该目录，然后仅下载未找到的组件。 如果所有文件都可脱机使用，则无需连接到 Internet 连接即可安装。

1. 有关最新的 IoT Edge 安装文件以及旧版本，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 找到要安装的版本，然后从发行说明的“资产”部分将以下文件下载到 IoT 设备上：

   * IoTEdgeSecurityDaemon.ps1
   * 1\.0.9 或更高版本中的 Microsoft-Azure-IoTEdge-amd64.cab，或者 1.0.8 或更低版本中的 Microsoft-Azure-IoTEdge.cab。

   从 1.0.9 开始，也可以使用 Microsoft-Azure-IotEdge-arm32.cab（仅用于测试目的）。 Windows ARM32 设备目前不支持 IoT Edge。

   请务必使用与所用 .cab 文件版本相同的 PowerShell 脚本，因为功能会进行更改以支持每个版本中的特性。

3. 如果下载的 .cab 文件在其上有体系结构后缀，则只需将该文件重命名为“Microsoft-Azure-IoTEdge.cab”即可  。

4. （可选）下载 Visual C++ Redistributable 的安装程序。 例如，PowerShell 脚本使用此版本：[vc_redist.x64.exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe)。 将安装程序保存到 IoT 设备上 IoT Edge 文件所在的文件夹中。

5. 若要使用脱机组件进行安装，请[使用点获取 PowerShell 脚本本地副本的来源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)。

6. 用 `-OfflineInstallationPath` 参数运行 [Deploy-IoTEdge](reference-windows-scripts.md#deploy-iotedge) 命令。 提供文件目录的绝对路径。 例如，

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   部署命令会使用已提供的本地文件目录中的任何组件。 如果缺少 .cab 文件或 Visual C++ 安装程序，部署命令会尝试下载该文件或安装程序。

## <a name="update-iot-edge"></a>更新 IoT Edge

使用 `Update-IoTEdge` 命令更新安全守护程序。 脚本会自动提取最新版本的安全守护程序。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

运行 Update-IoTEdge 命令会从设备中删除并更新安全守护程序以及两个运行时容器映像。 config.yaml 文件以及 Moby 容器引擎中的数据会保留在设备上（如果使用 Windows 容器）。 保留配置信息意味着，在更新过程中，不需再次为设备提供连接字符串或设备预配服务信息。

若要更新到安全守护程序的特定版本，请在 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中查找目标版本。 在该版本中，下载 **Microsoft-Azure-IoTEdge.cab** 文件。 然后，使用 `-OfflineInstallationPath` 参数指向本地文件位置。 例如：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` 参数将在提供的目录中查找名为 **Microsoft-Azure-IoTEdge.cab** 的文件。 从 IoT Edge 版本 1.0.9-rc4 开始，可以使用两个 .cab 文件，一个用于 AMD64 设备，另一个用于 ARM32。 下载适用于设备的正确文件，然后重命名该文件以删除体系结构后缀。

如果你想要脱机更新设备，请从 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)查找要面向的版本。 在该版本中，下载 *IoTEdgeSecurityDaemon.ps1* 和 *Microsoft-Azure-IoTEdge.cab* 文件。 请务必使用与所用 .cab 文件版本相同的 PowerShell 脚本，因为功能会进行更改以支持每个版本中的特性。

如果下载的 .cab 文件在其上有体系结构后缀，则只需将该文件重命名为“Microsoft-Azure-IoTEdge.cab”即可  。

若要使用脱机组件进行更新，请[使用点获取 PowerShell 脚本本地副本的来源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)。 然后，使用 `-OfflineInstallationPath` 参数作为 `Update-IoTEdge` 命令的一部分，并提供文件目录的绝对路径。 例如，

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

有关更新选项的详细信息，请使用命令 `Get-Help Update-IoTEdge -full`，或参考[Windows 上 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

若要从设备中删除 IoT Edge 安装，请使用以下命令。

若要从 Windows 设备中删除 IoT Edge 安装，请在 PowerShell 管理窗口中使用 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 命令。 此命令会删除 IoT Edge 运行时，以及现有的配置和 Moby 引擎数据。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

`Uninstall-IoTEdge` 命令在 Windows IoT Core 中无法运行。 若要删除 IoT Edge，需要重新部署 Windows IoT Core 映像。

有关卸载选项的详细信息，请使用命令 `Get-Help Uninstall-IoTEdge -full`。

## <a name="next-steps"></a>后续步骤

转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
