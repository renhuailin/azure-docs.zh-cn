---
title: 备份保管库概述
description: 备份保管库概述。
ms.topic: conceptual
ms.date: 09/08/2021
ms.custom: references_regions
ms.openlocfilehash: 8d617dd2d2b1fa61a797d14c709ce285dc28b9bf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799402"
---
# <a name="backup-vaults-overview"></a>备份保管库概述

本文介绍备份保管库的功能。 备份保管库是 Azure 中的一个存储实体，用于保存 Azure 备份支持的某些更新的工作负荷的备份数据。 你可以使用备份保管库来保存各种 Azure 服务（例如 Azure Database for PostgreSQL 服务器和 Azure 备份将支持的更新的工作负荷）的备份数据。 备份保管库便于组织备份数据，并最大限度降低管理开销。 备份保管库基于 Azure 的 Azure 资源管理器模型，该模型提供如下功能：

- **有助于确保备份数据安全的增强功能**：使用备份保管库时，Azure 备份提供用于保护云备份的安全功能。 这些安全功能确保可以保护备份并安全地恢复数据，即使生产服务器和备份服务器受到危害。 [了解详细信息](backup-azure-security-feature.md)

- **Azure 基于角色的访问控制 (Azure RBAC)** ：Azure RBAC 在 Azure 中提供精细的访问管理控制。 [Azure 提供各种内置角色](../role-based-access-control/built-in-roles.md)，而 Azure 备份具有三个[用于管理恢复点的内置角色](backup-rbac-rs-vault.md)。 备份保管库与 Azure RBAC 兼容，后者会限制对已定义用户角色集的备份和还原访问权限。 [了解详细信息](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>备份保管库中的存储设置

备份保管库是一个实体，用于存储随着时间推移而创建的备份和恢复点。 备份保管库还包含与受保护虚拟机关联的备份策略。

- Azure 备份会自动处理保管库的存储。 创建备份保管库时，请选择符合业务需求的存储冗余。

- 若要详细了解存储冗余，请参阅有关[异地](../storage/common/storage-redundancy.md#geo-redundant-storage)冗余和[本地](../storage/common/storage-redundancy.md#locally-redundant-storage)冗余的这些文章。

## <a name="encryption-settings-in-the-backup-vault"></a>备份保管库中的加密设置

本部分介绍可用于加密备份保管库中存储的备份数据的选项。 Azure 备份服务使用“备份管理服务”应用来访问 Azure Key Vault，而不是使用备份保管库的托管标识。


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平台托管的密钥加密备份数据

默认情况下，所有数据将使用平台托管的密钥进行加密。 无需从你的终端执行任何明确操作即可实现此加密。 它适用于正在备份到备份保管库的所有工作负荷。

## <a name="create-a-backup-vault"></a>创建备份保管库

备份保管库是一个管理实体，它会存储一段时间内创建的恢复点，并提供用于执行备份相关操作的接口。 其中包括按需备份、执行还原和创建备份策略。

若要创建备份保管库，请执行以下步骤：

### <a name="sign-in-to-azure"></a>登录 Azure

通过 <https://portal.azure.com> 登录到 Azure 门户。

### <a name="create-backup-vault"></a>创建备份保管库

1. 在搜索框中键入“备份保管库”。
2. 在“服务”下，选择“备份保管库”。
3. 在“备份保管库”页上，选择“添加”。
4. 在“基本信息”选项卡上的“项目详细信息”下，确保选择了正确的订阅，然后选择“新建”资源组  。 对于名称，请键入 *myResourceGroup*。

    ![新建资源组](./media/backup-vault-overview/new-resource-group.png)

5. 在“实例详细信息”下，键入“myVault”作为“备份保管库名称”，然后选择所需的区域，在此示例中，你的“区域”为“美国东部”。
6. 现在，选择你的“存储冗余”。 保护保管库的项之后，无法更改存储冗余。
7. 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。
8. 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。 详细了解[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)和[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)。

    ![选择存储冗余](./media/backup-vault-overview/storage-redundancy.png)

9. 在页面底部选择“查看 + 创建”按钮。

    ![选择“查看 + 创建”](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>删除备份保管库

本部分介绍如何删除备份保管库。 其中分别说明了如何删除依赖项，以及如何删除保管库。

### <a name="before-you-start"></a>开始之前

不能删除具有以下任何依赖项的备份保管库：

- 不能删除包含受保护数据源（例如 Azure Database for PostgreSQL 服务器）的保管库。
- 不能删除包含备份数据的保管库。

如果在不删除依赖项的情况下尝试删除保管库，将会遇到以下错误消息：

>无法删除此备份保管库，因为此保管库中现有备份实例或备份策略。 请先删除此保管库中现有的全部备份实例和备份策略，再尝试删除此保管库。

请确保循环访问备份中心的“数据源类型”筛选器选项，不要错过任何需要删除的现有备份实例或策略，然后才能删除备份保管库。 

![数据源类型](./media/backup-vault-overview/datasource-types.png)

### <a name="proper-way-to-delete-a-vault"></a>删除保管库的正确方式

>[!WARNING]
以下操作是破坏性的，无法撤消。 与受保护服务器关联的所有备份数据和备份项将被永久删除。 请谨慎操作。

若要正确删除保管库，必须按以下顺序执行步骤：

- 验证是否有任何受保护的项：
  - 在左侧导航栏中，转到“备份实例”。 必须先删除此处列出的所有项。

完成这些步骤后，你可以继续删除保管库。

### <a name="delete-the-backup-vault"></a>删除备份保管库

当保管库中没有其他任何项时，在保管库仪表板上选择“删除”。 你将看到一条确认文本，询问你是否要删除该保管库。

![删除保管库](./media/backup-vault-overview/delete-vault.png)

1. 选择“是”确认要删除该保管库。 随即会删除该保管库。 门户返回到“新建”服务菜单。

## <a name="monitor-and-manage-the-backup-vault"></a>监视和管理备份保管库

本部分介绍如何使用备份保管库“概述”仪表板来监视和管理备份保管库。 概述窗格包含两个磁贴：“作业”和“实例”。

![“概述”仪表板](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>管理备份实例

在“作业”磁贴中，可以获取备份保管库中所有备份和还原相关作业的汇总视图。 选择此磁贴中的任何数字即可查看作业详细信息，了解特定数据源类型、操作类型和状态。

![备份实例](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>管理备份作业

在“备份实例”磁贴中，可获得备份保管库中所有备份实例的汇总视图。 选择此磁贴中的任何数字即可查看备份详细信息，了解特定数据源类型和保护状态。

![备份作业](./media/backup-vault-overview/backup-jobs.png)

## <a name="move-a-backup-vault-across-azure-subscriptionsresource-groups-public-preview"></a>跨 Azure 订阅/资源组之间移动备份保管库（公共预览版）

本节说明如何使用 Azure 门户在 Azure 订阅和资源组之间移动备份保管库（为 Azure 备份配置）。

>[!Note]
>您还可以使用 [PowerShell](/powershell/module/az.resources/move-azresource?view=azps-6.3.0&preserve-view=true) 和 [CLI](/cli/azure/resource?view=azure-cli-latest&preserve-view=true#az_resource_move) 将备份保管库移动到不同的资源组或订阅。

### <a name="supported-regions"></a>支持的区域

以下区域当前支持跨订阅和资源组移动保管库：美国西部、美国中南部、东亚、瑞士北部、南非北部、英国西部、美国中北部、阿拉伯联合酋长国北部、挪威东部、澳大利亚东南部、日本西部、加拿大东部、韩国中部、澳大利亚中部、美国中西部、美国中部、印度西部、印度南部、阿拉伯联合酋长国中部、南非西部、挪威西部、瑞士西部

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-resource-group"></a>使用 Azure 门户将备份保管库移到不同的资源组

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开备份保管库列表并选择要移动的保管库。

   保管库仪表板显示保管库详细信息。

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-inline.png" alt-text="该屏幕截图显示了要移动到另一个资源组的保管库的仪表板。" lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-resource-group-expanded.png"::: 

1. 在保管库的“概述”菜单中，单击“移动”，然后选择“移动到另一个资源组”。  

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-resource-group-inline.png" alt-text="该屏幕截图显示用于将备份保管库移动到另一个资源组的选项。" lightbox="./media/backup-vault-overview/select-move-to-another-resource-group-expanded.png":::
   >[!Note]
   >只有管理员订阅才具有移动保管库所需的权限。

1. 在“资源组”下拉列表中，选择现有资源组或选择“新建”以创建新资源组。 

   订阅将保持不变，并自动填充。

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-inline.png" alt-text="该屏幕截图显示选择现有资源组或创建新资源组。" lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-expanded.png":::

1. 在“要移动的资源”选项卡上，需要移动的备份保管库将进行验证。 此过程可能需要几分钟时间。 等到验证完成。

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-inline.png" alt-text="该屏幕截图显示备份保管库验证状态。" lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-resource-group-expanded.png"::: 

1. 选中“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”复选框进行确认，然后选择“移动”。
 
   >[!Note]
   >在跨资源组或订阅移动保管库后，资源路径将会改变。 在移动操作完成后，请务必用新的资源路径更新工具和脚本。

等待移动操作完成后再对保管库执行任何其他操作。 如果在移动过程中执行，则对备份保管库中执行任何操作都将失败。 此过程完成后，备份保管库应出现在目标资源组中。

>[!Important]
>如果您在移动保管库时遇到任何错误，请参阅[错误代码和故障排除部分](#error-codes-and-troubleshooting)。  

### <a name="use-azure-portal-to-move-backup-vault-to-a-different-subscription"></a>使用 Azure 门户将备份保管库移到不同的订阅

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 打开备份保管库列表并选择要移动的保管库。
   
   保管库仪表板显示保管库详细信息。

   :::image type="content" source="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-inline.png" alt-text="该屏幕截图显示了要移动到另一个 Azure 订阅的保管库的仪表板。" lightbox="./media/backup-vault-overview/vault-dashboard-to-move-to-another-subscription-expanded.png"::: 

1. 在保管库的“概述”菜单中，单击“移动”，然后选择“移动到另一个订阅”。  

   :::image type="content" source="./media/backup-vault-overview/select-move-to-another-subscription-inline.png" alt-text="该屏幕截图显示用于将备份保管库移动到另一个 Azure 订阅的选项。" lightbox="./media/backup-vault-overview/select-move-to-another-subscription-expanded.png"::: 
   >[!Note]
   >只有管理员订阅才具有移动保管库所需的权限。

1. 在“订阅”下拉列表中，选择现有订阅。

   要在订阅之间移动保管库，目标订阅必须与源订阅位于同一租户中。 若要将保管库移动到不同的租户，请参阅[将订阅转移到不同的目录](/azure/role-based-access-control/transfer-subscription)。

1. 在“资源组”下拉列表中，选择现有资源组或选择“新建”以创建新资源组。 

   :::image type="content" source="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-inline.png" alt-text="该屏幕截图显示选择现有资源组或在另一个 Azure 订阅中创建新资源组。" lightbox="./media/backup-vault-overview/select-existing-or-create-resource-group-to-move-to-other-subscription-expanded.png":::

1. 在“要移动的资源”选项卡上，需要移动的备份保管库将进行验证。 此过程可能需要几分钟时间。 等到验证完成。

   :::image type="content" source="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-inline.png" alt-text="该屏幕截图显示了要移动到另一个 Azure 订阅的备份保管库的验证状态。" lightbox="./media/backup-vault-overview/move-validation-process-to-move-to-another-subscription-expanded.png"::: 

1. 选中“我了解与已移动资源关联的工具和脚本在更新为使用新资源 ID 之前将不可用”复选框进行确认，然后选择“移动”。
 
   >[!Note]
   >在跨资源组或订阅移动保管库后，资源路径将会改变。 在移动操作完成后，请务必用新的资源路径更新工具和脚本。

等待移动操作完成后再对保管库执行任何其他操作。 如果在移动过程中执行，则对备份保管库中执行任何操作都将失败。 此过程完成后，备份保管库应出现在目标订阅和资源组中。

>[!Important]
>如果您在移动保管库时遇到任何错误，请参阅[错误代码和故障排除部分](#error-codes-and-troubleshooting)。

### <a name="error-codes-and-troubleshooting"></a>错误代码和故障排除

排查在备份保管库移动过程中可能会遇到的以下常见问题：

#### <a name="backupvaultmoveresourcespartiallysucceeded"></a>BackupVaultMoveResourcesPartiallySucceeded   

原因：如果备份保管库移动仅部分成功，你可能会遇到此错误。

建议：此问题应在 36 小时内自动解决。 如果问题持续出现，请与 Microsoft 支持部门联系。

#### <a name="backupvaultmoveresourcescriticalfailure"></a>BackupVaultMoveResourcesCriticalFailure 

原因：备份保管库移动严重失败时可能会遇到此错误。 

建议：此问题应在 36 小时内自动解决。 如果问题持续出现，请与 Microsoft 支持部门联系。 

#### <a name="usererrorbackupvaultresourcemoveinprogress"></a>UserErrorBackupVaultResourceMoveInProgress 

原因：如果在移动备份保管库时尝试对备份保管库执行任何操作，则可能会遇到此错误。 

建议：等待移动操作完成，然后重试。 
#### <a name="usererrorbackupvaultresourcemovenotallowedformultipleresources"></a>UserErrorBackupVaultResourceMoveNotAllowedForMultipleResources

原因：如果尝试一次移动多个备份保管库，则可能会遇到此错误。 

建议：确保对于每个移动操作指选择一个备份保管库。 
#### <a name="usererrorbackupvaultresourcemovenotalloweduntilresourceprovisioned"></a>UserErrorBackupVaultResourceMoveNotAllowedUntilResourceProvisioned

原因：如果尚未预配保管库，则可能会遇到此错误。 

建议：稍后重试操作。

#### <a name="backupvaultresourcemoveisnotenabled"></a>BackupVaultResourceMoveIsNotEnabled 

原因：当前所选 Azure 区域中不支持对备份保管库移动资源。

建议：确保已选择一个支持的区域来移动备份保管库。 请参阅[支持的区域](#supported-regions)。

## <a name="next-steps"></a>后续步骤

- [配置 Azure PostgreSQL 数据库备份](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
