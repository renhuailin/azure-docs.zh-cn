---
title: 还原 Azure Database for PostgreSQL
description: 了解如何还原 Azure Database for PostgreSQL 备份。
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5de8e74fb05eea45e5cb730515b3280c4952951
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359511"
---
# <a name="restore-azure-database-for-postgresql-backups-preview"></a>还原 Azure Database for PostgreSQL 备份（预览）

本文介绍如何将数据库还原到由 Azure 备份进行备份的 Azure PostgreSQL 服务器。

如果服务在目标服务器上具有相应的[权限集](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server)，则可以将数据库还原到同一订阅中的任何 Azure PostgreSQL 服务器。

## <a name="restore-a-database-on-the-target-storage-account"></a>还原目标存储帐户上的数据库


1. 转到“备份保管库” -> “备份实例”。 选择数据库，然后单击“还原”。

   :::image type="content" source="./media/restore-azure-database-postgresql/select-database-for-restore-inline.png" alt-text="屏幕截图，显示选择和还原数据库的过程。" lightbox="./media/restore-azure-database-postgresql/select-database-for-restore-expanded.png":::

   也可以从[备份中心](/azure/backup/backup-center-overview)导航到此页。   
  
1. 在“选择还原点”页上，从可供所选备份实例使用的所有完整备份的列表中选择一个恢复点。 默认选择最新的恢复点。

   :::image type="content" source="./media/restore-azure-database-postgresql/select-restore-point-inline.png" alt-text="屏幕截图，显示选择恢复点的过程。" lightbox="./media/restore-azure-database-postgresql/select-restore-point-expanded.png":::

   如果还原点位于存档层中，则必须在还原之前解除冻结恢复点。 提供解除冻结所需的以下附加参数：

   - “解除冻结优先级”：默认值为“标准”。 
   - “解除冻结持续时间”：最大解除冻结持续时间为 30 天，最小解除冻结持续时间为 10 天。 默认值为 15 天。 恢复点存储在“备份数据存储”中，存储时长为此持续时间。

1. 在“还原参数”页上，选择下列还原类型之一：“还原为数据库”或“还原为文件”。

   - 还原为数据库

     目标服务器可以与源服务器相同。 但是，不支持覆盖原始数据库。 可以跨所有订阅从服务器进行选择，但需要与保管库的位于同一区域。

     1. 在“选择密钥保管库和机密”下拉列表中，选择存储凭据的保管库以连接到目标服务器。

     1. 选择“查看 + 还原”触发验证，以检查服务是否[在目标服务器上具有还原权限](backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)。 必须[手动授予](backup-azure-database-postgresql-overview.md#grant-access-on-the-azure-postgresql-server-and-key-vault-manually)这些权限。

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-database-inline.png" alt-text="屏幕截图，显示选定还原类型为“还原为数据库”。" lightbox="./media/restore-azure-database-postgresql/restore-as-database-expanded.png":::

   - 还原为文件：将备份文件转储到目标存储帐户 (blob)。

     可以跨所有订阅从存储帐户中进行选择，但需要与保管库的位于同一区域。     

     1. 在“选择目标容器”下拉列表中，选择为所选存储帐户筛选的容器之一。
     1. 选择“查看 + 还原”来触发验证，以检查备份服务是有[目标存储帐户上的还原权限](#restore-permissions-on-the-target-storage-account)。

     :::image type="content" source="./media/restore-azure-database-postgresql/restore-as-files-inline.png" alt-text="屏幕截图，显示选定还原类型为“还原为文件”。" lightbox="./media/restore-azure-database-postgresql/restore-as-files-expanded.png":::
   
1. 提交还原操作并在“备份作业”下跟踪触发的作业。
   
   :::image type="content" source="./media/restore-azure-database-postgresql/track-triggered-job-inline.png" alt-text="屏幕截图，显示在“备份作业”下跟踪的已触发作业。" lightbox="./media/restore-azure-database-postgresql/track-triggered-job-expanded.png":::

>[!NOTE]
>对 Azure Database for PostgreSQL 的存档支持以有限的公共预览版形式提供。

## <a name="restore-permissions-on-the-target-storage-account"></a>针对目标存储帐户的还原权限

授予备份保管库 MSI 使用 Azure 门户访问存储帐户容器的权限。

1. 转到“存储帐户” -> “访问控制” -> “添加角色分配”。

1. 在“角色”下拉列表中选择分配到备份保管库 MSI 的“存储 Blob 数据参与者”角色。 

   :::image type="content" source="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-inline.png" alt-text="屏幕截图，显示为备份保管库 MSI 分配使用 Azure 门户访问存储帐户容器的权限的过程。" lightbox="./media/restore-azure-database-postgresql/assign-vault-msi-permission-to-access-storage-account-containers-azure-portal-expanded.png":::

也可通过使用 Azure CLI [az role assignment](/cli/azure/role/assignment) create 命令授予对要还原到的特定容器的精细权限。

```azurecli
az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
```
将代理人参数替换为保管库 MSI 的“应用程序 ID”和引用特定容器的作用域参数。 若要获取保管库 MSI 的“应用程序 ID”，请在“应用程序类型”下选择“所有应用程序”。 搜索保管库名称并复制应用程序 ID。

 :::image type="content" source="./media/restore-azure-database-postgresql/select-application-type-for-id-inline.png" alt-text="屏幕截图，显示获取保管库 MSI 的应用程序 ID 的过程。" lightbox="./media/restore-azure-database-postgresql/select-application-type-for-id-expanded.png":::

 :::image type="content" source="./media/restore-azure-database-postgresql/copy-vault-id-inline.png" alt-text="屏幕截图，显示复制保管库的应用程序 ID 的过程。" lightbox="./media/restore-azure-database-postgresql/copy-vault-id-expanded.png":::
 
## <a name="next-steps"></a>后续步骤

[排查使用 Azure 备份进行 PostgreSQL 数据库备份的问题](backup-azure-database-postgresql-troubleshoot.md)
