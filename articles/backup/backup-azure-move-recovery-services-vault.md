---
title: 如何移动 Azure 备份恢复服务保管库
description: 有关如何跨 Azure 订阅和资源组移动恢复服务保管库的说明。
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: references_regions
ms.openlocfilehash: 6de0d6902705ff0edffd5e51e0e8f9abc6378830
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114298082"
---
# <a name="move-a-recovery-services-vault-across-azure-subscriptions-and-resource-groups"></a>跨 Azure 订阅和资源组移动恢复服务保管库

本文介绍如何跨 Azure 订阅移动针对 Azure 备份配置的恢复服务保管库，或者将其移到同一订阅中的另一个资源组。 可以使用 Azure 门户或 PowerShell 移动恢复服务保管库。

## <a name="supported-regions"></a>支持的区域

在除法国中部、法国南部、德国东北部、德国中部、中国北部、中国北部 2、中国东部和中国东部 2 以外的所有公共区域和主权区域中受支持。

## <a name="prerequisites-for-moving-recovery-services-vault"></a>移动恢复服务保管库的先决条件

- 在跨资源组的保管库移动期间，源和目标资源组都会被锁定，从而阻止了写入和删除操作。 有关详细信息，请参阅[此文](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 只有订阅管理员有权移动保管库。
- 要在订阅之间移动保管库，目标订阅必须与源订阅位于同一租户中，并且其状态必须为“已启用”。 若要将保管库移动到其他 Azure AD，请参阅[将订阅转移到其他目录](../role-based-access-control/transfer-subscription.md)和[恢复服务保管库常见问题解答](/azure/backup/backup-azure-backup-faq#recovery-services-vault)。
- 必须有权对目标资源组执行写入操作。
- 移动保管库只会更改资源组。 恢复服务保管库将位于同一位置，并且无法更改。
- 一次只能在每个区域移动一个恢复服务保管库。
- 如果 VM 无法跨订阅连同恢复服务保管库一起移动，或者无法移到新的资源组，则当前 VM 恢复点在过期之前，会在保管库中保持不变。
- 不管 VM 是否连同保管库一起移动，都始终可以从保管库中保留的备份历史记录还原该 VM。
- Azure 磁盘加密要求密钥保管库和 VM 位于同一 Azure 区域和订阅中。
- 若要移动包含托管磁盘的虚拟机，请参阅[此文](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 移动通过经典模型部署的资源时，其选项各不相同，具体取决于是在订阅中移动资源，还是将资源移到新订阅。 有关详细信息，请参阅[此文](../azure-resource-manager/management/move-resource-group-and-subscription.md)。
- 跨订阅移动保管库或将其移到新资源组后，为保管库定义的备份策略将会保留。
- 只能移动包含以下任何类型的备份项的保管库。 在移动保管库之前，需要停止以下未列出的任何备份项类型，并永久删除数据。
  - Azure 虚拟机
  - Microsoft Azure 恢复服务 (MARS) 代理
  - Microsoft Azure 备份服务器 (MABS)
  - Data Protection Manager (DPM)
- 如果跨订阅移动包含 VM 备份数据的保管库，则必须将 VM 移到同一订阅，并使用同一目标 VM 资源组名称（与旧订阅中的名称相同）来继续备份。

> [!NOTE]
> 不支持跨 Azure 区域移动 Azure 备份的恢复服务保管库。<br><br>
> 如果使用 Azure Site Recovery 为灾难恢复配置了任何 VM（Azure IaaS、Hyper-V、VMware）或物理计算机，移动操作将受阻止。 如果要为 Azure Site Recovery 移动保管库，请[查看本文](../site-recovery/move-vaults-across-regions.md)，了解如何手动移动保管库。

## <a name="use-azure-portal-to-move-recovery-services-vault-to-different-resource-group"></a>使用 Azure 门户将恢复服务保管库移到不同的资源组

将恢复服务保管库及其关联的资源移到不同的资源组：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 打开“恢复服务保管库”列表，并选择要移动的保管库。 当保管库仪表板打开时，其外观如下图所示。

   ![打开恢复服务保管库](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

   如果未看到保管库的“概要”信息，请选择下拉图标。 现在，应会看到保管库的“概要”信息。

   ![“概要”信息选项卡](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保管库概述菜单中，选择“资源组”旁边的“更改”打开“移动资源”窗格。  

   ![更改资源组](./media/backup-azure-move-recovery-services/change-resource-group.png)

4. 在“移动资源”窗格中，对于选定的保管库，建议通过选中下图所示的复选框，移动可选的相关资源。

   ![移动订阅](./media/backup-azure-move-recovery-services/move-resource.png)

5. 若要添加目标资源组，请在“资源组”下拉列表中选择现有的资源组，或选择“创建新组”选项。 

   ![创建资源](./media/backup-azure-move-recovery-services/create-a-new-resource.png)

6. 添加资源组后，确认“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”选项，然后选择“确定”完成保管库移动操作。 

   ![确认消息](./media/backup-azure-move-recovery-services/confirmation-message.png)

## <a name="use-azure-portal-to-move-recovery-services-vault-to-a-different-subscription"></a>使用 Azure 门户将恢复服务保管库移到不同的订阅

可将恢复服务保管库及其关联的资源移到不同的订阅

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 打开“恢复服务保管库”列表，并选择要移动的保管库。 当保管库仪表板打开时，其外观如下图所示。

    ![打开恢复服务保管库](./media/backup-azure-move-recovery-services/open-recover-service-vault.png)

    如果未看到保管库的“概要”信息，请选择下拉图标。 现在，应会看到保管库的“概要”信息。

    ![“概要”信息选项卡](./media/backup-azure-move-recovery-services/essentials-information-tab.png)

3. 在保管库概述菜单中，选择“订阅”旁边的“更改”打开“移动资源”窗格。  

   ![更改订阅](./media/backup-azure-move-recovery-services/change-resource-subscription.png)

4. 选择要移动的资源。此处我们建议使用“全选”选项来选择列出的所有可选资源。

   ![移动资源](./media/backup-azure-move-recovery-services/move-resource-source-subscription.png)

5. 在“订阅”下拉列表中选择目标订阅，保管库将移到该订阅。
6. 若要添加目标资源组，请在“资源组”下拉列表中选择现有的资源组，或选择“创建新组”选项。 

   ![添加订阅](./media/backup-azure-move-recovery-services/add-subscription.png)

7. 选择“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”选项进行确认，然后选择“确定”。 

> [!NOTE]
> 不支持跨订阅的备份（RS 保管库和受保护 VM 位于不同的订阅中）。 此外，在执行保管库移动操作期间，无法将存储冗余选项从本地冗余存储 (LRS) 修改为全局冗余存储 (GRS)，反之亦然。
>
>

## <a name="use-powershell-to-move-recovery-services-vault"></a>使用 PowerShell 移动恢复服务保管库

若要将恢复服务保管库移到另一个资源组，请使用 `Move-AzureRMResource` cmdlet。 `Move-AzureRMResource` 需要资源名称和资源类型。 可以通过 `Get-AzureRmRecoveryServicesVault` cmdlet 获取这两项信息。

```powershell
$destinationRG = "<destinationResourceGroupName>"
$vault = Get-AzureRmRecoveryServicesVault -Name <vaultname> -ResourceGroupName <vaultRGname>
Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

若要将资源移到不同的订阅，请包含 `-DestinationSubscriptionId` 参数。

```powershell
Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vault.ID
```

执行上述 cmdlet 后，系统会要求你确认是否要移动指定的资源。 键入 **Y** 确认。 验证成功后，资源将会移动。

## <a name="use-cli-to-move-recovery-services-vault"></a>使用 CLI 移动恢复服务保管库

若要将恢复服务保管库移到另一个资源组，请使用以下 cmdlet：

```azurecli
az resource move --destination-group <destinationResourceGroupName> --ids <VaultResourceID>
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

## <a name="post-migration"></a>迁移之后

1. 设置/验证资源组的访问控制。  
2. 完成移动操作后，需要为保管库重新配置备份报告和监视功能。 在移动操作期间，以前的配置将会丢失。

## <a name="move-an-azure-virtual-machine-to-a-different-recovery-service-vault"></a>将 Azure 虚拟机移动到其他恢复服务保管库。 

如果要移动启用了 Azure 备份的 Azure 虚拟机，有两个选择。 具体取决于业务要求：

- [无需保留以前备份的数据](#dont-need-to-preserve-previous-backed-up-data)
- [必须保留以前备份的数据](#must-preserve-previous-backed-up-data)

### <a name="dont-need-to-preserve-previous-backed-up-data"></a>无需保留以前备份的数据

若要在新保管库中保护工作负载，需在旧保管库中删除当前保护和数据，并重新配置备份。

>[!WARNING]
>以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

停止并删除对旧保管库的当前保护：

1. 在保管库属性中禁用软删除。 按照[这些步骤](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal)禁用软删除。

2. 停止保护并删除当前保管库中的备份。 在保管库仪表板菜单中，选择“备份项”。 此处列出的需要移动到新保管库的项必须连同其备份数据一起删除。 请参阅如何[删除云中受保护的项](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud)以及[删除本地受保护的项](backup-azure-delete-vault.md#delete-protected-items-on-premises)。

3. 如果计划迁移 AFS（Azure 文件共享）、SQL 服务器或 SAP HANA 服务器，还需要将其注销。 在保管库仪表板菜单中，选择“备份基础结构”。 请参阅如何[注销 SQL 服务器](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance)、[注销与 Azure 文件共享相关联的存储帐户](manage-afs-backup.md#unregister-a-storage-account)和[注销 SAP HANA 实例](sap-hana-db-manage.md#unregister-an-sap-hana-instance)。

4. 将其从旧保管库中删除后，请继续在新保管库中配置工作负载的备份。

### <a name="must-preserve-previous-backed-up-data"></a>必须保留以前备份的数据

如果需要将当前受保护的数据保留在旧保管库中，并在新保管库中继续保护，则某些工作负载的选项受到限制：

- 对于 MARS，可以[停止保护并保留数据](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup)并在新保管库中注册代理。

  - Azure 备份服务将继续保留旧保管库的所有现有恢复点。
  - 需要付费才能将恢复点保留在旧保管库中。
  - 只能还原旧保管库中尚未过期的恢复点的备份数据。
  - 需要在新保管库中创建数据的新初始副本。

- 对于 Azure VM，可以对旧保管库中的 VM [停止保护并保留数据](backup-azure-manage-vms.md#stop-protecting-a-vm)，将该 VM 移到其他资源组，然后在新保管库中保护该 VM。 请参阅将 VM 移到其他资源组的[指南和限制](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md)。

  同一时间只能在一个保管库中保护 VM。 但是，可以在新保管库中保护新资源组中的 VM，因为它被视为不同的 VM。

  - Azure 备份服务将在旧保管库中保留已备份的恢复点。
  - 你需要付费才能将恢复点保留在旧保管库中（有关详细信息，请参阅 [Azure 备份定价](azure-backup-pricing.md)）。
  - 如果需要，你将能够从旧保管库还原 VM。
  - 新保管库中对新资源中 VM 的第一个备份将是初始副本。

## <a name="next-steps"></a>后续步骤

可以在资源组和订阅之间移动许多不同类型的资源。

有关详细信息，请参阅[将资源移到新资源组或订阅](../azure-resource-manager/management/move-resource-group-and-subscription.md)。