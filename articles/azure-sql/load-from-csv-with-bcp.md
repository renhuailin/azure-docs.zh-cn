---
title: 将数据从 CSV 文件载入数据库 (bcp)
description: 对于较小的数据，请使用 bcp 将数据导入到 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: dzsquared
ms.author: drskwier
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: 6fe0fdf53235ce1ec42a2b46e95291c111ed2dff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743691"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>将数据从 CSV 加载到 Azure SQL 数据库或 SQL 托管实例（平面文件）
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

可以使用 bcp 命令行实用程序将数据从 CSV 文件导入 Azure SQL 数据库或 Azure SQL 托管实例。

## <a name="before-you-begin"></a>准备阶段

### <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要：

* Azure SQL 数据库中的数据库
* 已安装 bcp 命令行实用工具
* 已安装 sqlcmd 命令行实用工具

可以从 [Microsoft sqlcmd 文档](/sql/tools/sqlcmd-utility?view=sql-server-ver15&preserve-view=true)下载 bcp 和 sqlcmd 实用工具。

### <a name="data-in-ascii-or-utf-16-format"></a>采用 ASCII 或 UTF-16 格式的数据

如果使用自己的数据尝试学习本教程，则数据需要使用 ASCII 或 UTF-16 编码，因为 bcp 不支持 UTF-8。

## <a name="1-create-a-destination-table"></a>1.创建目标表

将 SQL 数据库中的某个表定义为目标表。 该表中的列必须对应于数据文件每一行中的数据。

若要创建表，请打开命令提示符并使用 sqlcmd.exe 运行以下命令：

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2.创建源数据文件

打开记事本，将以下几行数据复制到新文本文件，然后将此文件保存到本地临时目录 C:\Temp\DimDate2.txt。 此数据采用 ASCII 格式。

```txt
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

（可选）若要从 SQL Server 数据库导出自己的数据，请打开命令提示符并运行以下命令。 将 TableName、ServerName、DatabaseName、Username 和 Password 替换成自己的信息。

```cmd
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3.加载数据

要加载数据，请打开命令提示符并运行以下命令，请注意将 Server Name、Database Name、Username 和 Password 替换为你自己的信息。

```cmd
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

使用此命令来验证是否已正确加载数据

```cmd
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

结果应如下所示：

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>后续步骤

若要迁移 SQL Server 数据库，请参阅 [SQL Server 数据库迁移](database/migrate-to-database-from-sql-server.md)。

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
