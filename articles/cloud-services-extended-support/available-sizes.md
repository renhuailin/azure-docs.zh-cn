---
title: Azure 云服务（外延支持）的可用大小
description: Azure 云服务（外延支持）部署的可用大小
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8075176302e81788b3b180501c951c27ee4105eb
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434153"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure 云服务（外延支持）的可用大小

本文介绍云服务（外延支持）实例的可用虚拟机大小。   

| SKU 系列 |  ACU/核心 | 
|---|---|
|[Av2](../virtual-machines/av2-series.md) | 100 | 
|[D](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#d-series) | 160 | 
|[Dv2](../virtual-machines/dv2-dsv2-series.md) | 160 - 190* |
|[Dv3](../virtual-machines/dv3-dsv3-series.md) | 160 - 190* |
|[Dav4](../virtual-machines/dav4-dasv4-series.md) | 230 - 260 |
|[Eav4](../virtual-machines/eav4-easv4-series.md) | 230 - 260 |
|[Ev3](../virtual-machines/ev3-esv3-series.md) | 160 - 190* |
|[G](../virtual-machines/sizes-previous-gen.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#g-series) | 180-240* |
|[H](../virtual-machines/h-series.md) | 290 - 300* | 

>[!NOTE]
> ACU 标有 *使用 Intel® Turbo 技术来增加 CPU 频率，并提升性能。 提升量可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

## <a name="configure-sizes-for-cloud-services-extended-support"></a>配置云服务（外延支持）的大小

可以在服务定义文件中指定角色实例的虚拟机大小作为服务模型的一部分。 角色大小决定了 CPU 核心数目、内存容量，以及本地文件系统大小。

例如，将 Web 角色实例大小设置为 `Standard_D2`： 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>更改现有角色的大小

若要更改现有角色的大小，请在服务定义文件 (csdef) 中更改虚拟机大小，重新打包云服务并重新进行部署。 

## <a name="get-a-list-of-available-sizes"></a>获取可用大小的列表 

若要检索可用大小列表，请参阅[资源 SKU - 列表](/rest/api/compute/resourceskus/list)并应用以下筛选器：

```powershell
    # Update the location
    $location = 'WestUS2'
    # Get all Compute Resource Skus
    $allSkus = Get-AzComputeResourceSku
    # Filter virtualMachine skus for given location
    $vmSkus = $allSkus.Where{$_.resourceType -eq 'virtualMachines' -and $_.LocationInfo.Location -like $location}
    # From filtered virtualMachine skus, select PaaS Skus
    $passVMSkus = $vmSkus.Where{$_.Capabilities.Where{$_.name -eq 'VMDeploymentTypes'}.Value.Contains("PaaS")}
    # Optional step to format and sort the output by Family
    $passVMSkus | Sort-Object Family, Name | Format-Table -Property Family, Name, Size
```

## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
