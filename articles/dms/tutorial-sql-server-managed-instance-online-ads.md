---
title: 教程：使用 Azure Data Studio 将 SQL Server 联机迁移到 Azure SQL 托管实例
titleSuffix: Azure Database Migration Service(DMS)
description: 将 Azure Data Studio 和 Azure 数据库迁移服务配合使用，以将 SQL Server 联机迁移到 Azure SQL 托管实例
services: dms
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cawrites
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 10/05/2021
ms.openlocfilehash: 96c552cd078bc18b11d7991482c280d92b4589d2
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544637"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-azure-data-studio-with-dms-preview"></a>教程：将 Azure Data Studio 和 DMS（预览版）配合使用，以将 SQL Server 联机迁移到 Azure SQL 托管实例

使用 Azure Data Studio 中的 Azure SQL 迁移扩展将数据库从 SQL Server 实例迁移到 [Azure SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)，此过程只会造成极短的停机时间。 有关可能需要一些手动操作的方法，请参阅[将 SQL Server 实例迁移到 Azure SQL 托管实例](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)一文。

在本教程中，你将 Azure Data Studio 和 Azure 数据库迁移服务 (DMS) 配合使用，以将 Adventureworks 数据库从 SQL Server 的本地实例迁移到 Azure SQL 托管实例，此过程只会造成极短的停机时间。

在本教程中，你将了解：
> [!div class="checklist"]
>
> * 在 Azure Data Studio 中启动“迁移到 Azure SQL”向导。
> * 对源 SQL Server 数据库运行评估
> * 指定源 SQL Server、备份位置和目标 Azure SQL 托管实例的详细信息
> * 创建新的 Azure 数据库迁移服务并安装自承载集成运行时以访问源服务器和备份。
> * 开始迁移并监视迁移的进度。
> * 准备就绪后执行迁移交接。

