---
title: include 文件
description: include 文件
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: 6d2e50a5154a74fc2521da6032d8bfcd8d34f4d6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364896"
---
Azure 计算提供独立于特定硬件类型并专用于单个客户的虚拟机大小。 独立大小在特定的硬件生成上有效并运行，当硬件生成失效时，将弃用。

独立的虚拟机大小最适合于由于满足符合性和法规要求等原因而需要与其他客户的工作负载高度隔离的工作负载。  使用独立大小可保证你的虚拟机将是在特定服务器实例上唯一运行的虚拟机。 


另外，由于独立大小的 VM 很大，客户可以选择使用 [对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)来细分这些 VM 的资源。

当前的独立虚拟机产品/服务包括：
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_M128ms
* Standard_DC8_v2


> [!NOTE]
> 独立的 VM 大小具有有限的硬件寿命。 详情请参阅下文

## <a name="deprecation-of-isolated-vm-sizes"></a>弃用独立的 VM 大小

独立 VM 大小的寿命受硬件限制。 Azure 将会在这些大小的正式弃用日期之前 12 个月发出提醒，并且将会提供更新的独立产品以供你考虑。

| 大小 | 隔离停用日期 | 
| --- | --- |
| Standard_DS15_v2 | 2021 年 5 月 15 日 |
| Standard_D15_v2  | 2021 年 5 月 15 日 |
| Standard_G5  | 2022 年 2 月 15 日 |
| Standard_GS5  | 2022 年 2 月 15 日 |
| Standard_E64i_v3  | 2022 年 2 月 15 日 |
| Standard_E64is_v3  | 2022 年 2 月 15 日 |
| Standard_DC8_v2 | 2022 年 2 月 15 日 |


## <a name="faq"></a>常见问题解答
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>问：是要停用大小还是只停用它的“隔离”功能？
答：目前仅停用 VM 大小的隔离功能。 弃用的独立大小将继续以非独立状态存在。 如果不需要隔离，则不需要执行任何操作，VM 将继续按预期工作。

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>问：当我的虚拟机落脚于非隔离的硬件上时，是否会出现停机？
**答**：如果不需要隔离，就不需要采取任何行动，也不会有停机时间。 相反，如果需要隔离，我们将会在公告中建议用于替换的大小。 如果选择替换大小，我们的客户将会需要调整其 VM 的大小。  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>问：迁移到非独立的虚拟机是否有成本增量？
**答**：否

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>问：其他独立大小将于何时停用？
**答**：我们将提前 12 个月进行提醒，以防官方弃用孤立的大小。 我们的最新公告包含 Standard_G5、Standard_GS5、Standard_E64i_v3 和 Standard_E64i_v3 的隔离功能停用。  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>问：我是依赖于白银或黄金耐久性层级的 Azure Service Fabric 客户。 此更改是否会影响我？
**答**：否。 Service Fabric 的[耐久性层级](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)提供的保证即使在此更改发生后也将继续履行。 如果你出于其他原因而需要物理硬件隔离，可能仍需采取上述措施之一。 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>问：D15_v2 或 DS15_v2 隔离停用有哪些里程碑？ 
**A**： 
 
| 日期 | 操作 |
|---|---| 
| 2020 年 5 月 15 日<sup>1</sup> | D/DS15_v2 隔离停用公告| 
| 2021 年 5 月 15 日 | D/DS15_v2 隔离保证被取消| 

<sup>1</sup> 使用这些大小的现有客户将会收到公告电子邮件，其中包含有关后续步骤的详细说明。  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>问：G5、Gs5、E64i_v3 和 E64is_v3 隔离停用有哪些里程碑？ 
**A**： 
 
| 日期 | 操作 |
|---|---|
| 2021 年 2 月 15 日<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 隔离停用公告 |
| 2022 年 2 月 28 日 | G5/GS5/E64i_v3/E64is_v3 隔离保证被取消 |

<sup>1</sup> 使用这些大小的现有客户将会收到公告电子邮件，其中包含有关后续步骤的详细说明。  

## <a name="next-steps"></a>后续步骤

客户还可以选择利用[对嵌套虚拟机的 Azure 支持](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)，对这些独立的虚拟机资源进一步细分。
