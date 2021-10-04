---
title: Av1 系列停用
description: Av1 系列 VM 大小的停用信息。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: cfc30046d33ad9e6f91b866dc5653fbbcbdbd995
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577725"
---
# <a name="av1-series-retirement"></a>Av1 系列停用

我们将在 2024 年 8 月 31 日停用基本和标准 A 系列 VM。 在此日期之前，你需要将工作负载迁移到 Av2 系列 VM，这些 VM 为每个 vCPU 提供更多内存，并且在固态硬盘 (SSD) 上提供更快的存储。

> [!NOTE]
> 在某些情况下，用户必须先解除分配 VM，然后再重设大小。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>将工作负载从基本和标准 A 系列 VM 迁移到 Av2 系列 VM 

可以使用 [Azure 门户、PowerShell 或 CLI](resize-vm.md) 将虚拟机大小调整为 Av2 系列。 以下示例显示了如何使用 Azure 门户和 PowerShell 重设 VM 大小。 

> [!IMPORTANT]
> 重设虚拟机大小将导致重启。 建议执行要导致在非高峰工作时间进行重启的操作。 

### <a name="azure-portal"></a>Azure 门户 
1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在搜索中键入“虚拟机”。
1. 在“服务”下，选择“虚拟机” 。
1. 在“虚拟机”页中，选择要重设其大小的虚拟机。
1. 在左侧菜单中，选择“大小”。
1. 从可用大小列表中选取新的 Av2 大小，然后选择“重设大小”。

### <a name="azure-powershell"></a>Azure PowerShell
1. 设置资源组和 VM 名称变量。 将值替换为要重设大小的 VM 的信息。 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. 列出托管 VM 的硬件群集上可用的 VM 大小。

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. 将 VM 大小重设为新大小。

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>帮助和支持

如果有疑问，请在 [Microsoft Q&A](/answers/topics/azure-virtual-machines.html) 中询问社区专家。 如果你有支持计划并需要技术帮助，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 对于“问题类型”，选择“技术”。
1. 对于“订阅”，请选择自己的订阅。 
1. 对于“服务”，请单击“我的服务”。
1. 对于“服务类型”，请选择“运行 Windows/Linux 的虚拟机”。
1. 有关摘要，请输入请求摘要。
1. 对于“问题类型”，请选择“协助调整 VM 大小”。
1. 对于“问题子类型”，请选择适用选项。

## <a name="next-steps"></a>后续步骤
详细了解 [Av2 系列 VM](av2-series.md)
