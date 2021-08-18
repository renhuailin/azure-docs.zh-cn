---
title: 从 Azure 开发测试实验室的另一个实验室中导入虚拟机
description: 了解如何在 Azure 开发测试实验室中将虚拟机从另一个实验室导入到当前实验室
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3e2cce7a364254798c88697d942041c1503af193
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300428"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>从 Azure 开发测试实验室的另一个实验室中导入虚拟机
本文介绍如何将虚拟机从另一个实验室导入用户实验室。

## <a name="scenarios"></a>方案
以下是需要将虚拟机从一个实验室导入到另一个实验室的一些方案：

- 团队成员正在迁移到企业内的另一个组，希望将开发人员桌面转到新团队的开发测试实验室。
- 组已达到[订阅级别配额](../azure-resource-manager/management/azure-subscription-service-limits.md)，并且想要将团队拆分到多个订阅。
- 公司计划移到 Express Route（或其他一些新的网络拓扑）且团队想要移动虚拟机以使用此新基础结构。

## <a name="solution-and-constraints"></a>解决方案和约束
此功能使用户能够将一个实验室（源）中的 Vm 导入到另一个实验室（目标) 。 用户可以选择在过程中为目标 VM 提供新名称。 导入过程包括如磁盘、计划、网络设置等所有依赖项。

此过程需要一段时间，并受以下因素影响：

- 附加到源计算机的磁盘的数量/大小 (因为它是复制操作，而不是移动操作)
- 与目标的距离 (例如，美国东部区域到东南亚) 。

导入过程完成后，源虚拟机保持关闭，新虚拟机在目标实验室中运行。

计划将 VM 从一个实验室导入到另一个实验室时要注意两个关键约束因素：

- 支持在订阅之间和跨区域导入虚拟机，但订阅必须与同一 Azure Active Directory 租户相关联。
- 虚拟机不能在源实验室中处于可认领状态。
- 用户是源实验室中虚拟机的所有者，也是目标实验室中实验室的所有者。
- 目前只能通过 Powershell 和 REST API 支持此功能。

## <a name="use-powershell"></a>使用 PowerShell
从 [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines)下载 ImportVirtualMachines.ps1 文件。 用户可以使用脚本将源实验室中的单个 VM 或所有 Vm 导入到目标实验室。

### <a name="use-powershell-to-import-a-single-vm"></a>使用 PowerShell 导入单个 VM
执行此 powershell 脚本需要标识源 VM 和目标实验室，并选择性地提供要用于目标计算机的新名称：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>使用 PowerShell 导入源实验室中的所有 VM
如果未指定源虚拟机，则脚本会自动导入开发测试实验室中的所有 VM。  例如：

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>使用 HTTP REST 导入 VM
REST 调用非常简单。 用户可以为标识源资源和目标资源而获得足够的信息。 请记住，操作发生在目标实验室资源上。

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [设置实验室的策略](devtest-lab-set-lab-policy.md)
- [常见问题解答](devtest-lab-faq.yml)
