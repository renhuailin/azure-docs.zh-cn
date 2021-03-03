---
title: 适用于 Windows 上的 Linux Azure IoT Edge 的 PowerShell 脚本 |Microsoft Docs
description: Windows PowerShell 脚本上适用于 Linux Azure IoT Edge 的参考信息，用于在 Windows 虚拟机上部署、设置和状态 IoT Edge。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f0af571f67862c91371b01bee6227d5fb6b291be
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744573"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>适用于 Windows 上的 Linux IoT Edge 的 PowerShell 脚本

了解 PowerShell 脚本，这些脚本可用于部署、预配和获取 Windows 虚拟机上 Linux IoT Edge 的状态。

本文中所述的命令来自 `AzureEFLOW.psm1` 文件，该文件可在你的目录下的系统中找到 `WindowsPowerShell` `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` 。

## <a name="deploy-eflow"></a>Deploy-Eflow

**Eflow** 命令是主要的部署方法。 部署命令创建虚拟机、预配文件，并部署 IoT Edge 代理模块。 尽管不需要任何参数，但它们可用于在部署过程中预配 IoT Edge 设备，并在创建期间修改虚拟机的设置。 有关完整列表，请使用命令 `Get-Help Deploy-Eflow -full` 。  

>[!NOTE]
>对于预配类型， **x509** 目前专门使用 [Azure IoT 中心设备预配服务](../iot-dps/about-iot-dps.md)来表示 x509 预配。 当前不支持手动 X509 预配方法。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| eflowVhdxDir | 目录路径 | 部署为 VM 存储 VHDX 文件的目录。 |
| provisioningType | **手动**、 **TPM**、 **X509** 或 **对称** |  定义要用于 IoT Edge 设备的预配类型。 |
| devConnString | 现有 IoT Edge 设备的设备连接字符串 | 用于手动预配 IoT Edge 设备 (**手动**) 的设备连接字符串。 |
| symmKey | 现有 DPS 注册的主键或使用对称密钥注册的现有 IoT Edge 设备的主密钥 | 用于预配 IoT Edge 设备 (**X509** 或 **对称**) 的对称密钥。 |
| scopeId | 现有 DPS 实例的作用域 ID。 | 用于预配 IoT Edge 设备 (**X509** 或 **对称**) 的作用域 ID。 |
| registrationId | 现有 IoT Edge 设备的注册 ID | 用于预配 (**X509** 或 **对称**) IOT EDGE 设备的注册 ID。 |
| identityCertLocVm | 目录路径;必须位于可由服务拥有的文件夹中 `iotedge` | 虚拟机上用于预配 IoT Edge 设备的标识证书的绝对目标路径 (**X509** 或 **对称**) 。 |
| identityCertLocWin | 目录路径 | 用于预配 IoT Edge 设备的 Windows 标识证书的绝对源路径 (**X509** 或 **对称**) 。 |
| identityPkLocVm |  | 目录路径;必须位于可由服务拥有的文件夹中 `iotedge` | 虚拟机上标识私钥的绝对目标路径，用于预配 IoT Edge 设备 (**X509** 或 **对称**) 。 |
| identityPkLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备 (**X509** 或 **对称**) 的标识私钥的绝对源路径。 |
| vmSizeDefintion | 长度不超过30个字符 | 虚拟机的内核数和可用 RAM 的定义。 **默认值**： Standard_K8S_v1。 |
| vmDiskSize | 介于 8 GB 到 256 GB 之间 | 动态扩展虚拟硬盘的最大磁盘大小。 **默认值**： 16 GB。 |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |
| vnetType | **透明** 或 **ICS** | 虚拟交换机的类型。 **默认值**：透明。 |
| vnetName | 长度不超过64个字符 | 虚拟交换机的名称。 **默认值**： External。 |
| enableVtpm | 无 | **开关参数**。 创建启用或禁用 TPM 的虚拟机。 |
| mobyPackageVersion | 长度不超过30个字符 |  要在虚拟机上验证或安装的小鲸鱼包的版本。  **默认值：** 19.03.11。 |
| iotedgePackageVersion | 长度不超过30个字符 | 要在虚拟机上验证或安装的 IoT Edge 包的版本。 **默认值：** 1.1.0。 |
| installPackages | 无 | **开关参数**。 切换后，该脚本将尝试安装小鲸鱼和 IoT Edge 包，而不只是验证包是否存在。 |

## <a name="verify-eflowvm"></a>Verify-EflowVm

EflowVm 命令是一 **项** 公开的功能，用于检查 Windows 虚拟机上是否已创建适用于 Linux 的 IoT Edge。 它只使用通用参数，如果虚拟机已创建，它将返回 **true** ，否则返回 **false** 。 有关其他信息，请使用命令 `Get-Help Verify-EflowVm -full` 。

## <a name="provision-eflowvm"></a>Provision-EflowVm

