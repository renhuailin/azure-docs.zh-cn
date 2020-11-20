---
title: Red Hat Enterprise Linux 扩展生命周期支持
description: 了解如何添加 Red Hat Enterprise 扩展生命周期支持外接
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 71ff00dbf32ef32ead85be60cdbe53f23c8e35c7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957565"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) 扩展生命周期支持
本文提供针对 Red Hat Enterprise 映像的扩展生命周期支持的信息：
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 生命周期
从2020年11月30日起，Red Hat Enterprise Linux 6 将到达维护阶段结束。 维护阶段后跟扩展生存期。 由于 Red Hat Enterprise Linux 6 转换为完整/维护阶段，强烈建议升级到 Red Hat Enterprise Linux 7 或8。 如果客户必须停留在 Red Hat Enterprise Linux 6 上，则建议 (ELS) 外接程序中添加 Red Hat Enterprise Linux 扩展生命周期支持。

## <a name="frequently-asked-questions"></a>常见问题

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>我正在运行 Red Hat Enterprise Linux 6，此时无法迁移到更高版本。 我有哪些选择？
* 继续运行 Red Hat Enterprise Linux 6，并 (ELS) Add-On 存储库购买延长的生命周期支持，以继续接收有限的软件维护和技术支持 (请参阅下面) 的升级和定价详细信息。
* 尽快迁移到 Red Hat Enterprise Linux 7 或8。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>使用 (ELS) 外接程序支持 Red Hat Enterprise Linux 扩展生命周期的额外费用是什么？

|VM 大小|额外的充电时间范围| (USD 的其他货币金额) | 说明|
|---|---|---|---|
| 小型虚拟来宾 ( # B0 = 4 核心)  | 每小时额外费用 | TBC | |
|  | 每月额外费用 | TBC | 对于预订实例 |
|  | 每年额外费用 | TBC | 对于预订实例 |
| 大型虚拟来宾 ( # B0 4 核心)  | 每小时额外费用 | TBC | |
|  | 每月额外费用 | TBC | 对于预订实例 |
|  | 每年额外费用 | TBC | 对于预订实例 |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a> (ELS) 存储库中添加扩展生命周期支持的过程是继续接收软件维护 (bug 和安全修补程序) 并支持 Red Hat Enterprise Linux 6？

即将注册 ELS 的端到端过程将在此处 (最晚30年11月 2020) 。

## <a name="next-steps"></a>后续步骤

* 若要查看 Azure 中 RHEL 映像的完整列表，请参阅 [Red Hat Enterprise Linux (RHEL) azure 中提供的映像](./redhat-imagelist.md)。
* 若要了解有关 Azure Red Hat 更新基础结构的详细信息，请参阅 [适用于 azure 中按需的 RHEL vm 的 Red Hat 更新基础结构](./redhat-rhui.md)。
* 若要了解有关 RHEL BYOS 产品/服务的详细信息，请参阅 [在 Azure 中 Red Hat Enterprise Linux 自带订阅金牌映像](./byos.md)。
* 有关适用于所有版本 RHEL 的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。