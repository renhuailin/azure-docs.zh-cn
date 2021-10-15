---
title: 排查 Azure Synapse Analytics、Azure SQL 数据库和 SQL Server 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Synapse Analytics、Azure SQL 数据库和 SQL Server 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f258ba3b8c62d0d290785bbf6a66cc7651286162
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390375"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Azure Synapse Analytics、Azure SQL 数据库和 SQL Server 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 Azure Synapse Analytics、Azure SQL 数据库和 SQL Server 连接器问题的建议。

## <a name="error-code-sqlfailedtoconnect"></a>错误代码：SqlFailedToConnect

- **消息**：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 对于 Azure SQL，如果错误消息包含字符串“SqlErrorNumber=47073”，则表示在连接设置中拒绝公共网络访问。 | 在 Azure SQL 防火墙中，请将“拒绝公用网络访问”选项设置为“否”。 有关详细信息，请参阅 [Azure SQL 连接设置](../azure-sql/database/connectivity-settings.md#deny-public-network-access)。 |
    | 对于 Azure SQL，如果错误消息包含 SQL 错误代码（例如“SqlErrorNumber=[errorcode]”），请查看 Azure SQL 疑难解答指南。 | 有关建议，请参阅[排查 Azure SQL 数据库和 Azure SQL 托管实例的连接问题和其他错误](../azure-sql/database/troubleshoot-common-errors-issues.md)。 |
    | 检查端口 1433 是否在防火墙允许列表中。 | 有关详细信息，请参阅 [SQL Server 使用的端口](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)。 |
    | 如果错误消息包含字符串“SqlException”，则该错误是由 SQL 数据库引发的，表示某个特定操作失败。 | 有关详细信息，请按[数据库引擎错误](/sql/relational-databases/errors-events/database-engine-events-and-errors)中的 SQL 错误代码进行搜索。 如需更多帮助，请联系 Azure SQL 支持部门。 |
    | 如果这是暂时性问题（例如网络连接不稳定），请在活动策略中添加重试操作来进行缓解。 | 有关详细信息，请参阅[管道和活动](./concepts-pipelines-activities.md#activity-policy)。 |
    | 如果错误消息包含“不允许 IP 地址为 '…' 的客户端访问服务器”这一字符串，并且你正在尝试连接到 Azure SQL 数据库，则该错误通常是由 Azure SQL 数据库防火墙问题引起的。 | 在 Azure SQL Server 防火墙配置中，启用“允许 Azure 服务和资源访问此服务器”选项。 有关详细信息，请参阅 [Azure SQL 数据库和 Azure Synapse IP 防火墙规则](../azure-sql/database/firewall-configure.md)。 |
    
## <a name="error-code-sqloperationfailed"></a>错误代码：SqlOperationFailed

- **消息**：`A database operation failed. Please search error to get more details.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串“SqlException”，则该错误是由 SQL 数据库的，表示某个特定操作失败。 | 如果 SQL 错误不明确，请尝试将数据库更改为最新的兼容性级别“150”。 它可能会引发最新版本 SQL 错误。 有关详细信息，请参阅[本文档](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)。 <br/> 若要详细了解如何排查 SQL 问题，请按[数据库引擎错误](/sql/relational-databases/errors-events/database-engine-events-and-errors)中的 SQL 错误代码进行搜索。 如需更多帮助，请联系 Azure SQL 支持部门。 |
    | 如果错误消息包含字符串“PdwManagedToNativeInteropException”，则这通常是由于源列和接收器列的大小不匹配导致的。 | 请检查源列和接收器列的大小。 如需更多帮助，请联系 Azure SQL 支持部门。 |
    | 如果错误消息包含字符串“InvalidOperationException”，则通常是由于输入数据无效导致的。 | 若要确定是哪一行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 有关详细信息，请参阅[复制活动的容错](./copy-activity-fault-tolerance.md)。 |


## <a name="error-code-sqlunauthorizedaccess"></a>错误代码：SqlUnauthorizedAccess

- **消息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：请检查确保登录帐户有足够的权限访问 SQL 数据库。


## <a name="error-code-sqlopenconnectiontimeout"></a>错误代码：SqlOpenConnectionTimeout

- **消息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：此问题可能是 SQL 数据库暂时性故障。

- **建议**：请重试操作，使用更大的连接超时值更新链接的服务连接字符串。


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>错误代码：SqlAutoCreateTableTypeMapFailed

- **消息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：自动创建表无法满足源要求。

- **建议**：更新映射中的列类型，或者在目标服务器中手动创建接收器表。


## <a name="error-code-sqldatatypenotsupported"></a>错误代码：SqlDataTypeNotSupported

- **消息**：`A database operation failed. Check the SQL errors.`

- **原因**：如果问题发生在 SQL 源中，并且错误与 SqlDateTime 溢出有关，则数据值超出逻辑类型范围 (1753/1/1 12:00:00 AM - 9999/12/31 11:59:59 PM)。

- **建议**：在源 SQL 查询中将类型强制转换为字符串，或在复制活动列映射中将列类型更改为“字符串”。

- **原因**：如果问题发生在 SQL 接收器上，并且错误与 SqlDateTime 溢出有关，则数据值超出了接收器表中允许的范围。

- **建议**：在接收器表中将相应的列类型更新为“datetime2”类型。


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>错误代码：SqlInvalidDbStoredProcedure

- **消息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 原因可能是存储过程没有返回任何数据。

- **建议**：使用 SQL 工具验证存储过程。 请确保存储过程可返回数据。


## <a name="error-code-sqlinvaliddbquerystring"></a>错误代码：SqlInvalidDbQueryString

- **消息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 原因可能是查询没有返回任何数据。

- **建议**：使用 SQL 工具验证 SQL 查询。 确保查询可返回数据。


## <a name="error-code-sqlinvalidcolumnname"></a>错误代码：SqlInvalidColumnName

- **消息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：配置可能有误，找不到此列。

- **建议**：验证查询中的列、数据集中的结构和活动中的映射 。


## <a name="error-code-sqlbatchwritetimeout"></a>错误代码：SqlBatchWriteTimeout

- **消息**：`Timeouts in SQL write operation.`

- **原因**：此问题可能是由 SQL 数据库暂时性故障导致的。

- **建议**：重试操作。 如果仍然出现此问题，请联系 Azure SQL 支持部门。


## <a name="error-code-sqlbatchwritetransactionfailed"></a>错误代码：SqlBatchWriteTransactionFailed

- **消息**：`SQL transaction commits failed.`

- **原因**：如果异常详细信息持续指示事务超时，则集成运行时和数据库之间的网络延迟高于默认阈值 30 秒。

- **建议**：使用大于等于 120 的连接超时值更新 SQL 链接服务连接字符串，然后重新运行活动。

- **原因**：如果异常详细信息间歇性地显示 SQL 连接已断开，则可能只是暂时性的网络故障或 SQL 数据库端问题。

- **建议**：重试活动并审阅 SQL 数据库端指标。


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>错误代码：SqlBulkCopyInvalidColumnLength

- **消息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**：SQL 大容量复制失败，因为它从大容量复制程序实用工具 (bcp) 客户端接收到无效的列长度。

- **建议**：若要确定是哪一行遇到了问题，请在复制活动上启用容错功能。 这可将有问题的行重定向到存储，以供进一步调查。 有关详细信息，请参阅[复制活动的容错](./copy-activity-fault-tolerance.md)。


## <a name="error-code-sqlconnectionisclosed"></a>错误代码：SqlConnectionIsClosed

- **消息**：`The connection is closed by SQL Database.`

- **原因**：在高并发运行且服务器终止连接时，SQL 数据库关闭了 SQL 连接。

- **建议**：重试连接。 如果仍然出现此问题，请联系 Azure SQL 支持部门。

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>错误代码：SqlServerInvalidLinkedServiceCredentialMissing

- **消息**：`The SQL Server linked service is invalid with its credential being missing.`

- **原因**：链接服务的配置不正确。

- **建议**：验证并修复 SQL Server 链接服务。 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>错误代码：SqlParallelFailedToDetectPartitionColumn

- **消息**：`Failed to detect the partition column with command '%command;', %message;.`

- **原因**：表中没有主键或唯一键。

- **建议**：检查表以确保创建了主键或唯一索引。 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>错误代码：SqlParallelFailedToDetectPhysicalPartitions

- **消息**：`Failed to detect the physical partitions with command '%command;', %message;.`

- **原因**：没有为表创建物理分区。 请检查数据库。

- **建议**：若要解决此问题，请参阅 [创建已分区表和索引](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true)。

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>错误代码：SqlParallelFailedToGetPartitionRangeSynapse

- **消息**：`Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **原因**：没有为表创建物理分区。 请检查数据库。

- **建议**：若要解决此问题，请参阅 [ SQL 池中的分区表](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md)。

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：将字符串转换为唯一标识符失败

- **故障描述**：使用暂存复制和 PolyBase 将数据从表格数据源（例如 SQL Server）复制到 Azure Synapse Analytics 时，遇到以下错误：

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **原因**：Azure Synapse Analytics PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器的 PolyBase 设置下，将“使用类型默认值”选项设置为 false。


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型：DECIMAL(x,x)，违规值

- **故障描述**：使用暂存复制和 PolyBase 将表格数据源（例如 SQL Server）中的数据复制到 Azure Synapse Analytics 中时，遇到以下错误：

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **原因**：Azure Synapse Analytics PolyBase 无法将空字符串（null 值）插入十进制列。

- **解决方法**：在复制活动接收器的 PolyBase 设置下，将“使用类型默认值”选项设置为 false。


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息：HdfsBridge::CreateRecordReader

- **故障描述**：使用 PolyBase 将数据复制到 Azure Synapse Analytics 时，遇到以下错误：

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **原因**：这可能是架构（总列宽）太大（大于 1 MB）导致的。 通过添加所有列的大小来检查目标 Azure Synapse Analytics 表的架构：

    - Int = 4 字节
    - Bigint = 8 字节
    - Varchar(n)、char(n)、binary(n)、varbinary(n) = n 字节
    - Nvarchar(n)、nchar(n) = n*2 字节
    - Date = 6 字节
    - Datetime/(2)、smalldatetime = 16 字节
    - Datetimeoffset = 20 字节
    - Decimal = 19 字节
    - Float = 8 字节
    - Money = 8 字节
    - Smallmoney = 4 字节
    - Real = 4 字节
    - Smallint = 2 字节
    - Time = 12 字节
    - Tinyint = 1 字节

- **解决方法**： 
    - 请将列宽减至 1 MB 以下。
    - 或者，禁用 PolyBase 来使用批量插入方法。


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **故障描述**：使用 SQL 查询从 Azure Synapse Analytics 提取数据时，遇到以下错误：

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **原因**：Azure Synapse Analytics 在查询 Azure 存储中的外部表时遇到问题。

- **解决方法**：在 SQL Server Management Studio (SSMS) 中运行相同的查询，并检查是否获得相同的结果。 如果需要，请打开 Azure Synapse Analytics 的支持票证，并提供 Azure Synapse Analytics 服务器和数据库名称。


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>性能层等级较低，导致复制失败

- **故障描述**：将数据复制到 Azure SQL 数据库时，遇到以下错误：`Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**：Azure SQL 数据库 s1 已达到输入/输出 (I/O) 限制。

- **解决方法**：升级 Azure SQL 数据库性能层可解决此问题。 


## <a name="sql-table-cant-be-found"></a>找不到 SQL 表 

- **故障描述**：将数据从混合复制到本地 SQL Server 表时，遇到以下错误：`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**：当前的 SQL 帐户没有足够的权限来执行 .NET SqlBulkCopy.WriteToServer 发出的请求。

- **解决方法**：切换到权限更高的一个 SQL 帐户。


## <a name="error-message-string-or-binary-data-is-truncated"></a>错误消息：字符串或二进制数据被截断

- **故障描述**：将数据复制到本地 Azure SQL Server 表时出错。 

- **原因**：Cx SQL 表架构定义包含一个或多个长度小于预期值的列。

- **解决方法**：请尝试以下操作来解决此问题：

    1. 若要排查哪些行出现此问题，请应用 SQL 接收器[容错](./copy-activity-fault-tolerance.md)，尤其是“redirectIncompatibleRowSettings”）。

        > [!NOTE]
        > 容错可能需要额外的执行时间，这可能导致更高的成本。

    2. 根据 SQL 表架构列长度仔细检查重定向的数据，查看哪些列需要更新。

    3. 相应地更新表架构。

## <a name="error-code-faileddboperation"></a>错误代码：FailedDbOperation

- **消息**：`User does not have permission to perform this action.`

- **建议**：确保在使用 PolyBase 加载数据时，Azure Synapse Analytics 连接器中配置的用户必须对目标数据库具有“CONTROL”权限。 有关详细信息，请参阅此[文档](./connector-azure-sql-data-warehouse.md#required-database-permission)。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
