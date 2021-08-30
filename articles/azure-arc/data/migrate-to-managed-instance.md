---
title: 将数据库从 SQL Server 迁移到已启用 Azure Arc 的 SQL 托管实例
description: 将数据库从 SQL Server 迁移到已启用 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: d93fa1d16366ee131d98aebe2ab8e3e040949d72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752348"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>迁移：从 SQL Server 到已启用 Azure Arc 的 SQL 托管实例

本方案将逐步引导你通过两种不同的备份和还原方法，将数据库从 SQL Server 实例迁移到 Azure Arc 中的 Azure SQL 托管实例。


## <a name="use-azure-blob-storage"></a>使用 Azure Blob 存储

使用 Azure Blob 存储迁移到已启用 Azure Arc 的 SQL 托管实例。

此方法使用 Azure Blob 存储作为临时存储位置，你可将数据库备份到该位置，然后从中还原。

### <a name="prerequisites"></a>先决条件

- [安装 Azure Data Studio](install-client-tools.md)

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- [安装 Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
- Azure 订阅



### <a name="step-1-provision-azure-blob-storage"></a>步骤 1：预配 Azure Blob 存储

1. 按照[创建 Azure Blob 存储帐户](../../storage/common/storage-account-create.md?tabs=azure-portal)中所述的步骤操作
1. 启动 Azure 存储资源管理器
1. [登录到 Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) 以访问在上一步骤中创建的 Blob 存储
1. 右键单击 Blob 存储帐户，并选择“创建 Blob 容器”以创建要将备份文件存储到的新容器

### <a name="step-2-get-storage-blob-credentials"></a>步骤 2：获取存储 Blob 凭据

1. 在 Azure 存储资源管理器中，右键单击刚刚创建的 Blob 容器，并选择“获取共享访问签名”

1. 选择“读取”、“写入”和“列出”  

1. 选择“创建”

   请记下此屏幕中的 URI 和查询字符串。 在后面的步骤中需要用到这些信息。 单击“复制”按钮将这些信息保存到记事本/OneNote 等程序中。

1. 关闭“共享访问签名”窗口。

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>步骤 3：将数据库文件备份到 Azure Blob 存储

在此步骤中，我们将连接到源 SQL Server，并创建要迁移到 SQL 托管实例 - Azure Arc 的数据库的备份文件。

1. 启动 Azure Data Studio
1. 连接到 SQL Server 实例，其中包含你要迁移到 SQL 托管实例 - Azure Arc 的数据库
1. 右键单击该数据库并选择“新建查询”
1. 使用在前面步骤中记下的共享访问签名中的信息替换 `<...>` 指示的占位符，按以下格式准备查询。  替换值后，运行该查询。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 同样，请按如下所示准备 BACKUP DATABASE 命令，以在 Blob 容器中创建备份文件。  替换值后，运行该查询。

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. 打开 Azure 存储资源管理器，验证在上一步骤中创建的备份文件是否显示在 Blob 容器中

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>步骤 4：将数据库从 Azure Blob 存储还原到 SQL 托管实例 - Azure Arc

1. 在 Azure Data Studio 中，登录并连接到 SQL 托管实例 - Azure Arc。
1. 展开“系统数据库”，右键单击“master”数据库并选择“新建查询”  。
1. 在查询编辑器窗口中，准备并运行在上一步骤中所用的同一查询以创建凭据。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 准备并运行以下命令，以验证备份文件是否可读且完好无损。

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. 按如下所示准备并运行 BACKUP DATABASE 命令，将备份文件还原到 SQL 托管实例 - Azure Arc 上的数据库

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

通过以下资源详细了解如何备份到 URL：

[备份到 URL 文档](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[使用 SQL Server Management Studio (SSMS) 备份到 URL](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>方法 2：使用 kubectl 将备份文件复制到 Azure SQL 托管实例 - Azure Arc Pod 中

此方法说明如何获取通过任一方法创建的备份文件，然后将其复制到 Azure SQL 托管实例 Pod 中的本地存储，以便可以像在 Windows 或 Linux 上的典型文件系统中那样，从该文件进行还原。 此方案使用命令 `kubectl cp` 将文件从某个位置复制到 Pod 的文件系统中。

### <a name="prerequisites"></a>先决条件

- 安装并配置 kubectl，使其指向部署了 Azure Arc 数据服务的 Kubernetes 群集
- 安装好 Azure Data Studio 或 SQL Server 管理服务器等工具，并将其连接到要在其中创建备份文件的 SQL Server；或者，准备好一个已在本地文件系统上创建的现有 .bak 文件。

### <a name="step-1-backup-the-database-if-you-havent-already"></a>步骤 1：备份数据库（如果尚未这样做）

就像在磁盘中进行任何典型的 SQL Server 备份一样，将 SQL Server 数据库备份到本地文件路径：

```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test' ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>步骤 2：将备份文件复制到 Pod 的文件系统中

找到部署了 SQL 实例的 Pod 的名称。 该名称通常类似于 `pod/<sqlinstancename>-0`

运行以下命令获取所有 Pod 的列表：

```console
kubectl get pods -n <namespace of data controller>
```

示例：

将备份文件从本地存储复制到群集中的 SQL Pod。

```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>步骤 3：还原数据库

准备并运行 RESTORE 命令，将备份文件还原到 Azure SQL 托管实例 - Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

示例：

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

## <a name="next-steps"></a>后续步骤

[详细了解已启用 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)

[首先创建数据控制器](create-data-controller.md)

[已创建数据控制器？请创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)
