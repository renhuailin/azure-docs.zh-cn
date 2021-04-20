---
title: 用于 Azure IoT Edge for Linux on Windows 的 PowerShell 脚本 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows PowerShell 脚本的参考信息，这些脚本用于部署、预配 IoT Edge for Linux on Windows 虚拟机及获取其状态。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a24b39107d8f78c049afa230fe678ec92852eeb0
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959681"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>用于 IoT Edge for Linux on Windows 的 PowerShell 脚本

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

了解 PowerShell 脚本，这些脚本用于部署、预配 IoT Edge for Linux on Windows 虚拟机及获取其状态。

本文所述的命令来自 `AzureEFLOW.psm1` 文件，该文件位于系统中 `WindowsPowerShell`目录中的 `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` 下。

## <a name="deploy-eflow"></a>Deploy-Eflow

Deploy-Eflow 命令是主要部署方法。 该部署命令可创建虚拟机、预配文件以及部署 IoT Edge 代理模块。 尽管不需要任何参数，但它们可用于在部署过程中预配 IoT Edge 设备，以及在创建期间修改虚拟机的设置。 若要获取完整列表，请使用命令 `Get-Help Deploy-Eflow -full`。  

>[!NOTE]
>对于预配类型，X509 目前专门指使用 [Azure IoT 中心设备预配服务](../iot-dps/about-iot-dps.md)的 X509 预配。 目前不支持手动 X509 预配方法。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| eflowVhdxDir | 目录路径 | 部署存储 VM 的 VHDX 文件的目录。 |
| provisioningType | “manual”、“TPM”、“X509”或“symmetric”    |  定义要用于 IoT Edge 设备的预配类型。 |
| devConnString | 现有 IoT Edge 设备的设备连接字符串 | 用于手动预配 IoT Edge 设备的设备连接字符串 (manual)。 |
| symmKey | 现有 DPS 注册的主密钥或使用对称密钥注册的现有 IoT Edge 设备的主密钥 | 用于预配 IoT Edge 设备的对称密钥（X509 或 symmetric） 。 |
| scopeId | 现有 DPS 实例的作用域 ID。 | 用于预配 IoT Edge 设备的作用域 ID（X509 或 symmetric） 。 |
| registrationId | 现有 IoT Edge 设备的注册 ID | 用于预配 IoT Edge 设备的注册 ID（X509 或 symmetric） 。 |
| identityCertLocVm | 目录路径；必须位于可由 `iotedge` 服务拥有的文件夹中 | 虚拟机上用于预配 IoT Edge 设备的标识证书的绝对目标路径（X509 或 symmetric） 。 |
| identityCertLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备的标识证书的绝对源路径（X509 或 symmetric） 。 |
| identityPkLocVm | 目录路径；必须位于可由 `iotedge` 服务拥有的文件夹中 | 虚拟机上用于预配 IoT Edge 设备的标识私钥的绝对目标路径（X509 或 symmetric） 。 |
| identityPkLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备的标识私钥的绝对源路径（X509 或 symmetric） 。 |
| vmSizeDefintion | 不超过 30 个字符 | 虚拟机的内核数和可用 RAM 的定义。 **默认值**：Standard_K8S_v1。 |
| vmDiskSize | 介于 8 GB 和 256 GB | 动态扩展虚拟硬盘的最大磁盘大小。 **默认值**：16 GB。 |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |
| vnetType | “透明”或“ICS”  | 虚拟交换机的类型。 默认值：透明。 透明指的是外部交换机，而 ICS 指的是内部交换机。 |
| vnetName | 不超过 64 个字符 | 虚拟交换机的名称。 默认值：External。 |
| enableVtpm | 无 | **开关参数**。 创建启用或禁用 TPM 的虚拟机。 |
| mobyPackageVersion | 不超过 30 个字符 |  要在虚拟机上验证或安装的 Moby 包的版本。  默认值：19.03.11。 |
| iotedgePackageVersion | 不超过 30 个字符 | 要在虚拟机上验证或安装的 IoT Edge 包的版本。 默认值：1.1.0。 |
| installPackages | 无 | **开关参数**。 切换后，该脚本将尝试安装 Moby 和 IoT Edge 包，而不只是验证包是否存在。 |

>[!NOTE]
>默认情况下，如果进程找不到名为 `External` 的外部交换机，它会搜索任何现有的外部交换机，通过该交换机获取 IP 地址。 如果没有可用的外部交换机，它会搜索内部交换机。 如果没有可用的内部交换机，它会尝试创建默认交换机，通过该交换机获取 IP 地址。

## <a name="verify-eflowvm"></a>Verify-EflowVm

Verify-EflowVm 命令是一项公开的功能，用于检查是否已创建 IoT Edge for Linux on Windows 虚拟机。 该命令只使用常见参数，如果虚拟机已创建，它将返回 true，否则返回 false 。 有关详细信息，请使用命令 `Get-Help Verify-EflowVm -full`。

## <a name="provision-eflowvm"></a>Provision-EflowVm

