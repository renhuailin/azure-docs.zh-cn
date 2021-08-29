---
title: Synapse SQL 的连接字符串
description: Synapse SQL 的连接字符串
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: dfc82cfec8e2c356b0d2fcc3806e21a3bd01a299
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862506"
---
# <a name="connection-strings-for-synapse-sql"></a>Synapse SQL 的连接字符串

可以使用几种不同的应用程序协议（例如 [ADO.NET](/dotnet/framework/data/adonet/)、[ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows)、[PHP](/sql/connect/php/overview-of-the-php-sql-driver?f=255&MSPPError=-2147217396) 和 [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)）连接到 Synapse SQL。 下面是每个协议的连接字符串的一些示例。 

可以使用 Azure 门户来生成连接字符串。  若要使用 Azure 门户生成连接字符串，请导航到数据库边栏选项卡，在“概要”下选择“显示数据库连接字符串”。

## <a name="sample-adonet-connection-string"></a>示例 ADO.NET 连接字符串

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>示例 ODBC 连接字符串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>示例 PHP 连接字符串

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>示例 JDBC 连接字符串

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> 请考虑将连接超时值设置为 300 秒，使连接可以经受住短时间内不可用的情况。

## <a name="recommendations"></a>建议

若要执行无服务器 SQL 池查询，建议使用 [Azure Data Studio](get-started-azure-data-studio.md) 和 Azure Synapse Studio 这两个工具。

## <a name="next-steps"></a>后续步骤

若要开始使用 Visual Studio 和其他应用程序来查询分析，请参阅[使用 Visual Studio 进行查询](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?context=/azure/synapse-analytics/context/context)。