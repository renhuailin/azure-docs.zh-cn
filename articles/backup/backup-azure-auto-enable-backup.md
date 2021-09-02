---
title: 使用 Azure Policy 在创建 VM 时自动启用备份
description: 本文介绍如何使用 Azure Policy 为在给定范围内创建的所有 VM 自动启用备份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: fe70ba544bb75cee7faf3a4eb3aa2f5ef440909e
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300603"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure Policy 在创建 VM 时自动启用备份

在一个组织中，备份或法规符合性管理员的主要职责之一是确保所有业务关键型计算机都以适当的保留期进行备份。

如今，Azure 备份提供各种内置策略（使用 [Azure 策略](../governance/policy/overview.md)）来帮助你自动确保将 Azure 虚拟机配置为进行备份。 根据备份团队和资源的组织方式，可以使用以下任一策略：

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>策略 1 - 将不带给定标记的 VM 上的备份配置到同一位置的现有恢复服务保管库

如果你的组织有一个中心备份团队，负责管理跨应用程序团队的备份，则可以使用该策略将备份配置为与受管辖 VM 相同的订阅和位置中的现有中央恢复服务保管库。 你可以选择从该策略的作用域中排除包含特定标记的 VM。

## <a name="policy-2---configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>策略 2 - 将具有给定标记的 VM 上的备份配置到同一位置的现有恢复服务保管库
该策略的工作方式与上面的策略 1 相同，唯一的区别在于，您可以使用该策略将包含特定标记的 VM 包含在该策略的作用域内。 

## <a name="policy-3---configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>策略 3 - 使用默认策略将不带给定标记的 VM 上的备份配置到新的恢复服务保管库
如果你通过专用资源组来组织应用程序，并且想要通过同一保管库来备份这些应用程序，则可以使用此策略来自动管理此操作。 你可以选择从该策略的作用域中排除包含特定标记的 VM。

## <a name="policy-4---configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>策略 4 - 使用默认策略将具有给定标记的 VM 上的备份配置到新的恢复服务保管库
该策略的工作方式与上面的策略 3 相同，唯一的区别在于，您可以使用该策略将包含特定标记的 VM 包含在该策略的作用域内。 

除此之外，Azure 备份还提供[“仅审核”](../governance/policy/concepts/effects.md#audit)策略“应为虚拟机启用 Azure 备份”。 该策略只是标识哪些虚拟机未启用备份，但不会为这些 VM 自动配置备份。 如果只是想评估 VM 的总体合规性，但不希望立即采取措施时，该策略非常有用。

## <a name="supported-scenarios"></a>支持的方案

* 内置策略当前仅支持 Azure VM。 用户必须确保分配期间指定的保留策略是 VM 保留策略。 若要查看此策略支持的所有 VM SKU，请参阅[此](./backup-azure-policy-supported-skus.md)文档。

* 策略 1 和 2 一次可以分配给一个位置和订阅。 若要跨位置和订阅启用 VM 备份，需要创建策略分配的多个实例，位置和订阅的每个组合都需要创建一个实例。

* 对于策略 1 和 2，当前不支持管理组作用域。

* 对于策略 1 和 2，指定的保管库和为备份配置的 VM 可以位于不同的资源组下。

* 可以将策略 3 和 4一次分配到单个订阅（或订阅中的资源组）。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>使用内置策略

下面的步骤介绍了向给定作用域分配策略 1 的过程：**将不带给定标记的 VM 上的备份配置到同一位置的现有恢复服务保管库**。 类似的说明也适用于其他策略。 分配后，将自动为该作用域中创建的任何新 VM 配置备份。

1. 登录到 Azure 门户并导航到“策略”仪表板。
2. 在左边的菜单中选择“定义”以获取跨 Azure 资源的所有内置策略的列表。
3. 筛选“Category=Backup”列表并选择名为“将某个位置的 VM 上的备份配置为同一位置的现有中央保管库”的策略。
![Policy 仪表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 选择该策略的名称。 随后会重定向到该策略的详细定义。
![策略定义窗格](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 选择窗格顶部的“分配”按钮。 随后会重定向到“分配策略”窗格。
6. 在“基础”下，选择“范围”字段旁边的三个点 。 随即在右侧打开一个上下文窗格，可以在其中选择要应用策略的订阅。 还可以选择资源组，使该策略仅应用于特定资源组中的 VM。
![策略分配基础知识](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在“参数”选项卡中，从下拉列表中选择一个位置，然后选择范围中的 VM 必须关联的保管库和备份策略。 还可选择指定标记名称和标记值数组。 策略分配的范围中将排除包含给定标记的任何指定值的 VM。
![策略分配参数](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. 确保将“效果”设置为 deployIfNotExists。
9. 导航到“查看+创建”，然后选择“创建” 。

> [!NOTE]
>
> 也可以通过使用[修正](../governance/policy/how-to/remediate-resources.md)，在现有 VM 上使用 Azure Policy。

> [!NOTE]
>
> 建议不要一次将此策略分配给超过 200 个 VM。 如果将此策略分配给超过 200 个 VM，则可能导致备份触发时间比计划指定的时间晚几个小时。

## <a name="next-steps"></a>后续步骤

[了解有关 Azure Policy 的详细信息](../governance/policy/overview.md)