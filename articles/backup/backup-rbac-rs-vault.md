---
title: 使用 Azure 基于角色的访问控制来管理备份
description: 使用 Azure 基于角色的访问控制来管理对恢复服务保管库中的备份管理操作的访问。
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: fdde385ca49a61a8fb2c2bba81311035dca3e324
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215176"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用 Azure 基于角色的访问控制来管理 Azure 备份恢复点

Azure 基于角色的访问控制 (Azure RBAC) 可用于对 Azure 进行细致的访问管理。 使用 Azure RBAC，可以在团队中实现职责分离，仅向用户授予他们执行作业所需的访问权限。

> [!IMPORTANT]
> Azure 备份提供的角色仅限于执行可在 Azure 门户中执行的操作，或者通过 REST API 或恢复服务保管库 PowerShell 或 CLI cmdlet 执行的操作。 这些角色对在 Azure 备份代理客户端 UI、System Center Data Protection Manager UI 或 Azure 备份服务器 UI 中执行的操作不具有控制权。

Azure 备份提供三个用于控制备份管理操作的内置角色。 详细了解 [Azure 内置角色](../role-based-access-control/built-in-roles.md)

* [备份参与者](../role-based-access-control/built-in-roles.md#backup-contributor) - 此角色具有创建和管理备份的所有权限，但删除恢复服务保管库和授予其他人访问权限除外。 可以把该角色想象成可执行每个备份管理操作的备份管理的管理员。
* [备份操作员](../role-based-access-control/built-in-roles.md#backup-operator) - 此角色具有除删除备份和管理备份策略之外的针对参与者操作的所有权限。 此角色等效于参与者，但它不能执行破坏性操作，例如通过删除数据或删除本地资源的注册来停止备份。
* [备份读取器](../role-based-access-control/built-in-roles.md#backup-reader) - 此角色具有查看所有备份管理操作的权限。 可以把该角色想象成一位监视者。

若要定义自己的角色以便进一步控制，请参阅如何在 Azure RBAC 中 [生成自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>将备份内置角色映射到备份管理操作

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Azure VM 备份的最低角色要求

下表捕获了备份管理操作以及执行该操作所需的相应的最低 Azure 角色。

| 管理操作 | 所需的最低 Azure 角色 | 所需的范围 | 替代方法 |
| --- | --- | --- | --- |
| 创建恢复服务保管库 | 备份参与者 | 包含保管库的资源组 |   |
| 启用 Azure VM 备份 | 备份操作员 | 包含保管库的资源组 |   |
| | 虚拟机参与者 | VM 资源 |  你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| 按需 VM 备份 | 备份操作员 | 恢复服务保管库 |   |
| 还原 VM | 备份操作员 | 恢复服务保管库 |   |
| | 参与者 | 将在其中部署 VM 的资源组 |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Resources/subscriptions/resourceGroups/write、Microsoft.DomainRegistration/domains/write、Microsoft.Compute/virtualMachines/write、Microsoft.Network/virtualNetworks/read、Microsoft.Network/virtualNetworks/subnets/join/action |
| | 虚拟机参与者 | 已备份的源 VM |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| 还原非托管磁盘 VM 备份 | 备份操作员 | 恢复服务保管库 |
| | 虚拟机参与者 | 已备份的源 VM | 你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| | 存储帐户参与者 | 要还原磁盘的存储帐户资源 |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Storage/storageAccounts/write |
| 从 VM 备份还原托管磁盘 | 备份操作员 | 恢复服务保管库 |
| | 虚拟机参与者 | 已备份的源 VM |    你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| | 存储帐户参与者 | 在还原过程中选择的临时存储帐户，用于在将保管库转换为托管磁盘之前保存保管库中的数据 |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Storage/storageAccounts/write |
| | 参与者 | 托管磁盘将还原到的资源组 | 你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Resources/subscriptions/resourceGroups/write|
| 从 VM 备份还原单个文件 | 备份操作员 | 恢复服务保管库 |
| | 虚拟机参与者 | 已备份的源 VM | 你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| 跨区域还原 | 备份操作员 | 恢复服务保管库的订阅 | 这是除上面提到的还原权限之外的权限。 专用于 CRR，可考虑具有以下权限的自定义角色，而不是内置角色：   "Microsoft.RecoveryServices/locations/backupAadProperties/read" "Microsoft.RecoveryServices/locations/backupCrrJobs/action"         "Microsoft.RecoveryServices/locations/backupCrrJob/action" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action"          "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read" "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read" |
| 创建 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 修改 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 删除 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 停止在 VM 备份上备份（通过保留数据或删除数据） | 备份参与者 | 恢复服务保管库 |
| 注册本地 Windows Server/客户端/SCDPM 或 Azure 备份服务器 | 备份操作员 | 恢复服务保管库 |
| 删除已注册的本地 Windows Server/客户端/SCDPM 或 Azure 备份服务器 | 备份参与者 | 恢复服务保管库 |

> [!IMPORTANT]
> 如果在 VM 资源范围内指定 VM 参与者并选择“备份”作为 VM 设置的一部分，则即使已经备份 VM，也会打开“启用备份”屏幕 。 这是因为验证备份状态的调用仅在订阅级别起作用。 若要避免此问题，请转到保管库并打开 VM 的备份项视图，或者在订阅级别指定“VM 参与者”角色。

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Azure 工作负荷备份（SQL 和 HANA DB 备份）的最低角色要求

下表捕获了备份管理操作以及执行该操作所需的相应的最低 Azure 角色。

| 管理操作 | 所需的最低 Azure 角色 | 所需的范围 | 替代方法 |
| --- | --- | --- | --- |
| 创建恢复服务保管库 | 备份参与者 | 包含保管库的资源组 |   |
| 启用 SQL 数据库和/或 HANA 数据库的备份 | 备份操作员 | 包含保管库的资源组 |   |
| | 虚拟机参与者 | 在其中安装 DB 的 VM 资源 |  你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| 按需 DB 备份 | 备份操作员 | 恢复服务保管库 |   |
| 还原数据库或还原为文件 | 备份操作员 | 恢复服务保管库 |   |
| | 虚拟机参与者 | 已备份的源 VM |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| | 虚拟机参与者 | 将在其中还原 DB 或创建文件的目标 VM |   你也可以考虑具有以下权限的自定义角色而不是内置角色：Microsoft.Compute/virtualMachines/write |
| 创建 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 修改 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 删除 Azure VM 备份的备份策略 | 备份参与者 | 恢复服务保管库 |
| 停止在 VM 备份上备份（通过保留数据或删除数据） | 备份参与者 | 恢复服务保管库 |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure 文件共享备份的最低角色要求

下表列出了备份管理操作以及执行 Azure 文件共享操作所需的相应角色。

| 管理操作 | 所需的角色 | 资源 |
| --- | --- | --- |
| 启用 Azure 文件共享的备份 | 备份参与者 |恢复服务保管库 |
| | 存储帐户备份参与者 | 存储帐户名称 |
| 按需 VM 备份 | 备份操作员 | 恢复服务保管库 |
| 还原文件共享 | 备份操作员 | 恢复服务保管库 |
| | 存储帐户备份参与者 | 存在还原源和目标文件共享的存储帐户资源 |
| 还原单个文件 | 备份操作员 | 恢复服务保管库 |
| |存储帐户参与者|存在还原源和目标文件共享的存储帐户资源 |
| 停止保护 |备份参与者 | 恢复服务保管库 |
| 从保管库中注销存储帐户 |备份参与者 | 恢复服务保管库 |
| |存储帐户参与者 | 存储帐户名称|

## <a name="next-steps"></a>后续步骤

* [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)：Azure 门户中的 Azure RBAC 入门。
* 了解如何通过以下方式管理访问权限：
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Azure 基于角色的访问控制故障排除](../role-based-access-control/troubleshooting.md)：获取有关修复常见问题的建议。
