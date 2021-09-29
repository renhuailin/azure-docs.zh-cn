---
title: 用于 Azure IoT Edge for Linux on Windows 的 PowerShell 函数 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows PowerShell 函数的参考信息，这些函数用于部署、预配 IoT Edge for Linux on Windows 虚拟机及获取其状态。
author: v-tcassi
ms.author: fcabrera
ms.date: 06/18/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 93a217a88b75ec6c472972d3f12cb33e33561f2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579778"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>用于 IoT Edge for Linux on Windows 的 PowerShell 函数

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

了解用于部署、预配和获取 IoT Edge for Linux on Windows (EFLOW) 虚拟机状态的 PowerShell 函数。

## <a name="prerequisites"></a>先决条件

本文所述的命令来自 `AzureEFLOW.psm1` 文件，该文件位于系统中 `WindowsPowerShell`目录中的 `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW` 下。

如果 PowerShell 目录中没有 AzureEflow 文件夹，请按照以下步骤下载和安装 Azure IoT Edge for Linux on Windows： 

1. 在已提升权限的 PowerShell 会话中，运行以下每个命令来下载 IoT Edge for Linux on Windows。

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. 在设备上安装 IoT Edge for Linux on Windows。

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   通过向安装命令中添加 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 和 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 参数，可以指定自定义安装和 VHDX 目录。

1. 在目标设备上将执行策略设置为 `AllSigned`（如果尚未设置）。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVM

Connect-EflowVM 命令使用 SSH 连接到虚拟机。 唯一被允许通过 SSH 登录到虚拟机的帐户是创建该虚拟机的用户。

此命令仅适用于在主机设备上运行的 PowerShell 会话。 使用 Windows Admin Center 或 PowerShell ISE 时，它将不起作用。

有关详细信息，请参阅命令 `Get-Help Connect-EflowVM -full`。

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

Copy-EflowVmFile 使用 SCP 向虚拟机或从其中复制文件。 使用可选参数可指定源和目标文件路径以及副本的方向。

用户 iotedge-user 必须对虚拟机上的任何源目录具有读取权限，或者对虚拟机上的任何目标目录具有写入权限。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| fromFile | 表示文件路径的字符串 | 定义要从中读取的文件。 |
| toFile | 表示文件路径的字符串 |  定义要写入到的文件。 |
| pushFile | 无 | 此标记指示复制方向。 如果存在，则该命令会将文件推送到虚拟机。 如果不存在，则该命令将从虚拟机提取文件。 |

有关详细信息，请参阅命令 `Get-Help Copy-EflowVMFile -full`。

## <a name="deploy-eflow"></a>Deploy-Eflow

Deploy-Eflow 命令是主要部署方法。 该部署命令可创建虚拟机、预配文件以及部署 IoT Edge 代理模块。 尽管不需要任何参数，但它们可用于在部署过程中预配 IoT Edge 设备，以及在创建期间修改虚拟机的设置。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| acceptEula | “是”或“否”  | 接受/拒绝 EULA 并绕过 EULA 提示的快捷方式。 |
| acceptOptionalTelemetry | “是”或“否”  |  接受/拒绝可选遥测并绕过遥测提示的快捷方式。 |
| cpuCount | 介于 1 和设备 CPU 内核数之间的整数值 |  VM 的 CPU 内核数。<br><br>默认值：1 个 vCore。 |
| memoryInMB | 介于 1024 和设备最大可用内存量之间的整数值 |为该 VM 分配的内存。<br><br>默认值：1024 MB。 |
| vmDiskSize | 介于 8 GB 和 256 GB | 动态扩展虚拟硬盘的最大磁盘大小。<br><br>默认值：10 GB。 |
| vswitchName | 虚拟交换机的名称 |  分配给 EFLOW VM 的虚拟交换机的名称。 |
| vswitchType | “内部”或“外部”  | 分配给 EFLOW VM 的虚拟交换机的类型。 |
| ip4Address | DCHP 服务器范围中的 IPv4 地址 | EFLOW VM 的静态 Ipv4 地址。 |
| ip4PrefixLength | 子网的 IPv4 前缀长度 | Ipv4 子网前缀长度，仅在指定了静态 Ipv4 地址时有效。 |
| ip4GatewayAddress | 子网网关的 IPv4 地址 | 网关 Ipv4 地址，仅当指定了静态 Ipv4 地址时有效。 |
| gpuName | GPU 设备名称 |  用于传递的 GPU 设备的名称。 |
| gpuPassthroughType | DirectDeviceAssignment、ParaVirtualization 或无（仅限 CPU） |  GPU 传递类型 |
| gpuCount | 介于 1 和设备 GPU 内核数量之间的整数值 | VM 的 GPU 设备数。 <br><br>注意：如果使用 ParaVirtualization，请确保设置 gpuCount = 1 |

