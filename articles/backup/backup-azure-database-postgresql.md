---
title: 备份 Azure Database for PostgreSQL
description: 了解具有长期保留功能的 Azure Database for PostgreSQL 备份（预览版）
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 4f8e44bbaba87581b3c988602a436ed18b1a1a20
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110061761"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>具有长期保留功能的 Azure Database for PostgreSQL 备份（预览版）

Azure 备份和 Azure 数据库服务共同致力于为 Azure Database for PostgreSQL 服务器生成可将备份保留长达 10 年的企业级备份解决方案。

除长期保留功能外，该解决方案还具有以下列出的许多其他功能：

- 使用 Azure Active Directory 和托管服务标识 (MSI) 身份验证实现的针对数据库的 Azure 基于角色的访问控制 (Azure RBAC)。
- 单个数据库级别的客户控制的计划备份和按需备份。
- 到任何 Postgres 服务器的数据库级别还原或直接还原到 Blob 存储。
- 长期保留。
- 集中监视所有操作和作业。
- 备份存储在单独的安全域和容错域中。 因此，即使源服务器遭到入侵甚至被终止，备份也将在[备份保管库](backup-vault-overview.md)中保持安全。
- 使用 pg_dump 可以更灵活地进行还原，以便可以跨数据库版本进行还原，甚至仅还原备份的一部分。

你可以单独使用此解决方案，也可以与 Azure PostgreSQL 提供的本机备份解决方案（最多提供 35 天的保留期）配合使用。 本机解决方案适用于操作恢复，例如想要从最新备份中恢复时。 Azure 备份解决方案可帮助满足合规性需求以及提供更精细、更灵活的备份和还原。

## <a name="support-matrix"></a>支持矩阵

