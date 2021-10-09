---
title: 快速入门：还原备份 (SSMS)
titleSuffix: Azure SQL Managed Instance
description: 本快速入门介绍如何使用 SQL Server Management Studio (SSMS) 将数据库备份还原到 Azure 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: misliplavo
ms.author: mlazic
ms.reviewer: mathoma
ms.date: 09/13/2021
ms.openlocfilehash: f683bbd008e47a154fac11d89e8d06f0e07b87f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674123"
---
# <a name="quickstart-restore-a-database-to-azure-sql-managed-instance-with-ssms"></a>快速入门：使用 SSMS 将数据库还原到 Azure SQL 托管实例
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本快速入门介绍如何使用 SQL Server Management Studio (SSMS) 将某个数据库（Wide World Importers - Standard 备份文件）从 Azure Blob 存储还原到 [Azure SQL 托管实例](sql-managed-instance-paas-overview.md)。

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> 有关使用 Azure 数据库迁移服务进行迁移的详细信息，请参阅[教程：使用数据库迁移服务将 SQL Server 迁移到 Azure 托管实例](../../dms/tutorial-sql-server-to-managed-instance.md)。
> 有关各种迁移方法的详细信息，请参阅[SQL Server 到 Azure SQL 托管实例迁移指南](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)。

## <a name="prerequisites"></a>先决条件

本快速入门：

- 使用[创建托管实例](instance-create-quickstart.md)快速入门中的资源。
- 需要安装最新版本的 [SSMS](/sql/ssms/sql-server-management-studio-ssms)。
- 要求使用 SSMS 连接到 SQL 托管实例。 有关连接方法，请参阅以下快速入门：
  - 在 SQL 托管实例上[启用公共终结点](public-endpoint-configure.md)，这是本教程推荐的方法。
  - [从 Azure VM 连接到 SQL 托管实例](connect-vm-instance-configure.md)。
  - [配置从本地到 SQL 托管实例的点到站点连接](point-to-site-p2s-configure.md)。