**预配-EflowVm** 命令会将 IoT Edge 设备的设置信息添加到虚拟机的 IoT Edge `config.yaml` 文件中。 还可以在部署阶段通过设置 **Eflow** 命令中的参数来完成设置。 有关其他信息，请使用命令 `Get-Help Provision-EflowVm -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |
| provisioningType | **手动**、 **TPM**、 **X509** 或 **对称** |  定义要用于 IoT Edge 设备的预配类型。 |
| devConnString | 现有 IoT Edge 设备的设备连接字符串 | 用于手动预配 IoT Edge 设备 (**手动**) 的设备连接字符串。 |
| symmKey | 现有 DPS 注册的主键或使用对称密钥注册的现有 IoT Edge 设备的主密钥 | 用于预配 IoT Edge 设备 (**DPS**、 **对称**) 的对称密钥。 |
| scopeId | 现有 DPS 实例的作用域 ID。 | 用于预配 (**DPS**) IoT Edge 设备的作用域 ID。 |
| registrationId | 现有 IoT Edge 设备的注册 ID | 用于预配 (**DPS**) IoT Edge 设备的注册 ID。 |
| identityCertLocVm | 目录路径;必须位于可由服务拥有的文件夹中 `iotedge` | 用于预配 IoT Edge 设备 (**DPS**、 **X509**) 的虚拟机上的标识证书的绝对目标路径。 |
| identityCertLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备 (**dps**、 **X509**) 的标识证书的绝对源路径。 |
| identityPkLocVm |  | 目录路径;必须位于可由服务拥有的文件夹中 `iotedge` | 用于预配 IoT Edge 设备 (**DPS**、 **X509**) 的虚拟机上标识私钥的绝对目标路径。 |
| identityPkLocWin | 目录路径 | Windows 中用于预配 IoT Edge 设备 (**dps**、 **X509**) 的标识私钥的绝对源路径。 |

## <a name="get-eflowvmname"></a>Get-EflowVmName

**EflowVmName** 命令用于查询虚拟机的当前主机名。 此命令存在的原因是 Windows 主机名可能会随时间而改变。 它只使用通用参数，它将返回虚拟机的当前主机名。 有关其他信息，请使用命令 `Get-Help Get-EflowVmName -full` 。

## <a name="get-eflowlogs"></a>Get-EflowLogs

**EflowLogs** 命令用于从适用于 Linux 的 Windows 部署上的 IoT Edge 收集日志并将其绑定。 它以文件夹的形式输出捆绑的日志 `.zip` 。 有关其他信息，请使用命令 `Get-Help Get-EflowLogs -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**EflowVmTpmProvisioningInfo** 命令用于收集和显示虚拟机的 vTPM 设置信息。 如果创建虚拟机时没有 vTPM，则该命令将返回找不到 TPM 预配信息。 有关其他信息，请使用命令 `Get-Help Get-EflowVmTpmProvisioningInfo -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**EflowVmAddr** 命令用于查找和显示虚拟机的 IP 和 MAC 地址。 它只使用常见参数。 有关其他信息，请使用命令 `Get-Help Get-EflowVmAddr -full` 。

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

**EflowVmSystemInformation** 命令用于收集和显示虚拟机中的系统信息，例如内存和存储使用情况。 有关其他信息，请使用命令 `Get-Help Get-EflowVmSystemInformation -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

**EflowVmEdgeInformation** 命令用于收集和显示虚拟机中的 IoT Edge 信息，如虚拟机运行 IoT Edge 的版本。 有关其他信息，请使用命令 `Get-Help Get-EflowVmEdgeInformation -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

**EflowVmEdgeModuleList** 命令用于查询和显示在虚拟机上运行的 IoT Edge 模块的列表。 有关其他信息，请使用命令 `Get-Help Get-EflowVmEdgeModuleList -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

**EflowVmEdgeStatus** 命令用于查询和显示虚拟机上 IoT Edge 运行时的状态。 有关其他信息，请使用命令 `Get-Help Get-EflowVmEdgeStatus -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

**EflowVmUserName** 命令用于查询和显示从注册表创建虚拟机所用的虚拟机用户名。 它只使用常见参数。 有关其他信息，请使用命令 `Get-Help Get-EflowVmUserName -full` 。

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

**EflowVmSshKey** 命令用于查询和显示虚拟机使用的 SSH 密钥。 它只使用常见参数。 有关其他信息，请使用命令 `Get-Help Get-EflowVmSshKey -full` 。

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

使用 **EflowVm** 命令通过 ssh 连接到虚拟机。 有关其他信息，请使用命令 `Get-Help Ssh-EflowVm -full` 。

| 参数 | 接受的值 | 注释 |
| --------- | --------------- | -------- |
| vmUser | 长度不超过30个字符 | 用于登录到虚拟机的用户名。 |

## <a name="next-steps"></a>后续步骤

在以下文章中了解如何使用这些命令：

* [在 Windows 上安装适用于 Linux 的 Azure IoT Edge](how-to-install-iot-edge-windows.md)

* 有关通过 PowerShell 提供的所有命令，请参阅 [Windows PowerShell 上的适用于 Linux 的 IoT Edge 脚本参考](reference-iot-edge-for-linux-on-windows-scripts.md#Deploy-Eflow) 。
