---
title: 使用 Windows 容器的 Azure IoT Edge 脚本 | Microsoft Docs
description: 用于在 Windows 设备上安装、卸载或更新的 IoT Edge PowerShell 脚本的参考信息
author: kgremban
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 912d13e5a9c05916e209ee42cf5629b8a363e686
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749530"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>使用 Windows 容器实现用于 IoT Edge 的 PowerShell 脚本

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

了解用于在 Windows 设备上安装、更新或卸载 IoT Edge 的 PowerShell 脚本。

本文所述的命令来自 `IoTEdgeSecurityDaemon.ps1` 文件，该文件随每个 [IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)一起发布。 最新版本的脚本始终在 aka.ms/iotedge-win 中提供。

可使用 `Invoke-WebRequest` cmdlet 运行任何命令来访问最新的脚本版本。 例如：

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

还可下载此脚本或某个特定发行版中的脚本版本来运行命令。 例如：

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

提供的脚本已签名以提高安全性。 若要验证签名，可以将脚本下载到设备，然后运行以下 PowerShell 命令：

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

如果签名获得验证，则输出状态为“有效”。

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 命令下载并部署 IoT Edge 安全守护程序及其依赖项。 该部署命令接受这些通用参数，此外还接受其他一些参数。 有关完整列表，请使用命令 `Get-Help Deploy-IoTEdge -full`。  

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器操作系统，则 Windows 是默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的 Moby 容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **Proxy** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装时所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 系统会下载该目录中不存在的任何文件。 如果这两个文件在该目录中存在，则无需建立 Internet 连接即可安装 IoT Edge。 还可以通过此参数来使用特定的版本。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **RestartIfNeeded** | 无 | 此标志可让部署脚本在无提示的情况下根据需要重启计算机。 |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 命令使用设备连接字符串和操作详细信息配置 IoT Edge。 然后，此命令生成的许多信息将存储在 iotedge\config.yaml 文件中。 该初始化命令接受这些通用参数，此外还接受其他一些参数。 有关完整列表，请使用命令 `Get-Help Initialize-IoTEdge -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ManualConnectionString** | 无 | **开关参数**。 **默认值**。 如果未指定预配类型，则默认使用连接字符串进行手动预配。<br><br>声明你要提供设备连接字符串来手动预配设备。 |
| **ManualX509** | 无 | **开关参数**。 如果未指定预配类型，则默认使用连接字符串进行手动预配。<br><br>声明你要提供标识证书和私钥来手动预配设备。
| **DpsTpm** | 无 | **开关参数**。 如果未指定预配类型，则默认使用连接字符串进行手动预配。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID，以通过 DPS 进行预配。  |
| **DpsSymmetricKey** | 无 | **开关参数**。 如果未指定预配类型，则默认使用连接字符串进行手动预配。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID 来通过 DPS 进行预配，同时提供对称密钥进行证明。 |
| **DpsX509** | 无 | **开关参数**。 如果未指定预配类型，则默认使用连接字符串进行手动预配。<br><br>声明你要提供设备预配服务 (DPS) 范围 ID 和设备的注册 ID 来通过 DPS 进行预配，同时提供 X.509 标识证书和私钥进行证明。  |
| **DeviceConnectionString** | 已在 IoT 中心注册的 IoT Edge 设备中的连接字符串，括在单引号中 | 如果使用连接字符串进行手动预配，则这是必需项。 如果未在脚本参数中提供连接字符串，系统会提示你提供一个。 |
| **IotHubHostName** | 设备连接到的 IoT 中心的主机名。 | 如果使用 X.509 证书进行手动预配，则这是必需项。 采用 {hub name}.azure-devices.net 格式。 |
| **DeviceId** | IoT 中心内已注册的设备标识的设备 ID。 | 如果使用 X.509 证书进行手动预配，则这是必需项。 |
| **ScopeId** | 与 IoT 中心关联的设备预配服务实例中的范围 ID。 | 进行 DPS 预配所需。 如果未在脚本参数中提供范围 ID，系统会提示你提供一个。 |
| **RegistrationId** | 设备生成的注册 ID | 如果使用 TPM 或对称密钥证明，则为 DPS 预配所需。 如果使用 X.509 证书证明，则为可选。 |
| **X509IdentityCertificate** | 设备上的 X.509 设备标识证书的 URI 路径。 | 如果使用 X.509 证书证明，则是手动或 DPS 预配的必需项。 |
| **X509IdentityPrivateKey** | 设备上的 X.509 设备标识证书密钥的 URI 路径。 | 如果使用 X.509 证书证明，则是手动或 DPS 预配的必需项。 |
| **SymmetricKey** | 使用 DPS 时用于预配 IoT Edge 设备标识的对称密钥 | 如果使用对称密钥证明，则为 DPS 预配所需。 |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器操作系统，则 Windows 是默认值。<br><br>对于 Windows 容器，IoT Edge 使用安装中包含的 Moby 容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **DeviceCACertificate** | 设备上的 X.509 设备 CA 证书的 URI 路径。 | 还可在 `C:\ProgramData\iotedge\config.yaml` 文件中配置。 有关详细信息，请参阅[管理 IoT Edge 设备上的证书](how-to-manage-device-certificates.md)。 |
| **DeviceCAPrivateKey** | 设备上的 X.509 设备 CA 私钥的 URI 路径。 | 还可在 `C:\ProgramData\iotedge\config.yaml` 文件中配置。 有关详细信息，请参阅[管理 IoT Edge 设备上的证书](how-to-manage-device-certificates.md)。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **AgentImage** | IoT Edge 代理映像 URI | 默认情况下，新的 IoT Edge 安装使用 IoT Edge 代理映像的最新滚动标记。 使用此参数可为映像版本设置特定的标记，或者提供自己的代理映像。 有关详细信息，请参阅[了解 IoT Edge 标记](how-to-update-iot-edge.md#understand-iot-edge-tags)。 |
| **用户名** | 容器注册表用户名 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供有权访问注册表的用户名。 |
| **密码** | 安全密码字符串 | 仅当针对专用注册表中的容器设置了 -AgentImage 参数时，才使用此参数。 提供用于访问注册表的密码。 |

## <a name="update-iotedge"></a>Update-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 或 **Linux** | 如果未指定容器 OS，则 Windows 是默认值。 对于 Windows 容器，安装中会包含一个容器引擎。 对于 Linux 容器，需要在开始安装之前安装容器引擎。 |
| **Proxy** | 代理 URL | 如果设备需要通过代理服务器来连接 Internet，请包含此参数。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **InvokeWebRequestParameters** | 参数和值的哈希表 | 在安装期间，会发出多个 Web 请求。 请使用此字段来设置这些 Web 请求的参数。 此参数可用于配置代理服务器的凭据。 有关详细信息，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)。 |
| **OfflineInstallationPath** | 目录路径 | 如果包含此参数，则安装程序将在列出的目录中检查安装时所需的 IoT Edge cab 和 VC 运行时 MSI 文件。 系统会下载该目录中不存在的任何文件。 如果这两个文件在该目录中存在，则无需建立 Internet 连接即可安装 IoT Edge。 还可以通过此参数来使用特定的版本。 |
| **RestartIfNeeded** | 无 | 此标志可让部署脚本在无提示的情况下根据需要重启计算机。 |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| **Force** | 无 | 在上次尝试卸载失败时，此标志会强制卸载。
| **RestartIfNeeded** | 无 | 此标志可让卸载脚本在无提示的情况下根据需要重启计算机。 |

## <a name="next-steps"></a>后续步骤

在以下文章中了解如何使用这些命令：

* [安装或卸载适用于 Windows 的 Azure IoT Edge](how-to-install-iot-edge-windows-on-windows.md)
