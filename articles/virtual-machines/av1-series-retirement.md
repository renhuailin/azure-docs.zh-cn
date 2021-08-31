---
title: Av1 系列停用
description: Av1 系列 VM 大小的停用信息。
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: 6c870673f05c71d2605838001dea347321d320a6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178908"
---
# <a name="av1-series-retirement"></a>Av1 系列停用

2024 年 8 月 31 日，我们将停用基本和标准 A 系列 VM。 在此之前，需要将工作负载迁移到 Av2 系列 VM，这将为每个 vCPU 提供更多内存，并在固态硬盘 (SSD) 上提供更快的存储速度。

> [!NOTE]
> 在某些情况下，用户必须在调整大小之前解除分配 VM。 如果新大小在当前托管 VM 的硬件群集上不可用，则可能会出现这种情况。


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>将工作负载从基本和标准 A 系列 VM 迁移到 Av2 系列 VM 

可以使用 [Azure 门户](https://portal.azure.com)、[PowerShell](windows/resize-vm.md) 和 [CLI](/linux/change-vm-size.md) 将虚拟机大小调整为 Av2 系列。 以下示例展示如何使用 Azure 门户和 PowerShell 调整 VM 大小。 

> [!IMPORTANT]
> 调整虚拟机大小将导致重启。 建议执行的操作将导致在非高峰工作时间重启。 

### <a name="azure-portal"></a>Azure 门户 
1. 打开 [Azure 门户](https://portal.azure.com)。
1. 在搜索中键入“虚拟机”。
1. 在“服务”下，选择“虚拟机” 。
1. 在“虚拟机”页中，选择要调整大小的虚拟机。
1. 在左侧菜单中，选择“大小”。
1. 从可用大小列表中选择新的 Av2 大小，然后选择“调整大小”。

### <a name="azure-powershell"></a>Azure PowerShell
1. 设置资源组和 VM 名称变量。 将值替换为要调整大小的 VM 的信息。 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. 列出托管 VM 的硬件群集上可用的 VM 大小。

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. 将 VM 大小调整为新大小。

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>帮助和支持

如果有疑问，请在 [Microsoft Q&A](/answers/topics/azure-virtual-machines.html) 中咨询社区专家。 如有支持计划并需要技术帮助，请创建[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 对于“问题类型”，选择“技术”。
1. 对于“订阅”，请选择自己的订阅。 
1. 对于“服务”，请单击“我的服务”。
1. 对于“服务类型”，请选择“运行 Windows/Linux 的虚拟机”。
1. 有关摘要，请输入请求摘要。
1. 对于“问题类型”，请选择“协助调整 VM 大小”。
1. 对于“问题子类型”，请选择适用选项。

## <a name="next-steps"></a>后续步骤
详细了解 [Av2 系列 VM](av2-series.md)