有关详细信息，请参阅命令 `Get-Help Deploy-Eflow -full`。  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

Get-EflowHostConfiguration 命令可返回主机配置。 此命令无参数。 它将返回一个包含四个属性的对象：

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

有关详细信息，请参阅命令 `Get-Help Get-EflowHostConfiguration -full`。


## <a name="get-eflowlogs"></a>Get-EflowLogs

Get-EflowLogs 命令用于从 IoT Edge for Linux on Windows 部署和安装收集和绑定日志。 它以 `.zip` 文件夹的形式输出捆绑的日志。

有关详细信息，请参阅命令 `Get-Help Get-EflowLogs -full`。

## <a name="get-eflowvm"></a>Get-EflowVm

Get-EflowVm 命令用于返回虚拟机的当前配置。 此命令无参数。 它将返回一个包含四个属性的对象：

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

若要查看可读列表中的特定属性，请在展开该属性的情况下运行 `Get-EflowVM` 命令。 例如：

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

有关详细信息，请参阅命令 `Get-Help Get-EflowVm -full`。

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Get-EflowVmAddr 命令用于查询虚拟机的当前 IP 和 MAC 地址。 此命令用于应对 IP 和 MAC 地址可能会随时间而改变的情况。

有关详细信息，请使用命令 `Get-Help Get-EflowVmAddr -full`。

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

Get-EflowVmFeature 命令可返回 IoT Edge for Linux on Windows 功能的启用状态。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| feature | **DpsTpm** | 要切换的功能名称。 |

有关详细信息，请参阅命令 `Get-Help Get-EflowVmFeature -full`。

## <a name="get-eflowvmname"></a>Get-EflowVmName

Get-EflowVmName 命令可返回虚拟机的当前主机名。 此命令用于应对 Windows 主机名可能会随时间而改变的情况。

有关详细信息，请参阅命令 `Get-Help Get-EflowVmName -full`。

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

Get-EflowVmTelemetryOption 命令可显示虚拟机内遥测的状态（“可选”或“必选”） 。

有关详细信息，请参阅命令 `Get-Help Get-EflowVmTelemetryOption -full`。


## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Get-EflowVmTpmProvisioningInfo 命令可返回 TPM 预配信息。 此命令无参数。 它将返回一个包含两个属性的对象：

* 认可密钥
* 注册 ID 

有关详细信息，请参阅命令 `Get-Help Get-EflowVmTpmProvisioningInfo -full`。



## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

Invoke-EflowVMCommand 命令可在虚拟机内执行 Linux 命令并返回输出。 此命令仅适用于返回有限输出的 Linux 命令。 它不能用于需要用户交互或无限期运行的 Linux 命令。

可以使用以下可选参数来提前指定命令。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| 命令 | 字符串 | 要在 VM 中执行的命令。 |
| ignoreError | 无 |  如果存在此标记，则忽略命令中的错误。 |

有关详细信息，请参阅命令 `Get-Help Invoke-EflowVmCommand -full`。

## <a name="provision-eflowvm"></a>Provision-EflowVm

