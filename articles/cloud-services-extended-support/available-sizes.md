---
title: 'Azure 云服务的可用大小 (扩展支持) '
description: Azure 云服务的可用大小 (扩展支持) 部署
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744174"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure 云服务的可用大小 (扩展支持) 

本文介绍了云服务的可用虚拟机大小 (扩展支持) 实例。   

| SKU 系列 |  ACU/Core | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> ACU 标有 *使用 Intel® Turbo 技术来增加 CPU 频率，并提升性能。 提升量可能因 VM 大小、工作负荷和同一主机上运行的其他工作负荷而有所不同。

## <a name="configure-sizes-for-cloud-services-extended-support"></a> (扩展支持配置云服务的大小) 

你可以在服务定义文件中指定角色实例的虚拟机大小作为服务模型的一部分。 角色大小确定了 CPU 核心数、内存容量和本地文件系统大小。

例如，将 web 角色实例大小设置为 `Standard_D2` ： 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>更改现有角色的大小

若要更改现有角色的大小，请将服务定义文件中的虚拟机大小更改 (工作) ，重新打包云服务并重新部署它。 

## <a name="get-a-list-of-available-sizes"></a>获取可用大小的列表 

若要检索可用大小列表，请参阅 [资源 Sku 列表](https://docs.microsoft.com/rest/api/compute/resourceskus/list) 并应用以下筛选器：


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 查看 [云服务的常见问题 (](faq.md) 扩展支持) 。
- ) 使用 [Azure 门户](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [模板](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)部署云服务 (扩展支持。
