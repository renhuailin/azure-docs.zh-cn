---
title: NC 系列停用
description: NC 系列自 2022 年 8 月 31 日起停用
author: sherrywangms
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 09/01/2021
ms.author: sherrywang
ms.openlocfilehash: e95c32903f601a883b62a06eb1b1901c15042dbb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699394"
---
# <a name="migrate-your-nc-and-nc_promo-series-virtual-machines-by-august-31-2022"></a>请在 2022 年 8 月 31 日之前迁移 NC 和 NC_Promo 系列虚拟机
我们会持续将新式的和经过优化的虚拟机实例引入 Azure，利用数据中心技术的最新创新，因此，我们要仔细计划如何停用老化的硬件。 基于这方面的考虑，我们会从 2022 年 8 月 31 日起停用由 NVIDIA Tesla K80 GPU 提供支持的 NC (v1) GPU VM 大小。 

## <a name="how-does-the-nc-series-migration-affect-me"></a>NC 系列迁移对我有何影响？  

在 2022 年 8 月 31 日之后，订阅中剩余的任何 NC 大小虚拟机都将会被设置为解除分配状态。 这些虚拟机会被停止并从主机中删除。 这些虚拟机在已解除分配状态下将不再计费。 

此次 VM 大小停用只影响 [NC 系列](nc-series.md)中的 VM 大小。 此次停用不影响较新的 [NCv3](ncv3-series.md)、[NC T4 v3](nct4-v3-series.md) 和 [ND v2](ndv2-series.md) 系列虚拟机。 


## <a name="what-actions-should-i-take"></a>我应该采取什么措施？  
需要将 NC 虚拟机重设大小或解除分配。 建议将 GPU 工作负载移至另一个 GPU 虚拟机大小。 请详细了解如何将工作负载迁移至另一个 [GPU 加速虚拟机大小](sizes-gpu.md)。

## <a name="next-steps"></a>后续步骤

[详细了解](n-series-migration.md)如何将工作负荷迁移到其他 GPU Azure 虚拟机大小。 

如果有疑问，请通过客户支持与我们联系。
