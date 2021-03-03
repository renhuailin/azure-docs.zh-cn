---
title: 使用 Azure Policy 在创建 VM 时自动启用备份
description: 本文介绍如何使用 Azure Policy 为在给定范围内创建的所有 VM 自动启用备份
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: dfa4364eeaa9f5b60af3f5d6a19aaeb188d4f65e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707296"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>使用 Azure Policy 在创建 VM 时自动启用备份

在一个组织中，备份或法规符合性管理员的主要职责之一是确保所有业务关键型计算机都以适当的保留期进行备份。

如今，Azure 备份提供各种内置策略 (使用 [Azure 策略](../governance/policy/overview.md)) 来帮助你自动确保将 Azure 虚拟机配置为进行备份。 根据备份团队和资源的组织方式，可以使用以下任一策略：

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>策略 1-将无给定标记的 Vm 上的备份配置到相同位置的现有恢复服务保管库

如果你的组织有一个管理跨应用程序团队的备份的中心备份团队，则可以使用此策略将备份配置为与受管辖 Vm 相同的订阅和位置中的现有中央恢复服务保管库的备份。 你可以选择从此策略的作用域中 **排除** 包含特定标记的 vm。

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>策略 2-[预览] 在同一位置的现有恢复服务保管库中对具有给定标记的 Vm 配置备份
此策略的工作方式与上面的策略1相同，唯一的区别在于，您可以使用此策略将包含特定标记的 vm **包含** 在此策略的范围内。 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>策略 3-[预览] 在没有给定标记的情况下，在具有默认策略的新恢复服务保管库中配置备份
如果组织专用资源组中的应用程序，并且想要通过同一保管库来备份这些应用程序，则可以使用此策略来自动管理此操作。 你可以选择从此策略的作用域中 **排除** 包含特定标记的 vm。

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>策略 4-[预览] 使用默认策略将具有给定标记的 Vm 上的备份配置到新的恢复服务保管库
此策略的工作方式与上述策略3相同，唯一的区别在于，您可以使用此策略将包含特定标记的 vm **包含** 在此策略的作用域中。 

除此之外，Azure 备份还提供 [仅审核](../governance/policy/concepts/effects.md#audit) 策略- **应为虚拟机启用 Azure 备份**。 此策略标识哪些虚拟机未启用备份，但不会为这些 Vm 自动配置备份。 当你只想评估 Vm 的总体符合性，但不希望立即采取措施时，这非常有用。

## <a name="supported-scenarios"></a>支持的方案

* 内置策略当前仅支持 Azure VM。 用户必须确保分配期间指定的保留策略是 VM 保留策略。 若要查看此策略支持的所有 VM SKU，请参阅[此](./backup-azure-policy-supported-skus.md)文档。

* 策略1和2可以一次分配到一个位置和订阅。 若要跨位置和订阅启用 VM 备份，需要创建策略分配的多个实例，位置和订阅的每个组合都需要创建一个实例。

* 对于策略1和2，当前不支持管理组范围。

* 对于策略1和2，指定的保管库和为备份配置的 Vm 可以在不同的资源组下。

* 策略1、2、3和4目前在国家云中不可用。

* 可以将策略3和4一次分配到单个订阅 (或订阅中的资源组) 。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>使用内置策略

下面的步骤介绍了向给定作用域的 **同一位置中的现有恢复服务保管库中** 分配策略1的端到端过程。 类似的说明适用于其他策略。 分配后，将自动为该作用域中创建的任何新 VM 配置备份。

1. 登录到 Azure 门户并导航到“策略”仪表板。
2. 在左边的菜单中选择“定义”以获取跨 Azure 资源的所有内置策略的列表。
3. 筛选 " **类别 = 备份** " 列表，并选择名为 "在同一位置的现有中央保管库的 Vm 上配置备份" 的策略。
![Policy 仪表板](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 选择该策略的名称。 随后会重定向到该策略的详细定义。
![策略定义窗格](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 选择窗格顶部的“分配”按钮。 随后会重定向到“分配策略”窗格。
6. 在“基础”下，选择“范围”字段旁边的三个点 。 随即在右侧打开一个上下文窗格，可以在其中选择要应用策略的订阅。 还可以选择资源组，使该策略仅应用于特定资源组中的 VM。
![策略分配基础知识](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. 在“参数”选项卡中，从下拉列表中选择一个位置，然后选择范围中的 VM 必须关联的保管库和备份策略。 还可以选择指定标记名称和标记值的数组。 将从策略分配的作用域中排除包含给定标记的任何指定值的 VM。
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