Provision-EflowVm 命令可将 IoT Edge 设备的预配信息添加到虚拟机的 IoT Edge `config.yaml` 文件中。 还可以在部署阶段通过在 Deploy-Eflow 命令中设置参数来完成预配。 有关详细信息，请使用命令 `Get-Help Provision-EflowVm -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |
| provisioningType | “manual”、“TPM”、“X509”或“symmetric”    |  定义要用于 IoT Edge 设备的预配类型。 |
| devConnString | 现有 IoT Edge 设备的设备连接字符串 | 用于手动预配 IoT Edge 设备的设备连接字符串 (manual)。 |
| symmKey | 现有 DPS 注册的主密钥或使用对称密钥注册的现有 IoT Edge 设备的主密钥 | 用于预配 IoT Edge 设备的对称密钥（DPS 或 symmetric） 。 |
| scopeId | 现有 DPS 实例的作用域 ID。 | 用于预配 IoT Edge 设备的作用域 ID (DPS)。 |
| registrationId | 现有 IoT Edge 设备的注册 ID | 用于预配 IoT Edge 设备的注册 ID (DPS)。 |
| identityCertLocVm | 目录路径；必须位于可由 `iotedge` 服务拥有的文件夹中 | 虚拟机上用于预配 IoT Edge 设备的标识证书的绝对目标路径（DPS、X509） 。 |
| identityCertLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备的标识证书的绝对源路径（DPS、X509） 。 |
| identityPkLocVm  | 目录路径；必须位于可由 `iotedge` 服务拥有的文件夹中 | 虚拟机上用于预配 IoT Edge 设备的标识私钥的绝对目标路径（DPS、X509） 。 |
| identityPkLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备的标识私钥的绝对源路径（DPS、X509） 。 |

## <a name="get-eflowvmname"></a>Get-EflowVmName

Get-EflowVmName 命令用于查询虚拟机的当前主机名。 此命令用于应对 Windows 主机名可能会随时间而改变的情况。 它只使用常见参数，并将返回虚拟机的当前主机名。 有关详细信息，请使用命令 `Get-Help Get-EflowVmName -full`。

## <a name="get-eflowlogs"></a>Get-EflowLogs

Get-EflowLogs 命令用于从 IoT Edge for Linux on Windows 部署收集日志并将其捆绑。 它以 `.zip` 文件夹的形式输出捆绑的日志。 有关详细信息，请使用命令 `Get-Help Get-EflowLogs -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Get-EflowVmTpmProvisioningInfo 命令用于收集和显示虚拟机的 vTPM 预配信息。 如果创建虚拟机时未使用 vTPM，则该命令将返回找不到 TPM 预配信息。 有关详细信息，请使用命令 `Get-Help Get-EflowVmTpmProvisioningInfo -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Get-EflowVmAddr 命令用于查找和显示虚拟机的 IP 和 MAC 地址。 它只使用常见参数。 有关详细信息，请使用命令 `Get-Help Get-EflowVmAddr -full`。

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

Get-EflowVmSystemInformation 命令用于收集和显示虚拟机中的系统信息，例如内存和存储空间使用情况。 有关详细信息，请使用命令 `Get-Help Get-EflowVmSystemInformation -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

Get-EflowVmEdgeInformation 命令用于收集和显示虚拟机中的 IoT Edge 信息，如虚拟机运行的 IoT Edge 的版本。 有关详细信息，请使用命令 `Get-Help Get-EflowVmEdgeInformation -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

Get-EflowVmEdgeModuleList 命令用于查询和显示在虚拟机上运行的 IoT Edge 模块的列表。 有关详细信息，请使用命令 `Get-Help Get-EflowVmEdgeModuleList -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

Get-EflowVmEdgeStatus 命令用于查询和显示在虚拟机上的 IoT Edge 运行时的状态。 有关详细信息，请使用命令 `Get-Help Get-EflowVmEdgeStatus -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

Get-EflowVmUserName 命令用于查询和显示从注册表创建虚拟机所用的虚拟机用户名。 它只使用常见参数。 有关详细信息，请使用命令 `Get-Help Get-EflowVmUserName -full`。

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

Get-EflowVmSshKey 命令用于查询和显示虚拟机使用的 SSH 密钥。 它只使用常见参数。 有关详细信息，请使用命令 `Get-Help Get-EflowVmSshKey -full`。

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

Ssh-EflowVm 命令用于通过 SSH 连接到虚拟机。 唯一被允许通过 SSH 登录到虚拟机的帐户是创建该虚拟机的用户。 有关详细信息，请使用命令 `Get-Help Ssh-EflowVm -full`。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 不超过 30 个字符 | 用于登录到虚拟机的用户名。 |

## <a name="next-steps"></a>后续步骤

在以下文章中了解如何使用这些命令：

* [安装 Azure IoT Edge for Linux on Windows](./how-to-install-iot-edge-windows-on-windows.md)

* 有关 PowerShell 提供的所有命令，请参阅 [IoT Edge for Linux on Windows PowerShell 脚本参考](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow)。
