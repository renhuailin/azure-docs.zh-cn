---
title: 将 Windows 许可证应用到会话主机虚拟机 - Azure
description: 介绍了如何为 Azure 虚拟桌面 VM 应用 Windows 许可证。
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 44a8c8ab6420db717533e83af9e01a89fad47bad
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205023"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>将 Windows 许可证应用到会话主机虚拟机

如果客户已获得运行 Azure 虚拟桌面工作负载的适当许可，则有资格将 Windows 许可证应用到其会话主机虚拟机，并在无需支付另一个许可证的情况下运行它们。 有关详细信息，请参阅 [Azure 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

## <a name="ways-to-use-your-azure-virtual-desktop-license"></a>Azure 虚拟桌面许可证的使用方法
通过 Azure 虚拟桌面许可，可以将许可证应用到任何在主机池中注册为会话主机并接收用户连接的 Windows 或 Windows Server 虚拟机。 此许可证不会应用到作为文件共享服务器、域控制器等运行的虚拟机。

Azure 虚拟桌面许可证的使用方法有以下几种：
- 可以使用 [Azure 市场产品/服务](./create-host-pools-azure-marketplace.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机会自动应用许可证。
- 可以使用 [GitHub Azure 资源管理器模板](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)创建主机池及其会话主机虚拟机。 以这种方式创建的虚拟机会自动应用许可证。
- 可以将许可证应用到现有的会话主机虚拟机。 为此，请先按照[使用 PowerShell 创建主机池](./create-host-pools-powershell.md)中的说明操作来创建主机池和关联的 VM，再返回到本文了解如何应用许可证。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>将 Windows 许可证应用到会话主机 VM
确保[已安装并配置最新的 Azure PowerShell](/powershell/azure/)。 运行下面的 PowerShell cmdlet，以应用 Windows 许可证：

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>验证会话主机 VM 是否在利用许可权益
在部署 VM 后，运行下面的 cmdlet，以验证许可证类型：
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

已应用 Windows 许可证的会话主机 VM 将显示如下内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

没有应用 Windows 许可证的 VM 将显示如下内容：

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

运行下面的 cmdlet，以查看 Azure 订阅中应用了 Windows 许可证的所有会话主机 VM 的列表：

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>部署 Windows Server 远程桌面服务的要求

如果在部署中将 Windows Server 2019、2016 或 2012 R2 部署为 Azure 虚拟桌面主机，则必须能够从这些虚拟机访问远程桌面服务许可证服务器。 远程桌面服务许可证服务器可以位于本地或 Azure 中。 有关详细信息，请参阅[激活远程桌面服务许可证服务器](/windows-server/remote/remote-desktop-services/rds-activate-license-server)。
