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
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744035"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) 扩展生命周期支持
本文提供针对 Red Hat Enterprise 映像的扩展生命周期支持的信息：
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 生命周期
从2020年11月30日起，Red Hat Enterprise Linux 6 将到达维护阶段结束。 维护阶段后跟扩展生存期。 由于 Red Hat Enterprise Linux 6 转换为完整/维护阶段，强烈建议升级到 Red Hat Enterprise Linux 7 或8。 如果客户必须停留在 Red Hat Enterprise Linux 6 上，则建议 (ELS) 外接程序中添加 Red Hat Enterprise Linux 扩展生命周期支持。

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>添加市场即用即付 Vm 的扩展生命周期支持的步骤
1. 在此处填写所需的 [ELS 窗体](https://aka.ms/els-form) 以及要为其添加 ELS 支持的 vm 的订阅信息。 还提供了窗体中的附加定价详细信息。
1. Azure Red Hat Enterprise Linux 团队将与你联系，其中包含适用于 ELS 支持的 Vm 列表，请在1-2 工作日内增加。 请查看此列表，并同意附加定价。
1. Azure Red Hat Enterprise Linux 团队会分享将 ELS 客户端包添加到 Vm 的步骤。 按照电子邮件中提供的步骤进行操作，以继续接收软件维护 (bug 和安全修补程序) 并支持 Red Hat Enterprise Linux 6。

> [!Note]
> 不要将使用 RHEL ELS 外接程序的步骤与组织外部的任何人共享。 联系以 AzureRedHatELS@microsoft.com 获取支持或其他任何问题。

## <a name="frequently-asked-questions"></a>常见问题

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>我正在运行 Red Hat Enterprise Linux 6，此时无法迁移到更高版本。 我有哪些选择？
* 继续运行 Red Hat Enterprise Linux 6，并 (ELS) Add-On 存储库购买延长的生命周期支持，以继续接收有限的软件维护和技术支持 (请参阅下面) 的升级和定价详细信息。
* 尽快迁移到 Red Hat Enterprise Linux 7 或8。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>使用 (ELS) 外接程序支持 Red Hat Enterprise Linux 扩展生命周期的额外费用是什么？
可以通过[ELS 窗体](https://aka.ms/els-form)找到与扩展生命周期支持相关的成本


## <a name="next-steps"></a>后续步骤

* 若要查看 Azure 中 RHEL 映像的完整列表，请参阅 [Red Hat Enterprise Linux (RHEL) azure 中提供的映像](./redhat-imagelist.md)。
* 若要了解有关 Azure Red Hat 更新基础结构的详细信息，请参阅 [适用于 azure 中按需的 RHEL vm 的 Red Hat 更新基础结构](./redhat-rhui.md)。
* 若要了解有关 RHEL BYOS 产品/服务的详细信息，请参阅 [在 Azure 中 Red Hat Enterprise Linux 自带订阅金牌映像](./byos.md)。
* 有关适用于所有版本 RHEL 的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。