|支持  |详细信息  |
|---------|---------|
|支持的部署   |  [Azure Database for PostgreSQL - 单一服务器](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|支持的 Azure 区域 |  美国东部、美国东部 2、美国中部、美国中南部、美国西部、美国西部 2、美国中西部、巴西南部、加拿大中部、北欧、西欧、英国南部、英国西部、德国中西部、瑞士北部、瑞士西部、东亚、东南亚、日本东部、日本西部、韩国中部、韩国南部、印度中部、澳大利亚东部、澳大利亚中部、澳大利亚中部 2、阿联酋北部  |
|支持的 Azure PostgreSQL 版本    |   9.5、9.6、10、11      |

## <a name="feature-considerations-and-limitations"></a>功能注意事项和限制

- 仅 Azure 门户支持所有操作。
- 建议的最大数据库大小限制为 400 GB。
- 不支持跨区域备份。 这意味着无法将 Azure PostgreSQL 服务器备份到另一个区域中的保管库。 同样，只能将备份还原到与保管库位于同一区域内的服务器。
- 还原时仅恢复数据， 不还原“角色”。
- 在预览版中，我们建议仅在测试环境中运行解决方案。

## <a name="backup-process"></a>备份过程

1. 此解决方案使用 pg_dump 来备份 Azure PostgreSQL 数据库。

2. 指定要备份的 Azure PostgreSQL 数据库后，该服务就会验证它是否具有在服务器和数据库上执行备份操作的正确权限集和访问权限。

3. Azure 备份会启动其中安装了备份扩展的辅助角色。 此扩展与 Postgres 服务器通信。

4. 此扩展包含协调器和 Azure Postgres 插件。 协调器负责触发各种操作（如配置备份、备份和还原）的工作流，插件负责实际的数据流。
  
5. 在所选数据库上触发配置保护后，备份服务将使用备份计划和其他策略详细信息设置协调器。

6. 在计划的时间，协调器与插件通信，并开始使用 pg_dump 从 Postgres 服务器流式传输备份数据。

7. 插件将数据直接发送到备份保管库，因此不需要暂存位置。 数据使用 Microsoft 托管密钥加密，并由 Azure 备份服务存储在存储帐户中。

8. 数据传输完成后，协调器将与备份服务确认提交。

    ![备份过程](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>在 Azure PostgreSQL 数据库上配置备份

可以跨多个 Azure PostgreSQL 服务器在多个数据库上配置备份。 确保已配置服务备份 Postgres 服务器所需的[必备权限](#prerequisite-permissions-for-configure-backup-and-restore)。

以下说明分步介绍如何使用 Azure 备份在 Azure PostgreSQL 数据库上配置备份：

1. 可通过两种方法开始该流程：

    1. 转到“[备份中心](backup-center-overview.md)” -> “概述” -> “备份”。

        ![转到“备份中心”](./media/backup-azure-database-postgresql/backup-center.png)

        在“启动: 配置备份”下，为“数据源类型”选择“Azure Database for PostgreSQL”。

        ![在“启动: 配置备份”中，选择“数据源类型”](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. 或者，可以直接转到“[备份保管库](backup-vault-overview.md)” -> “备份”。

        ![转到“备份保管库”](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![在“备份保管库”中选择“备份”](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. 在“配置备份”下，选择要将 Postgres 数据库备份到的“备份保管库”。 如果已经在保管库上下文中，此信息会预先填充。

    ![在“配置备份”中选择“备份保管库”](./media/backup-azure-database-postgresql/configure-backup.png)

1. 选择或创建“备份策略”。

    ![选择“备份策略”](./media/backup-azure-database-postgresql/backup-policy.png)

1. 选择要备份的资源或 Postgres 数据库。

    ![选择要备份的资源](./media/backup-azure-database-postgresql/select-resources.png)

1. 如果所有 Azure PostgreSQL 服务器都与保管库位于同一区域，则可以跨订阅从列表中进行选择。 展开箭头以查看服务器中数据库的列表。

    ![选择服务器](./media/backup-azure-database-postgresql/choose-servers.png)

1. 服务对所选数据库运行这些检查，以验证保管库是否具有备份选定 Postgres 服务器和数据库的权限。
    1. 所有数据库的“备份就绪情况”应读取为“成功”才能继续操作。
    1. 如果出现错误，请修复错误并“重新验证”，或从选择中删除数据库。

    ![要修复的验证错误](./media/backup-azure-database-postgresql/validation-errors.png)

1. 确认“查看和配置”下的所有详细信息，然后选择“配置备份”以提交操作。

    ![确认“查看和配置”中的详细信息](./media/backup-azure-database-postgresql/review-and-configure.png)

1. 触发后，“配置备份”操作将创建备份实例。 可以在“备份中心”或保管库视图中的“[备份实例](backup-center-monitor-operate.md#backup-instances)”窗格下跟踪操作的状态。

    ![备份实例](./media/backup-azure-database-postgresql/backup-instances.png)

1. 备份根据策略中定义的备份计划触发。 可以在“[备份作业](backup-center-monitor-operate.md#backup-jobs)”下跟踪作业。 目前，可以查看过去 7 天的作业。

    ![备份作业](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>创建备份策略

1. 转到“备份中心” -> “备份策略” -> “添加”。 或者，可以转到“备份保管库” -> “备份策略” -> “添加”。

    ![添加备份策略](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. 输入新策略的“名称”。

    ![输入策略名称](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. 定义备份计划。 我们目前支持“每周”备份。 可以在一周中的一天或多天计划备份。

    ![定义备份计划](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. 定义保留设置。 可以添加一个或多个保留规则。 每个保留规则均假定特定备份的输入，以及这些备份的数据存储和保留期限。

1. 可以选择将备份存储到两个数据存储（或分层）之一中：备份数据存储（标准层）或存档数据存储（预览版）。

   可以选择“过期时”以在备份数据存储中的备份过期时将其移动到存档数据存储。

1. 默认保留规则在没有其他保留规则的情况下适用，其默认值为 3 个月。

    - 备份数据存储中的保留期限从 7 天到 10 年不等。
    - 存档数据存储中的保留期限从 6 个月到 10 年不等。

    ![编辑保留期限](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>保留规则按预先确定的优先级顺序进行评估。 每年规则的优先级最高，其次是每月规则，然后是每周规则。 没有其他规则符合条件时，将应用默认保留设置。 例如，同一恢复点可能会是每周进行的首次成功备份以及每月进行的首次成功备份。 但是，由于每月规则的优先级高于每周规则的优先级，因此适用与每月进行的首次成功备份相对应的保留期。

## <a name="restore"></a>还原

如果服务在目标服务器上具有相应的权限集，则可以将数据库还原到同一订阅中的任何 Azure PostgreSQL 服务器。 确保已配置服务备份 Postgres 服务器所需的[必备权限](#prerequisite-permissions-for-configure-backup-and-restore)。

遵循以下分步指南来触发还原：

1. 可通过两种方法启动还原流程：
    1. 转到“[备份中心](backup-center-overview.md)” -> “概述” -> “还原”。

    ![在“备份中心”中选择“还原”](./media/backup-azure-database-postgresql/backup-center-restore.png)

    在“启动: 还原”下，为“数据源类型”选择“Azure Database for PostgreSQL”。 选择“备份实例”。

    ![在“启动: 还原”中选择“数据源类型”](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. 或者，可以直接转到“备份保管库” -> “备份实例”。 选择与要还原的数据库相对应的“备份实例”。

    ![用于还原的备份实例](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![备份实例列表](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![选择“还原”](./media/backup-azure-database-postgresql/select-restore.png)

1. 从所选备份实例可用的所有完整备份的列表中“选择恢复点”。 默认选择最新的恢复点。

    ![选择恢复点](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![恢复点列表](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. 输入“还原参数”。 此时，可以从两种还原中进行选择：“还原为数据库”和“还原为文件”。

1. **还原为数据库**：还原备份数据，以在目标 PostgreSQL 服务器中新建数据库。

    - 目标服务器可以与源服务器相同。 但是，不支持覆盖原始数据库。
    - 可以跨所有订阅从服务器进行选择，但需要与保管库位于同一区域。
    - 选择“查看 + 还原”。 这将触发验证，以检查服务在目标服务器上是否具有适当的还原权限。

    ![还原为数据库](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **还原为文件**：将备份文件转储到目标存储帐户 (blob)。

    - 可以跨所有订阅从存储帐户中进行选择，但需要与保管库位于同一区域。
    - 从针对所选存储帐户进行筛选的容器列表中选择目标容器。
    - 选择“查看 + 还原”。 这将触发验证，以检查服务在目标服务器上是否具有适当的还原权限。

    ![还原为文件](./media/backup-azure-database-postgresql/restore-as-files.png)

1. 如果恢复点位于存档层中，则必须在还原之前解除冻结恢复点。
   
   ![解除冻结设置](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   提供解除冻结所需的以下附加参数：
   - “解除冻结优先级”：默认值为"标准"。 
   - “解除冻结持续时间”：最大解除冻结持续时间为 30 天，最小解除冻结持续时间为 10 天。 默认值为 **15**。
   
   恢复点存储在“备份数据存储”中，持续时间为指定的解除冻结持续时间。


1. 核对信息，然后选择“还原”。 这将触发相应的还原作业，该作业可以在“备份作业”下进行跟踪。

>[!NOTE]
>对 Azure Database for PostgreSQL 的存档支持以有限的公共预览版形式提供。

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>配置备份和还原的必备权限

Azure 备份遵循严格的安全准则。 即使它是本机 Azure 服务，也不会假定对资源具有权限，而是需要由用户显式授予。  同样，不会存储用于连接到数据库的凭据。 这对于保护数据很重要。 相反，我们使用 Azure Active Directory 身份验证。

[下载此文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)获取自动化脚本和相关说明。 它将向 Azure PostgreSQL 服务器授予一组适当的权限，以便进行备份和还原。

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>管理备份的 Azure PostgreSQL 数据库

以下是可以对备份实例执行的管理操作：

### <a name="on-demand-backup"></a>按需备份

若要触发不在策略指定的计划中的备份，请转到“备份实例” -> “立即备份”。
从关联的备份策略中定义的保留规则列表中进行选择。

![立即触发备份](./media/backup-azure-database-postgresql/backup-now.png)

![从保留规则列表中选择](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>停止保护

你可以停止对某个备份项的保护。 这还会删除该备份项的关联恢复点。 如果恢复点至少六个月不在存档层中，则删除这些恢复点会产生早期删除费用。 我们尚未提供在保留现有恢复点的同时停止保护的选项。

![停止保护](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>更改策略

你可以通过备份实例更改关联的策略。

1. 选择“备份实例” -> “更改策略”。

    ![更改策略](./media/backup-azure-database-postgresql/change-policy.png)

1. 选择要应用于数据库的新策略。

    ![重新分配策略](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>疑难解答

本部分提供有关使用 Azure 备份来备份 Azure PostgreSQL 数据库的疑难解答信息。

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

在要备份或还原的 PG 服务器上授予备份保管库 MSI“读取”访问权限。

为了建立与 PostgreSQL 数据库的安全连接，Azure 备份使用[托管服务标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 身份验证模型。 这意味着备份保管库只能访问由用户显式授予权限的资源。

系统 MSI 在创建时自动分配到保管库。 需要授予此保管库 MSI 访问你打算从中备份数据库的 PostgreSQL 服务器的权限。

步骤：

1. 在 Postgres 服务器中，转到“访问控制(IAM)”窗格。

    ![“访问控制”窗格](./media/backup-azure-database-postgresql/access-control-pane.png)

1. 选择“添加角色分配”。

    ![添加角色分配](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. 在右侧打开的上下文窗格中，输入以下信息：<br>

   - “角色”：在下拉列表中选择“读者”角色。 <br>
   - “分配访问权限”：在下拉列表中选择“用户、组或服务主体”选项。 <br>
   - **选择**：输入要将此服务器及其数据库备份到的备份保管库的名称。<br>

    ![选择角色](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

创建可以通过 Azure Active Directory 进行身份验证的数据库备份用户：

此错误可能是缺少 PostgreSQL 服务器的 Azure Active Directory 管理员，或者缺少可以使用 Azure Active Directory 进行身份验证的备份用户造成的。

步骤：

将 Active Directory 管理员添加到 OSS 服务器：

通过可以使用 Azure Active Directory（而非密码）进行身份验证的用户连接到数据库需要执行此步骤。 Azure Database for PostgreSQL 中的 Azure AD 管理员用户将拥有 azure_ad_admin 角色。 仅 azure_ad_admin 角色可以创建可通过 Azure AD 进行身份验证的新数据库用户。

1. 转到服务器视图左侧导航窗格中的“Active Directory 管理员”选项卡，然后将自己（或其他用户）添加为 Active Directory 管理员。

    ![设置 Active Directory 管理员](./media/backup-azure-database-postgresql/set-admin.png)

1. 确保“保存”AD 管理员用户设置。

    ![保存 Active Directory 管理员用户设置](./media/backup-azure-database-postgresql/save-admin-setting.png)

有关完成权限授予步骤所需执行的步骤的列表，请参阅[此文档](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx)。

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

通过在服务器视图中启用“允许访问 Azure 服务”标志来建立网络直通连接。 在服务器视图的“连接安全”窗格下，将“允许访问 Azure 服务”标志设置为“是”。

![允许访问 Azure 服务](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>还原为文件时的还原到存储帐户容器的权限

1. 授予备份保管库 MSI 使用 Azure 门户访问存储帐户容器的权限。
    1. 转到“存储帐户” -> “访问控制” -> “添加角色分配”。
    1. 将“存储 Blob 数据参与者”角色分配到备份保管库 MSI。

    ![分配存储 Blob 数据参与者角色](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. 或者，通过使用 Azure CLI [az role assignment create](/cli/azure/role/assignment) 命令授予对要还原到的特定容器的精细权限。

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. 将代理人参数替换为保管库 MSI 的“应用程序 ID”和引用特定容器的作用域参数。
    1. 若要获取保管库 MSI 的“应用程序 ID”，请在“应用程序类型”下选择“所有应用程序”：

        ![选择“所有应用程序”](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. 搜索保管库名称并复制应用程序 ID：

        ![搜索保管库名称](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>后续步骤

[备份保管库概述](backup-vault-overview.md)
