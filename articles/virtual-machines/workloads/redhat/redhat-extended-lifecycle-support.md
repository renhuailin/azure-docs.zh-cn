---
title: Red Hat Enterprise Linux 扩展生命周期支持
description: 了解如何添加 Red Hat Enterprise Linux 扩展生命周期支持附加产品
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 82aa54f1b9aea331c7c08128487fbc9711cac2df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696836"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) 扩展生命周期支持

**适用于：** :heavy_check_mark: Linux VM 

本文提供有关 Red Hat Enterprise 映像扩展生命周期支持的信息：
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 生命周期
从 2020 年 11 月 30 起，Red Hat Enterprise Linux 6 的维护阶段结束。 维护阶段之后是扩展生命阶段。 由于 Red Hat Enterprise Linux 6 结束了完整/维护阶段，因此强烈建议升级到 Red Hat Enterprise Linux 7 或 8。 如果客户必须保留 Red Hat Enterprise Linux 6，建议添加 Red Hat Enterprise Linux 扩展生命周期支持 (ELS) 附加产品。

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>在市场即用即付 VM 上添加扩展生命周期支持的步骤
1. 在[此处提供的 ELS 表单](https://aka.ms/els-form)中填写你的联系详细信息，以及要为其添加 ELS 支持的 VM 的订阅信息。 此表单中还提供了附加产品定价详细信息。
1. Azure Red Hat Enterprise Linux 团队将在 1-2 个工作日内与你联系，通过 VM 列表 为你提供 ELS 支持附加产品信息。 请查看该列表，并回复是否同意附加产品定价。
1. Azure Red Hat Enterprise Linux 团队将共享向 VM 添加 ELS 客户端包的步骤。 按照电子邮件中提供的步骤操作，继续接受软件维护（bug 和安全修复）和对 Red Hat Enterprise Linux 6 的支持。

> [!Note]
> 请勿与组织外部的任何人共享使用 RHEL ELS 附加产品的步骤。 若要获取支持或有任何其他疑问，请与 AzureRedHatELS@microsoft.com 联系。

## <a name="frequently-asked-questions"></a>常见问题

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>我正在运行 Red Hat Enterprise Linux 6，并且目前无法迁移到更高版本。 我有哪些选择？
* 继续运行 Red Hat Enterprise Linux 6 并购买扩展生命周期支持 (ELS) 附加产品存储库，以继续接受有限的软件维护和技术支持（请参阅下面的升级和定价详细信息）。
* 尽快迁移到 Red Hat Enterprise Linux 7 或 8。

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>使用 Red Hat Enterprise Linux 扩展生命周期支持 (ELS) 附加产品的额外费用是多少？
可以在 [ELS 表单](https://aka.ms/els-form)中查看与扩展生命周期支持相关的费用

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>我使用自定义映像部署了一个 VM。 如何为该 VM 添加扩展生命周期支持？
你需要直接联系 Red Hat 并直接从他们那里获得支持。

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>我使用自定义映像部署了一个 VM。 可否将此 VM 转换为 PAYG VM？
否，不能。 Azure 上目前不支持此转换。


## <a name="next-steps"></a>后续步骤

* 若要查看 Azure 中 RHEL 映像的完整列表，请参阅 [Azure 中提供的 Red Hat Enterprise Linux (RHEL) 映像](./redhat-imagelist.md)。
* 若要了解有关 Azure Red Hat 更新基础结构的详细信息，请参阅 [Azure 中按需 RHEL VM 的 Red Hat 更新基础结构](./redhat-rhui.md)。
* 若要了解有关 RHEL BYOS 产品/服务的详细信息，请参阅 [Azure 中的 Red Hat Enterprise Linux 自带订阅黄金映像](./byos.md)。
* 有关 RHEL 所有版本的 Red Hat 支持策略的信息，请参阅 [Red Hat Enterprise Linux 生命周期](https://access.redhat.com/support/policy/updates/errata)。