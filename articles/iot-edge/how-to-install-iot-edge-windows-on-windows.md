---
title: 安装适用于 Windows 的 Azure IoT Edge | Microsoft Docs
description: 在 Windows 设备上安装适用于 Windows 容器的 Azure IoT Edge
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 676bd23dce7ca79f3640bdfa8f9b55e427390e91
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722018"
---
# <a name="install-and-manage-azure-iot-edge-with-windows-containers"></a>使用 Windows 容器安装和管理 Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

设置 IoT Edge 设备的过程包括两个步骤。 第一步是安装运行时及其依赖项。 第二步是将设备连接到其在云中的标识，并使用 IoT 中心设置身份验证。

本文列出了使用 Windows 容器安装 Azure IoT Edge 运行时的步骤。 如果要在 Windows 设备上使用 Linux 容器，请参阅 [Azure IoT Edge for Linux on Windows](how-to-install-iot-edge-on-windows.md) 一文。

>[!NOTE]
>从 Azure IoT Edge 1.2 版开始，将不再支持带有 Windows 容器的 Azure IoT Edge。
>
>请考虑使用新方法在 Windows 设备上运行 IoT Edge：[Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)。

## <a name="prerequisites"></a>先决条件

* Windows 设备

  带有 Windows 容器的 IoT Edge 需要 Windows 版本 1809/内部版本 17763，这是最新的 [Windows 长期支持版本](/windows/release-information/)。 若要查看受支持的 SKU 列表，请参阅[受支持的系统列表](support.md#operating-systems)。

* [已注册的设备 ID](how-to-register-device.md)

  如果你已为设备注册了对称密钥身份验证，请准备好设备连接字符串。

  如果你已为设备注册了 X.509 自签名证书身份验证，则至少需要有一个用来注册设备的标识证书及其可在设备上使用的匹配私钥。

## <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于 OCI 兼容的容器运行时，如 [Moby](https://github.com/moby/moby)。 安装脚本中包含的基于 Moby 的引擎。 无需额外步骤来安装引擎。

## <a name="install-the-iot-edge-security-daemon"></a>安装 IoT Edge 安全守护程序

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

3. 如果出现提示，请重启设备。

在设备上安装 IoT Edge 时，可使用附加参数来修改过程，包括：

* 定向流量，使其通过代理服务器
* 将安装程序指向本地目录以进行脱机安装。

有关这些附加参数的详细信息，请参阅[用于带有 Windows 容器的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="provision-the-device-with-its-cloud-identity"></a>为设备预配其云标识

现在容器引擎和 IoT Edge 运行时已安装在设备上，你可以开始下一步操作，即，使用设备的云标识和身份验证信息来设置设备。

根据要使用的身份验证类型，选择下一部分：

* [选项 1：使用对称密钥进行身份验证](#option-1-authenticate-with-symmetric-keys)
* [选项 2：使用 X.509 证书进行身份验证](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>选项 1：使用对称密钥进行身份验证

此时，IoT Edge 运行时安装在 Windows 设备上，你需要使用设备的云标识和身份验证信息来预配设备。

本部分逐步讲解了如何为设备预配对称密钥身份验证。 你应当已在 IoT 中心注册了设备，并从设备信息中检索了连接字符串。 如果没有，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤进行操作。

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令在计算机上配置 IoT Edge 运行时。 该命令默认为使用 Windows 容器手动预配。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
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

有关这些附加参数的详细信息，请参阅[用于带有 Windows 容器的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

### <a name="option-2-authenticate-with-x509-certificates"></a>选项 2：使用 X.509 证书进行身份验证

此时，IoT Edge 运行时安装在 Windows 设备上，你需要使用设备的云标识和身份验证信息来预配设备。

本部分逐步讲解了如何为设备预配 X.509 证书身份验证。 你应当已在 IoT 中心注册了设备，并提供了与 IoT Edge 设备上的证书和私钥相匹配的指纹。 如果没有，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤进行操作。

1. 在 IoT Edge 设备上，以管理员身份运行 PowerShell。

2. 使用 [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) 命令在计算机上配置 IoT Edge 运行时。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
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

有关这些附加参数的详细信息，请参阅[用于带有 Windows 容器的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="offline-or-specific-version-installation-optional"></a>脱机或特定版本安装（可选）

本部分中的步骤适用于标准安装步骤未涵盖的方案。 这可能包括：

* 脱机安装 IoT Edge
* 安装候选发布版本
* 安装非最新版本

在安装过程中，会下载三个文件：

* 一个 PowerShell 脚本，其中包含安装说明
* Microsoft Azure IoT Edge cab，其中包含 IoT Edge 安全守护程序 (iotedged)、Moby 容器引擎和 Moby CLI
* Visual C++ Redistributable Package（VC 运行时）安装程序

如果在安装过程中设备会脱机，或者要安装 IoT Edge 的特定版本，则可提前将这些文件下载到设备。 当安装时，请将安装脚本指向包含已下载文件的目录。 安装程序首先会检查该目录，然后仅下载未找到的组件。 如果所有文件都可脱机使用，则无需连接到 Internet 连接即可安装。

1. 有关最新的 IoT Edge 安装文件以及旧版本，请参阅 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)。

2. 找到要安装的版本，然后从发行说明的“资产”部分将以下文件下载到 IoT 设备上：

   * IoTEdgeSecurityDaemon.ps1
   * 1\.1 发布通道中的 Microsoft-Azure-IoTEdge-amd64.cab。

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
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

运行 Update-IoTEdge 命令会从设备中删除并更新安全守护程序以及两个运行时容器映像。 config.yaml 文件以及 Moby 容器引擎中的数据会保留在设备上。 保留配置信息意味着，在更新过程中，不需再次为设备提供连接字符串或设备预配服务信息。

若要更新到安全守护程序的特定版本，请在 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中查找 1.1 发布通道中的目标版本。 在该版本中，下载 **Microsoft-Azure-IoTEdge.cab** 文件。 然后，使用 `-OfflineInstallationPath` 参数指向本地文件位置。 例如：

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>`-OfflineInstallationPath` 参数将在提供的目录中查找名为 **Microsoft-Azure-IoTEdge.cab** 的文件。 重命名该文件以删除其体系结构后缀（如果有）。

如果你想脱机更新设备，请从 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)中查找你需要的目标版本。 在该版本中，下载 IoTEdgeSecurityDaemon.ps1 和 Microsoft-Azure-IoTEdge.cab 文件。 请务必使用与所用 .cab 文件版本相同的 PowerShell 脚本，因为功能会进行更改以支持每个版本中的特性。

如果下载的 .cab 文件在其上有体系结构后缀，则只需将该文件重命名为“Microsoft-Azure-IoTEdge.cab”即可  。

若要使用脱机组件进行更新，请[使用点获取 PowerShell 脚本本地副本的来源](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing)。 然后，使用 `-OfflineInstallationPath` 参数作为 `Update-IoTEdge` 命令的一部分，并提供文件目录的绝对路径。 例如，

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

有关更新选项的详细信息，请使用命令 `Get-Help Update-IoTEdge -full`，或参考[用于带有 Windows 容器的 IoT Edge 的 PowerShell 脚本](reference-windows-scripts.md)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

若要从设备中删除 IoT Edge 安装，请使用以下命令。

若要从 Windows 设备中删除 IoT Edge 安装，请在 PowerShell 管理窗口中使用 [Uninstall-IoTEdge](reference-windows-scripts.md#uninstall-iotedge) 命令。 此命令会删除 IoT Edge 运行时，以及现有的配置和 Moby 引擎数据。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

有关卸载选项的详细信息，请使用命令 `Get-Help Uninstall-IoTEdge -full`。

## <a name="next-steps"></a>后续步骤

转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
