---
title: 排查 Azure Database for PostgreSQL 备份问题
description: 有关备份 Azure Database for PostgreSQL 的故障排除信息。
ms.topic: troubleshooting
ms.date: 09/22/2021
ms.openlocfilehash: 0dcb5b3a85fd1364d90327648274194d3bc30364
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698981"
---
# <a name="troubleshoot-postgresql-database-backup-by-using-azure-backup-preview"></a>排查使用 Azure 备份（预览版）进行 PostgreSQL 数据库备份的问题

本文提供有关使用 Azure 备份来备份 Azure PostgreSQL 数据库的故障排除信息。

## <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

在要备份或还原的 PG 服务器上授予备份保管库 MSI“读取”访问权限。

为了建立与 PostgreSQL 数据库的安全连接，Azure 备份使用[托管服务标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 身份验证模型。 这意味着备份保管库只能访问由用户显式授予权限的资源。

系统 MSI 在创建时自动分配到保管库。 需要授予此保管库 MSI 访问你打算从中备份数据库的 PostgreSQL 服务器的权限。

步骤：

1. 在 Postgres 服务器中，转到“访问控制(IAM)”窗格。

    ![“访问控制”窗格](./media/backup-azure-database-postgresql/access-control-pane.png)

1. 选择“添加角色分配”。

    ![“添加角色分配”的屏幕截图。](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 在右侧打开的上下文窗格中，输入以下信息：<br>

   - “角色”：在下拉列表中选择“读者”角色。 <br>
   - “分配访问权限”：在下拉列表中选择“用户、组或服务主体”选项。 <br>
   - **选择**：输入要将此服务器及其数据库备份到的备份保管库的名称。<br>

    ![显示如何选择角色的屏幕截图。](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

## <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

创建可以通过 Azure Active Directory 进行身份验证的数据库备份用户：

此错误可能是缺少 PostgreSQL 服务器的 Azure Active Directory 管理员，或者缺少可以使用 Azure Active Directory 进行身份验证的备份用户造成的。

步骤：

将 Active Directory 管理员添加到 OSS 服务器：

通过可以使用 Azure Active Directory（而非密码）进行身份验证的用户连接到数据库需要执行此步骤。 Azure Database for PostgreSQL 中的 Azure AD 管理员用户将拥有 azure_ad_admin 角色。 仅 azure_ad_admin 角色可以创建可通过 Azure AD 进行身份验证的新数据库用户。

1. 转到服务器视图左侧导航窗格中的“Active Directory 管理员”选项卡，然后将自己（或其他用户）添加为 Active Directory 管理员。

    ![显示如何设置 Active Directory 管理员的屏幕截图。](./media/backup-azure-database-postgresql/set-admin.png)

1. 确保“保存”AD 管理员用户设置。

    ![显示如何保存 Active Directory 管理员用户设置的屏幕截图。](./media/backup-azure-database-postgresql/save-admin-setting.png)

有关完成权限授予步骤所需执行的步骤的列表，请参阅[此文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

通过在服务器视图中启用“允许访问 Azure 服务”标志来建立网络直通连接。 在服务器视图的“连接安全”窗格下，将“允许访问 Azure 服务”标志设置为“是”。

>[!Note]
>启用此标志之前，请确保将“拒绝公用网络访问”标志设置为“否”。 

![显示如何允许访问 Azure 服务的屏幕截图。](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

## <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>还原为文件时的还原到存储帐户容器的权限

1. 授予备份保管库 MSI 使用 Azure 门户访问存储帐户容器的权限。
    1. 转到“存储帐户” -> “访问控制” -> “添加角色分配”。
    1. 将“存储 Blob 数据参与者”角色分配到备份保管库 MSI。

    ![显示分配“存储 Blob 数据参与者”角色的过程的屏幕截图。](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 或者，通过使用 Azure CLI [az role assignment create](/cli/azure/role/assignment) 命令授予对要还原到的特定容器的精细权限。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 将代理人参数替换为保管库 MSI 的“应用程序 ID”和引用特定容器的作用域参数。
    1. 若要获取保管库 MSI 的“应用程序 ID”，请在“应用程序类型”下选择“所有应用程序”：

        ![显示如何选择“所有应用程序”的屏幕截图。](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 搜索保管库名称并复制应用程序 ID：

        ![显示如何搜索保管库名称的屏幕截图。](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>后续步骤

[关于 Azure Database for PostgreSQL 备份（预览版）](backup-azure-database-postgresql-overview.md)
