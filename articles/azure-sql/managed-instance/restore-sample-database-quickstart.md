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
ms.date: 12/14/2018
ms.openlocfilehash: 31cdd093db7f687ecf1e35e655cff469a03a8fec
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114690373"
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

## <a name="restore-from-a-backup-file"></a>从备份文件还原

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
> 数据库还原操作是异步且可重试的。 如果连接中断或某些超时过期，SQL Server Management Studio 中可能会显示错误。 Azure SQL 数据库将在后台继续尝试还原数据库，可以使用 [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 和 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 视图来跟踪还原进度。
> 在还原过程的某些阶段，系统视图中会显示唯一标识符，而不是实际的数据库名称。 在[此处](./transact-sql-tsql-differences-sql-server.md#restore-statement)了解 `RESTORE` 语句行为差异。

## <a name="next-steps"></a>后续步骤

- 如果在步骤 5 中，数据库还原终止并显示消息 ID 22003，请创建包含备份校验和的新备份文件，然后再次执行还原。 请参阅[在备份或还原期间启用或禁用备份校验和](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。
- 若要排查备份到 URL 时出现的问题，请参阅[将 SQL Server 备份到 URL 的最佳做法和故障排除](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)。
- 有关应用连接选项的概述，请参阅[将应用程序连接到 SQL 托管实例](connect-application-instance.md)。
- 若要使用偏好的工具或语言进行查询，请参阅[快速入门：Azure SQL 数据库连接和查询](../database/connect-query-content-reference-guide.md)。
