---
title: 弃用使用 Azure Site Recovery 在客户托管站点（使用 VMM）之间进行灾难恢复 | Microsoft Docs
description: 详细介绍了 Azure 即将弃用在客户拥有的站点（使用 Hyper-V）之间和在由 SCVMM 托管的站点之间进行 DR 以及备用选项
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421835"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>弃用使用 Azure Site Recovery 在客户托管站点（使用 VMM）之间进行灾难恢复

本文介绍了即将推出的弃用计划、可能引起的相应后果，以及在以下方案中为客户提供的备用选项：

使用 Site Recovery 在客户拥有的由 System Center Virtual Machine Manager (SCVMM) 托管的站点之间进行 DR

> [!IMPORTANT]
> 建议客户尽早执行修正步骤，以免对其环境造成任何中断。 

## <a name="what-changes-should-you-expect"></a>预计应该会有哪些变更？

- 自 2020 年 3 月起，你将收到 Azure 门户通知和电子邮件通信，告知你即将弃用 Hyper-V VM 的站点到站点复制。 弃用计划在 2023 年 3 月进行。

- 如果你有现有配置，则不会对设置产生任何影响。

- 在方案被弃用后，除非客户遵循备用方法，否则现有复制可能会中断。 客户无法通过 Azure Site Recovery 体验在 Azure 门户中查看、管理或执行任何与 DR 相关的操作。
 
## <a name="alternatives"></a>备选方法 

下面是客户可以选择的备用方法，以确保在方案被弃用后，其 DR 策略不会受到影响。 

- 选项 1（推荐）：选择[开始使用 Azure 作为 DR 目标](hyper-v-vmm-azure-tutorial.md)。


- 选项 2：选择继续使用基础 [Hyper-V 副本解决方案](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)执行站点到站点复制，但你将无法使用 Azure Site Recovery 在 Azure 门户中管理 DR 配置。 


## <a name="remediation-steps"></a>修正步骤

如果你选择使用选项 1，请执行以下步骤：

1. [禁用保护所有与 VMM 关联的虚拟机](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario)。 使用“禁用复制并删除”选项或运行提及的脚本，以确保清理本地的复制设置。 

2. [为所有 VMM 服务器取消注册](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)站点到站点复制配置。

3. [准备 Azure 资源](tutorial-prepare-azure-for-hyperv.md)，以启用 VM 复制。
4. [准备本地 Hyper-V 服务器](hyper-v-prepare-on-premises-tutorial.md)
5. [为 VMM 云中的 VM 设置复制](hyper-v-vmm-azure-tutorial.md)
6. 可选但建议执行：[运行 DR 钻取](tutorial-dr-drill-azure.md)

如果你选择使用选项 2（即使用 Hyper-V 副本），请执行以下步骤：

1. 依次转到“受保护的项” > “复制的项”，右键单击计算机，再单击“禁用复制”。
2. 在“禁用复制”中，选择“删除”。

    此选项会从 Azure Site Recovery 中删除复制的项（停止计费）。 不过，并不会清理本地虚拟机上的复制配置。 

## <a name="next-steps"></a>后续步骤
为弃用做好计划，然后选择最适合你的基础结构和业务的备用选项。 如果你对此有任何疑问，请联系 Microsoft 支持部门

