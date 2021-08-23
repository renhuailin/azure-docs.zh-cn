---
title: NV 系列停用
description: NV 系列从 2021 年 9 月 1 日起停用
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: fcd0d460837195817018882d92b94b0012a14a6f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254260"
---
# <a name="migrate-your-nv-and-nv_promo-series-virtual-machines-by-august-31-2022"></a>在 2022 年 8 月 31 日之前迁移你的 NV 和 NV_Promo 系列虚拟机
我们仔细计划了如何停用老化的硬件，同时会继续使用数据中心技术的最新创新将新型优化虚拟机实例引入 Azure。
考虑到这一点，我们将于 2022 年 9 月 1 日停用 NV 系列 Azure 虚拟机大小。

## <a name="how-does-the-nv-series-migration-affect-me"></a>NV 系列迁移对我有何影响？  

2022 年 9 月 1 日之后，订阅中剩余的任何 NV 和 NV_Promo 大小的虚拟机都将设置为已解除分配的状态。 这些虚拟机会被停止并从主机中删除。 这些虚拟机在已解除分配状态下将不再计费。 

当前的 VM 大小停用只会影响 [NV 系列](nv-series.md)中的 VM 大小。 不会影响 [NVv3](nvv3-series.md) 和 [NVv4](nvv4-series.md) 系列虚拟机。 

## <a name="what-actions-should-i-take"></a>我应该采取什么措施？  

需要将 NV 虚拟机重设大小或解除分配。 建议将 GPU 可视化/图形工作负荷移到另一个 [GPU 加速虚拟机大小](sizes-gpu.md)。

[详细了解](nv-series-migration-guide.md)如何将工作负荷迁移到其他 GPU Azure 虚拟机大小。 

如果有疑问，请通过客户支持与我们联系。