> [!NOTE]
> 有关使用 Azure Blob 存储与[共享访问签名 (SAS) 密钥](../../storage/common/storage-sas-overview.md)备份和还原 SQL Server 数据库的详细信息，请参阅[将 SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

## <a name="restore-from-a-backup-file-using-the-restore-wizard"></a>使用还原向导从备份文件还原

在 SSMS 中，按照以下步骤使用还原向导将 Wide World Importers 数据库还原到 SQL 托管实例。 数据库备份文件存储在预配置的 Azure Blob 存储帐户中。

1. 打开 SSMS 并连接到托管实例。
2. 在“对象资源管理器”中，右键单击托管实例的数据库并选择“还原数据库”以打开还原向导。

    ![屏幕截图显示正在打开还原向导。](./media/restore-sample-database-quickstart/restore-wizard-start.png)

3. 在新的还原向导中，选择省略号 (...) 以选择要使用的备份文件的源。

    ![屏幕截图显示正在打开新的还原向导窗口。](./media/restore-sample-database-quickstart/new-restore-wizard.png)

4. 在“选择备份设备”中，选择“添加”。 在“备份媒体类型”中，“URL”是唯一的选项，因为它是唯一受支持的源类型。 选择“确定”。

    ![屏幕截图显示正在选择设备。](./media/restore-sample-database-quickstart/restore-wizard-select-device.png)

5. 在“选择备份文件位置”中，可以从三个选项中进行选择，以提供有关备份文件所在位置的信息：
    - 从下拉列表中选择一个预注册的存储容器。
    - 输入新的存储容器和共享访问签名。 （将为你注册一个新的 SQL 凭据。） 
    - 选择“添加”以浏览 Azure 订阅中的更多存储容器。

    ![屏幕截图显示正在选择备份文件位置。](./media/restore-sample-database-quickstart/restore-wizard-backup-file-location.png)

    如果选择“添加”按钮，请完成后续步骤。 如果使用其他方法提供备份文件位置，请转到步骤 12。
6. 在“连接到 Microsoft 订阅”中，选择“登录”以登录到 Azure 订阅：

    ![屏幕截图显示 Azure 订阅登录。](./media/restore-sample-database-quickstart/restore-wizard-connect-subscription-sign-in.png)

7. 登录到 Microsoft 帐户，以在 Azure 中启动会话：

    ![屏幕截图显示正在登录到 Azure 会话。](./media/restore-sample-database-quickstart/restore-wizard-sign-in-session.png)

8. 选择包含备份文件的存储帐户所在的订阅：

    ![屏幕截图显示正在选择订阅。](./media/restore-sample-database-quickstart/restore-wizard-select-subscription.png)

9. 选择备份文件所在的存储帐户：

    ![屏幕截图显示存储帐户。](./media/restore-sample-database-quickstart/restore-wizard-select-storage-account.png)

10. 选择备份文件所在的 Blob 容器：

    ![选择 Blob 容器](./media/restore-sample-database-quickstart/restore-wizard-select-container.png)

11. 指定共享访问策略的到期日期并选择“创建凭据”。 系统将创建一个具有正确权限的共享访问签名。 选择“确定”。

    ![屏幕截图显示正在生成共享访问签名。](./media/restore-sample-database-quickstart/restore-wizard-generate-shared-access-signature.png)

12. 在左侧窗格中，展开文件夹结构以显示备份文件所在的文件夹。 选择与要还原的备份集相关的所有备份文件，然后选择“确定”：

    ![屏幕截图显示备份文件选择。](./media/restore-sample-database-quickstart/restore-wizard-backup-file-selection.png)

    SSMS 将验证备份集。 此过程最多需要几秒钟的时间，具体取决于备份集的大小。

13. 如果备份通过验证，请指定目标数据库名称或保留备份集的数据库名称，然后选择“确定”：

    ![屏幕截图显示正在开始还原。](./media/restore-sample-database-quickstart/restore-wizard-start-restore.png)

    开始还原。 持续时间取决于备份集的大小。

    ![屏幕截图显示正在运行还原。](./media/restore-sample-database-quickstart/restore-wizard-running-restore.png)

14. 还原完成后，会出现一个对话框，显示它已成功。 选择“确定”。

    ![屏幕截图显示已完成的还原。](./media/restore-sample-database-quickstart/restore-wizard-finish-restore.png)

15. 在对象资源管理器中检查还原的数据库：

    ![屏幕截图显示还原的数据库。](./media/restore-sample-database-quickstart/restore-wizard-restored-database.png)


## <a name="restore-from-a-backup-file-using-t-sql"></a>使用 T-SQL 从备份文件还原

在 SQL Server Management Studio 中，请按照以下步骤将 Wide World Importers 数据库还原到 SQL 托管实例。 数据库备份文件存储在预配置的 Azure Blob 存储帐户中。

1. 打开 SSMS 并连接到托管实例。
2. 在“对象资源管理器”中，右键单击托管实例，并选择“新建查询”以打开新的查询窗口 。
3. 运行以下 SQL 脚本，该脚本使用预配置的存储帐户和 SAS 密钥在托管实例中[创建凭据](/sql/t-sql/statements/create-credential-transact-sql)。
 
   > [!IMPORTANT]
   > `CREDENTIAL` 必须与容器路径匹配，以 `https` 开头，结尾不能包含正斜杠。 `IDENTITY` 必须为 `SHARED ACCESS SIGNATURE`。 `SECRET` 必须是共享访问签名令牌，且不能包含前导 `?`。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![创建凭据](./media/restore-sample-database-quickstart/credential.png)

4. 若要检查凭据，请运行以下脚本。该脚本使用[容器](https://azure.microsoft.com/services/container-instances/) URL 获取备份文件列表。

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![文件列表](./media/restore-sample-database-quickstart/file-list.png)

5. 运行以下脚本，还原 Wide World Importers 数据库。

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![屏幕截图显示在对象资源管理器中运行的脚本，并显示一条成功消息。](./media/restore-sample-database-quickstart/restore.png)

6. 运行以下脚本跟踪还原状态。

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. 还原完成后，请在对象资源管理器中查看数据库。 可以使用 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图验证是否已完成数据库还原。

> [!NOTE]
> 数据库还原操作是异步且可重试的。 如果连接中断或某些超时过期，SQL Server Management Studio 中可能会显示错误。 Azure SQL 托管实例将在后台继续尝试还原数据库，你可以使用 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图来跟踪还原进度。
> 在还原过程的某些阶段，系统视图中会显示唯一标识符，而不是实际的数据库名称。 在[此处](./transact-sql-tsql-differences-sql-server.md#restore-statement)了解 `RESTORE` 语句行为差异。

## <a name="next-steps"></a>后续步骤

- 如果在步骤 5 中，数据库还原终止并显示消息 ID 22003，请创建包含备份校验和的新备份文件，然后再次执行还原。 请参阅[在备份或还原期间启用或禁用备份校验和](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。
- 若要排查备份到 URL 时出现的问题，请参阅[将 SQL Server 备份到 URL 的最佳做法和故障排除](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)。
- 有关应用连接选项的概述，请参阅[将应用程序连接到 SQL 托管实例](connect-application-instance.md)。
- 若要使用偏好的工具或语言进行查询，请参阅[快速入门：Azure SQL 数据库连接和查询](../database/connect-query-content-reference-guide.md)。