Provision-EflowVm 命令可将 IoT Edge 设备的预配信息添加到虚拟机的 IoT Edge `config.yaml` 文件中。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| provisioningType | ManualConnectionString、ManualX509、DpsTPM、DpsX509 或 DpsSymmetricKey |  定义要用于 IoT Edge 设备的预配类型。 |
| devConnString | 现有 IoT Edge 设备的设备连接字符串 | 用于手动预配 IoT Edge 设备的设备连接字符串 (ManualConnectionString)。 |
| iotHubHostname | 现有 IoT 中心的主机名 | 用于预配 IoT Edge 设备的 Azure IoT 中心主机名 (ManualX509)。 |
| deviceId | 现有 IoT Edge 设备的设备 ID | 用于预配 IoT Edge 设备的设备 ID (ManualX509)。 |
| scopeId | 现有 DPS 实例的作用域 ID。 | 用于预配 IoT Edge 设备的作用域 ID（DpsTPM、DpsX509 或 DpsSymmetricKey）  。 |
| symmKey | 现有 DPS 注册的主密钥或使用对称密钥注册的现有 IoT Edge 设备的主密钥 | 用于预配 IoT Edge 设备的对称密钥 (DpsSymmetricKey)。 |
| registrationId | 现有 IoT Edge 设备的注册 ID | 用于预配 IoT Edge 设备的注册 ID (DpsSymmetricKey)。 |
| identityCertPath | 目录路径 | Windows 主机上标识证书的绝对目标路径（ManualX509、DpsX509） 。 |
| identityPrivKeyPath | 目录路径 | Windows 主机上标识证书的绝对源路径（ManualX509、DpsX509） 。 |

有关详细信息，请参阅命令 `Get-Help Provision-EflowVm -full`。

## <a name="set-eflowvm"></a>Set-EflowVM

Set-EflowVM 命令可针对虚拟机配置更新请求的属性。 使用可选参数可定义虚拟机的特定配置。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| cpuCount | 介于 1 和设备 GPU 内核数之间的整数值 | VM 的 CPU 内核数。 |
| memoryInMB | 介于 1024 和设备最大可用内存量之间的整数值 | 为该 VM 分配的内存。 |
| gpuName | GPU 设备名称 |  用于传递的 GPU 设备的名称。 |
| gpuPassthroughType | DirectDeviceAssignment、ParaVirtualization 或无（不传递） |  GPU 传递类型 |
| gpuCount | 介于 1 和设备 GPU 内核数之间的整数值 | VM 的 GPU 设备数 注意：仅在使用 DirectDeviceAssignment 时有效 |
| headless | 无 | 如果存在此标记，则决定在发出安全警告时是否需要用户进行确认。 |

有关详细信息，请参阅命令 `Get-Help Set-EflowVM -full`。

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

Set-EflowVmFeature 命令可启用或禁用 IoT Edge for Linux on Windows 功能的状态。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| feature | **DpsTpm** | 要切换的功能名称。 |
| enable | 无 | 如果存在此标记，则该命令将启用该功能。 |

有关详细信息，请参阅命令 `Get-Help Set-EflowVmFeature -full`。

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

Set-EflowVmTelemetryOption 命令可启用或禁用虚拟机内的可选遥测。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| optionalTelemetry | True 或 False | 是否选择了可选遥测。 |

有关详细信息，请参阅命令 `Get-Help Set-EflowVmTelemetryOption -full`。

## <a name="start-eflowvm"></a>Start-EflowVm

Start-EflowVm 命令用于启动虚拟机。 如果虚拟机已启动，则不执行任何操作。

有关详细信息，请参阅命令 `Get-Help Start-EflowVm -full`。

## <a name="stop-eflowvm"></a>Stop-EflowVm

Stop-EflowVm 命令用于停止虚拟机。 如果虚拟机已停止，则不执行任何操作。

有关详细信息，请参阅命令 `Get-Help Stop-EflowVm -full`。

## <a name="verify-eflowvm"></a>Verify-EflowVm

Verify-EflowVm 命令是一个公开函数，用于检查是否已创建 IoT Edge for Linux on Windows 虚拟机。 该命令只使用常见参数，如果虚拟机已创建，则返回 true，否则返回 false 。

有关详细信息，请参阅命令 `Get-Help Verify-EflowVm -full`。

## <a name="next-steps"></a>后续步骤

阅读以下文章，了解如何使用这些命令安装和预配 IoT Edge for Linux on Windows：

* [安装 Azure IoT Edge for Linux on Windows](./how-to-install-iot-edge-on-windows.md)
