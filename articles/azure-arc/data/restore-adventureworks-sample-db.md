---
title: 将 AdventureWorks 示例数据库还原到 SQL 托管实例
description: 将 AdventureWorks 示例数据库还原到 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 29b266732b8e303f39d16587f61beedc1710ea3c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741457"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>将 AdventureWorks 示例数据库还原到 SQL 托管实例 - Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure) 是一个示例数据库，其中包含教程和示例中经常使用的 OLTP 数据库。 它由 Microsoft 提供并维护，是 [SQL Server 示例 GitHub 存储库](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

本文档介绍将 AdventureWorks 示例数据库还原到 SQL 托管实例 - Azure Arc 的简单过程。


## <a name="download-the-adventureworks-backup-file"></a>下载 AdventureWorks 备份文件

将 AdventureWorks 备份 (.bak) 文件下载到 SQL 托管实例容器中。 此示例使用 `kubectl exec` 命令在 SQL 托管实例容器中远程执行某个命令，以将 .bak 文件下载到该容器中。 如果你想要将其他数据库备份文件拉取到 SQL 托管实例容器中，请从 `wget` 可访问的任何位置下载此 .bak 文件。 将此文件下载到 SQL 托管实例容器后，便可以轻松使用标准的 `RESTORE DATABASE` T-SQL 进行还原。

运行如下所示的命令下载 .bak 文件（在运行之前请替换 Pod 名称和命名空间名称的值）。
> [!NOTE]
>  容器需要通过端口 443 建立 Internet 连接才能从 GitHub 下载文件。

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

示例

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>还原 AdventureWorks 数据库

同样，可以运行 `kubectl` exec 命令，以使用 SQL 托管实例容器中包含的 `sqlcmd` CLI 工具来执行 T-SQL 命令 RESTORE DATABASE。

运行如下所示的命令还原数据库。 在运行该命令之前，请替换 Pod 名称、密码和命名空间名称的值。

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
示例

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