> [!IMPORTANT]
> 为迁移做好准备并尽量缩短联机迁移过程的持续时间，以便将实例重新配置或计划内维护造成的中断风险降到最低。 如果发生此类事件，迁移过程将从头开始。 计划内维护有 36 小时的宽限期，在此期限内，在重新开始迁移过程之前，将会保留目标 Azure SQL 托管实例配置或维护内容。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文介绍如何将数据库从 SQL Server 联机迁移到 Azure SQL 托管实例。 若要进行脱机数据库迁移，请参阅[将 Azure Data Studio 和 DMS 配合使用，以将 SQL Server 脱机迁移到 Azure SQL 托管实例](tutorial-sql-server-managed-instance-offline-ads.md)。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [下载并安装 Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* 从 Azure Data Studio 市场[安装 Azure SQL 迁移扩展](/sql/azure-data-studio/extensions/azure-sql-migration-extension)
* 有一个 Azure 帐户，该帐户已分配给下列内置角色之一：
    - 目标 Azure SQL 托管实例（以及从 SMB 网络共享上传数据库备份文件的存储帐户）的参与者。
    - 包含目标 Azure SQL 托管实例或 Azure 存储帐户的 Azure 资源组的“所有者”或“参与者”角色。
    - Azure 订阅所有者或参与者角色。
* 创建目标 [Azure SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)。
* 确保用于连接源 SQL Server 的登录名是 sysadmin 服务器角色或具有 `CONTROL SERVER` 权限的成员。 
* 为完整数据库和事务日志备份文件使用以下存储选项之一： 
    - SMB 网络共享 
    - Azure 存储帐户文件共享或 blob 容器 

    > [!IMPORTANT]
    > - 如果数据库备份文件在 SMB 网络共享中提供，请[创建一个 Azure 存储帐户](../storage/common/storage-account-create.md)，该帐户允许 DMS 服务上传数据库备份文件。  请务必在创建 Azure 数据库迁移服务实例的同一区域创建 Azure 存储帐户。
    > - Azure 数据库迁移服务不启动任何备份，而是使用现有备份进行迁移。你可能已经在灾难恢复计划中有了这些备份。
    > - 应[使用 `WITH CHECKSUM` 选项进行备份](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。 
    > - 可以将每个备份写入单个独立的备份文件或多个备份文件。 但是，不支持将多个备份（即完整备份和 t-log 备份）追加到单个备份介质中。 
    > - 使用压缩的备份来减少遇到与迁移大型备份相关的潜在问题的可能性。
* 确保运行源 SQL Server 实例的服务帐户对包含数据库备份文件的 SMB 网络共享具有读取和写入权限。
* 在迁移数据之前，需要将源 SQL Server 实例证书从受透明数据加密 (TDE) 保护的数据库迁移到目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。 若要了解详细信息，请参阅[将受 TDE 保护的数据库的证书迁移到 Azure SQL 托管实例](../azure-sql/managed-instance/tde-certificate-migrate.md)和[将受 TDE 保护的数据库迁移到另一个 SQL Server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。
    > [!TIP]
    > 如果数据库包含受 [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio) 保护的敏感数据，则在 Azure Data Studio 中使用 DMS 的迁移过程会自动将你的 Always Encrypted 密钥迁移到目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

* 如果数据库备份在网络文件共享中，请提供计算机以安装[自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)，以访问和迁移数据库备份。 迁移向导提供下载链接和身份验证密钥，用于下载和安装自承载集成运行时。 在为迁移做准备时，请确保计划安装自承载集成运行时的计算机启用了以下出站防火墙规则和域名：

    | 域名                                          | 出站端口 | 说明                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | 公有云：`{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` <br> Azure 政府版：`{datafactory}.{region}.datafactory.azure.us` <br> 中国：`{datafactory}.{region}.datafactory.azure.cn` | 443            | 自承载集成运行时连接到数据迁移服务时需要此端口。 <br>对于在公有云中新创建的数据工厂，请从自承载集成运行时密钥（格式为 `{datafactory}.{region}.datafactory.azure.net`）查找 FQDN。 对于旧数据工厂，如果在自承载集成密钥中找不到 FQDN，请改用 *.frontend.clouddatahub.net。 |
    | `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过对此域的配置。 |
    | `*.core.windows.net`          | 443            | 由连接到 Azure 存储帐户的自承载集成运行时使用，用于从网络共享上传数据库备份 |

    > [!TIP]
    > 如果数据库备份文件已在 Azure 存储帐户中提供，则在迁移过程中不需要自承载集成运行时。

* 使用自承载集成运行时时，请确保安装了运行时的计算机可以连接到源 SQL Server 实例和备份文件所在的网络文件共享。 应该启用出站端口 445，以允许访问网络文件共享。 另请参阅[使用自承载集成运行时的建议](migration-using-azure-data-studio.md#recommendations-for-using-self-hosted-integration-runtime-for-database-migrations)
* 如果是首次使用 Azure 数据库迁移服务，请确保在订阅中注册了 Microsoft.DataMigration 资源提供程序。 可以按照步骤[注册资源提供程序](quickstart-create-data-migration-service-portal.md#register-the-resource-provider)

## <a name="launch-the-migrate-to-azure-sql-wizard-in-azure-data-studio"></a>在 Azure Data Studio 中启动“迁移到 Azure SQL”向导

1. 打开 Azure Data Studio 并选择服务器图标以连接到本地 SQL Server（或 Azure 虚拟机上的 SQL Server）。
1. 在服务器连接上，右键单击并选择“管理”。
1. 在服务器的主页上，选择“Azure SQL 迁移”扩展。
1. 在“Azure SQL 迁移”仪表板上，选择“迁移到 Azure SQL”以启动迁移向导。
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/launch-migrate-to-azure-sql-wizard.png" alt-text="启动“迁移到 Azure SQL”向导":::
1. 在迁移向导的第一个步骤中，将现有或新的 Azure 帐户链接到 Azure Data Studio。

## <a name="run-database-assessment-and-select-target"></a>运行数据库评估并选择目标

1. 选择要对其运行评估的数据库，然后选择“下一步”。
1. 选择“Azure SQL 托管实例”作为目标。
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-complete-target-selection.png" alt-text="评估确认":::
1. 选择“查看/选择”按钮查看数据库评估结果的详细信息，选择要迁移的数据库，然后选择“确定” 。 如果评估结果中显示了任何问题，则需要在继续执行后续步骤之前修正这些问题。
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/assessment-issues-details.png" alt-text="数据库评估详细信息":::
1. 从相应的下拉列表中选择订阅、位置和资源组来指定目标 Azure SQL 托管实例，然后选择“下一步” 。

## <a name="configure-migration-settings"></a>配置迁移设置

1. 选择“联机迁移”作为迁移模式。
    > [!NOTE]
    > 在联机迁移模式下，在目标 Azure SQL 托管实例上持续还原数据库备份时，可以对源 SQL Server 数据库进行读取和写入活动。 应用程序停机时间限制为迁移结束时的直接转换持续时间。
1. 选择数据库备份的位置。 数据库备份可以位于本地网络共享或 Azure 存储 Blob 容器中。
    > [!NOTE]
    > 如果数据库备份是在本地网络共享中提供的，DMS 将要求你在向导的下一步中安装自承载集成运行时。 如果要访问源数据库备份、检查备份集的有效性并将其上传到 Azure 存储帐户，则需要自承载集成运行时。<br/> 如果数据库备份已在 Azure 存储 Blob 容器中，则无需安装自承载集成运行时。
1. 选择备份位置后，请提供源 SQL Server 和源备份位置的详细信息。

    |字段    |说明  |
    |---------|-------------|
    |源凭据 - 用户名    |用于连接到源 SQL Server 实例并验证备份文件的凭据（Windows / SQL身份验证）。         |
    |源凭据 - 密码    |用于连接到源 SQL Server 实例并验证备份文件的凭据（Windows / SQL身份验证）。         |
    |包含备份的网络共享位置     |包含完整备份文件和事务日志备份文件的网络共享位置。 在迁移过程中，将自动忽略网络共享中不属于有效备份集的任何无效文件或备份文件。        |
    |拥有网络共享位置读取权限的 Windows 用户帐户     |拥有网络共享读取权限以检索备份文件的 Windows 凭据（用户名）。       |
    |**密码**     |拥有网络共享读取权限以检索备份文件的 Windows 凭据（密码）。         |
    |**目标数据库名称** |如果要在迁移过程中更改目标上的数据库名称，可以修改目标数据库名称。            |

1. 在订阅、位置和资源组的相应下拉列表中进行选择来指定 Azure 存储帐户   。 DMS 将利用此 Azure 存储帐户从网络共享上传数据库备份。 无需创建容器，因为在上传过程中 DMS 会在指定的存储帐户中自动创建 Blob 容器。
    > [!IMPORTANT]
    > 如果启用了环回检查功能，并且源 SQL Server 和文件共享位于同一台计算机上，则源将无法使用 FQDN 访问文件共享。 若要解决此问题，请按照[此处](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)的说明禁用环回检查功能

## <a name="create-azure-database-migration-service"></a>创建 Azure 数据库迁移服务

1. 创建新的 Azure 数据库迁移服务或重用之前创建的现有服务。
    > [!NOTE]
    > 如果以前使用 Azure 门户创建了 DMS，则不能在 Azure Data Studio 中提供的迁移向导中重复使用此 DMS。 只能重用以前使用 Azure Data Studio 创建的 DMS。
1. 在“资源组”处，可以选择现有 DMS 或需要创建新的 DMS。 “Azure 数据库迁移服务”下拉列表将列出所选资源组内的任何现有 DMS。
1. 若要重用现有 DMS，请从下拉列表中选择它，自承载集成运行时的状态将显示在页面底部。
1. 若要创建新的 DMS，请选择“新建”。 在“创建 Azure 数据库迁移服务”屏幕上提供 DMS 的名称，并选择“创建” 。
1. 成功创建 DMS 后，“安装集成运行时”处会提供详细信息供你安装集成运行时。
1. 选择“下载并安装集成运行时”以在 Web 浏览器中打开下载链接。 完成下载。 在满足先决条件的计算机上安装集成运行时，具体条件是计算机需连接到源 SQL Server 和包含源备份的位置。
1. 安装完成后，Microsoft Integration Runtime Configuration Manager 将自动启动以开始注册过程。
1. 复制并粘贴 Azure Data Studio 中向导屏幕提供的身份验证密钥。 如果身份验证密钥有效，Integration Runtime Configuration Manager 中会显示绿色复选图标，指示可以继续注册。
1. 成功完成自承载集成运行时的注册后，关闭 Microsoft Integration Runtime Configuration Manager 并切换回 Azure Data Studio 中的迁移向导。
1. 在 Azure Data Studio 的“创建 Azure 数据库迁移服务”屏幕中选择“测试链接”来验证新建的 DMS 是否连接到新注册的自承载集成运行时 。
    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/test-connection-integration-runtime-complete.png" alt-text="测试连接集成运行时":::
1. 查看迁移摘要并选择“完成”以启动数据库迁移。

## <a name="monitor-your-migration"></a>监视迁移

1. 在“数据库迁移状态”中，可以跟踪正在进行的迁移、已完成的迁移和失败的迁移（如果有）。

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/monitor-migration-dashboard.png" alt-text="监视迁移仪表板":::
1. 选择“正在进行的数据库迁移”来查看正在进行的迁移，选择数据库名称可获取更多详细信息。
1. “迁移详细信息”页显示备份文件和相应状态：

    | 状态 | 说明 |
    |--------|-------------|
    | 已到达 | 备份文件已到达源备份位置并进行了验证 |
    | 上传 | 集成运行时当前正在将备份文件上传到 Azure 存储|
    | 已上传 | 备份文件已上传到 Azure 存储 |
    | Restoring | Azure 数据库迁移服务当前正在将备份文件还原到 Azure SQL 托管实例|
    | 还原 | 备份文件已成功还原到 Azure SQL 托管实例 |
    | 已取消 | 迁移过程已取消 |
    | 忽略 | 已忽略备份文件，因为它不属于有效的数据库备份链 |

    :::image type="content" source="media/tutorial-sql-server-to-managed-instance-online-ads/online-to-mi-migration-details-all-backups-restored.png" alt-text="备份还原详细信息":::

## <a name="complete-migration-cutover"></a>完成迁移直接转换

本教程的最后一个步骤是完成迁移切换，以确保 Azure SQL 托管实例中已迁移的数据库可供使用。 这是迁移过程中唯一需要使连接到数据库的应用程序停机的步骤，因此，需要与业务或应用程序利益干系人一起认真规划切换持续时间。

若要完成切换，请执行以下操作：

1. 停止源数据库的所有传入事务，并准备进行任何应用程序配置更改，以指向 Azure SQL 托管实例中的目标数据库。
2. 在指定的备份位置中为源数据库创建任何结尾日志备份
3. 确保所有数据库备份都在监视详细信息页中显示为“已还原”状态
4. 在监视详细信息页中选择“完成切换”

在切换过程中，迁移状态将从“正在进行”更改为“正在完成” 。 完成切换过程后，迁移状态将更改为“成功”，表示数据库迁移成功，且迁移的数据库可供使用。

> [!IMPORTANT]
> 交接后，具有业务关键服务层的 SQL 托管实例只能用比具有常规用途服务层的 SQL 托管实例更长的时间才能使用，因为必须为 AlwaysOn 高可用性组设定三个辅助副本的种子。 此操作持续时间取决于数据的大小，有关详细信息，请参阅[管理操作持续时间](../azure-sql/managed-instance/management-operations-overview.md#duration)。

## <a name="next-steps"></a>后续步骤

* 有关介绍如何使用 T-SQL RESTORE 命令将数据库迁移到 SQL 托管实例的教程，请参阅[使用 restore 命令将备份还原到 SQL 托管实例](../azure-sql/managed-instance/restore-sample-database-quickstart.md)。
* 有关 SQL 托管实例的信息，请参阅[什么是 SQL 托管实例](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)。
* 若要了解如何将应用连接到 SQL 托管实例，请参阅[连接应用程序](../azure-sql/managed-instance/connect-application-instance.md)。
