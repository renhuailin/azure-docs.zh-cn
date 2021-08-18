---
title: 排查连接问题
description: 排查专用 SQL 池（以前称为 SQL DW）中的连接问题。
services: synapse-analytics
author: rothja
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: jroth
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 923000d0a0579df2bcdcd82e7c4ad3bf13a1213e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441823"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>排查专用 SQL 池（以前称为 SQL DW）中的连接问题

本文列出了有关连接到专用 SQL 池数据库（以前称为 SQL DW）的常用故障排除方法。

## <a name="check-service-availability"></a>检查服务可用性

检查服务是否可用。 在 Azure 门户中转到要尝试连接的专用 SQL 池（以前称为 SQL DW）。 在左侧 TOC 面板中，单击“诊断并解决问题”。

![选择“资源运行状况”](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

专用 SQL 池（以前称为 SQL DW）的状态会显示在此处。 如果该服务未显示为“可用”，请查看其他步骤。

![可用服务](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

如果资源运行状况显示专用 SQL 池（以前称为 SQL DW）实例已暂停或正在缩放，请按指南恢复实例。

![屏幕截图显示了已暂停或正在缩放的专用 SQL 池的实例。](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
可以在此处找到有关资源运行状况的其他信息。

## <a name="check-for-paused-or-scaling-operation"></a>检查已暂停的操作或正在缩放的操作

检查门户，了解专用 SQL 池（以前称为 SQL DW）实例是已暂停还是正在缩放。

![屏幕截图显示了如何检查数据仓库是否已暂停。](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

如果发现服务已暂停或正在缩放，请检查其是否未处于维护计划期间。 在专用 SQL 池（以前称为 SQL DW）对应的门户的“概述”中，可以看到所选的维护计划。

![维护计划概述](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

否则，请与 IT 管理员联系以验证此维护不是计划事件。 若要恢复专用 SQL 池（以前称为 SQL DW）实例，请按照[这些步骤](pause-and-resume-compute-portal.md)操作。

## <a name="check-your-firewall-settings"></a>检查防火墙设置

专用 SQL 池（以前称为 SQL DW）数据库通过端口 1433 进行通信。如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 这种情况下无法连接到[逻辑服务器](../../azure-sql/database/logical-servers.md)，除非 IT 部门打开了端口 1433。 可从[此处](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)找到有关防火墙配置的更多信息。

## <a name="check-your-vnetservice-endpoint-settings"></a>检查 VNet/服务终结点设置

如果收到 40914 和 40615 错误，请参阅[此处的错误说明和解决方法](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="check-for-the-latest-drivers"></a>检查最新驱动程序

### <a name="software"></a>软件

请进行检查，确保使用最新工具连接到专用 SQL 池（以前称为 SQL DW）：

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>驱动程序

请进行检查，确保使用最新的驱动程序版本。  使用旧版驱动程序可能会导致意外行为，因为旧驱动程序可能不支持新功能。

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="check-your-connection-string"></a>检查连接字符串

请进行检查，确保正确设置了连接字符串。  下面是一些示例。  可在[此处](sql-data-warehouse-connection-strings.md)找到有关连接字符串的其他信息。

ADO.NET 连接字符串

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 连接字符串

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 连接字符串

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 连接字符串

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>间歇性连接问题

检查是否在服务器上遇到具有大量排队请求的重型负载。 可能需要纵向扩展专用 SQL 池（以前称为 SQL DW），以获取更多资源。

## <a name="common-error-messages"></a>常见错误消息

对于 40914 和 40615 错误，请参阅[此处的错误说明和解决方法](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)。

## <a name="still-having-connectivity-issues"></a>仍有连接问题？

创建[支持票证](sql-data-warehouse-get-started-create-support-ticket.md)，以便工程团队提供支持。
