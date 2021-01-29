---
title: 排查 Azure 数据工厂连接器问题
description: 了解如何排查 Azure 数据工厂中的连接器问题。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: fdc4bbd463c45fecfc9e3961e42f81ed93d820ae
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054630"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨排查 Azure 数据工厂连接器问题的常见方法。
  
## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code-azurebloboperationfailed"></a>错误代码：AzureBlobOperationFailed

- **消息**： "Blob 操作失败。 容器容器：% 容器;，路径：% path;。 "

- **原因**： Blob 存储操作存在问题。

- **建议**：若要检查错误详细信息，请参阅 [Blob 存储错误代码](https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes)。 有关更多帮助，请联系 Blob 存储团队。


### <a name="invalid-property-during-copy-activity"></a>执行复制活动期间属性无效

- 消息：`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **原因**：数据集中定义的类型与复制活动中定义的源或接收器类型不一致。

- **解决方法**：编辑数据集或管道 JSON 定义，使类型一致，然后重新运行部署。


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求过大

- **症状**：将数据复制到具有默认写入批大小的 Azure Cosmos DB 时，会收到以下错误： `Request size is too large.`

- **原因**： Azure Cosmos DB 将单个请求的大小限制为 2 MB。 公式为 *请求大小 = 单文档大小 \* 写入批大小*。 如果文档大小太大，则默认行为会导致请求大小太大。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减少 " *写入批大小* " 值 (默认值为 10000) 。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**：将数据复制到 Azure Cosmos DB 时，会收到以下错误：

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **原因：** 有两个可能的原因：

    - 原因1：如果使用 **Insert** 作为写入行为，此错误意味着源数据包含具有相同 ID 的行或对象。
    - 原因2：如果使用 **Upsert** 作为写入行为，并将另一个唯一键设置到容器，此错误意味着源数据中的行或对象具有不同的 id，但定义的唯一键具有相同的值。

- **解决方法**： 

    - 对于原因1，将 **Upsert** 设置为写入行为。
    - 对于原因2，请确保每个文档的定义唯一键的值不同。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **症状**：将数据复制到 Azure Cosmos DB 时，会收到以下错误：

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **原因**：所使用的请求单位数 (ru) 大于在 Azure Cosmos DB 中配置的可用的 ru 数。 若要了解 Azure Cosmos DB 如何计算 ru，请参阅 [Azure Cosmos DB 中的请求单位](../cosmos-db/request-units.md#request-unit-considerations)。

- **解决方法**：尝试以下两个解决方案之一：

    - 将 *容器* 的 ru 数增加到 Azure Cosmos DB 中的更大值。 此解决方案将提高复制活动的性能，但在 Azure Cosmos DB 会导致更高的成本。 
    - 将 *writeBatchSize* 减少到较小的值（例如1000），并将 *parallelCopies* 减小为较小的值（例如1）。 此解决方案将降低复制运行的性能，但不会在 Azure Cosmos DB 中产生更高的成本。

### <a name="columns-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：导入用于列映射的 Azure Cosmos DB 的架构时，缺少某些列。 

- **原因**：数据工厂从前10个 Azure Cosmos DB 文档中推断架构。 如果某些文档列或属性不包含值，则数据工厂不会检测到架构，因此不会显示。

- **解决方法**：可以按下面的代码所示优化查询，以强制在结果集中显示值为空的列值。 假定) 的前10个文档中缺少 "不 *可能* " 列。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **症状**：从 Azure Cosmos DB MongoAPI 或 MongoDB 将数据复制 (UUID) 字段的全局唯一标识符时，会收到以下错误：

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **原因**：可以通过两种方法来表示二进制 JSON 中的 UUID (BSON) ： UuidStardard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会收到错误。

- **解决方法**：在 MongoDB 连接字符串中添加 *uuidRepresentation = standard* 选项。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>错误代码：CosmosDbSqlApiOperationFailed

- 消息：`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**： CosmosDbSqlApi 操作存在问题。

- **建议**：若要检查错误详细信息，请参阅 [Azure Cosmos DB 帮助文档](../cosmos-db/troubleshoot-dot-net-sdk.md)。 有关更多帮助，请联系 Azure Cosmos DB 团队。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>错误消息：基础连接已关闭：无法为 SSL/TLS 安全通道建立信任关系。

- **症状**：复制活动失败，出现以下错误： 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **原因**：在 TLS 握手期间，证书验证失败。

- **解决** 方法：作为一种解决方法，使用分段副本跳过 AZURE DATA LAKE STORAGE GEN1 (TLS) 验证的传输层安全性。 你需要重现此问题，并 (netmon 收集网络监视器) 跟踪，然后与网络团队合作来检查本地网络配置。

    ![用于解决问题的 Azure Data Lake Storage Gen1 连接的关系图。](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回了错误：(403)已禁止

- **症状**：复制活动失败，出现以下错误： 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **原因**：一个可能的原因是你使用的服务主体或托管标识没有访问某些文件夹或文件的权限。

- **解决方法**：向需要复制的所有文件夹和子文件夹授予适当的权限。 有关详细信息，请参阅 [使用 Azure 数据工厂将数据复制到 Azure Data Lake Storage Gen1 或从复制数据](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体来获取访问令牌。 ADAL 错误: service_unavailable

- **症状**：复制活动失败，出现以下错误：

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **原因**：当服务令牌服务器 (Azure Active Directory 所拥有的 STS) 不可用时，这意味着处理请求的情况太忙，并返回 HTTP 错误503。 

- **解决方法**：数分钟后重新运行复制活动。


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>错误代码：ADLSGen2OperationFailed

- 消息：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因**：如果 Azure Data Lake Storage Gen2 引发此错误，则操作已失败。

- **建议**：检查 Azure Data Lake Storage Gen2 引发的详细错误消息。 如果错误是暂时性故障，请重试该操作。 如需进一步的帮助，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

- **原因**：如果错误消息包含字符串 "禁止"，则你使用的服务主体或托管标识可能没有足够的权限访问 Azure Data Lake Storage Gen2。

- **建议**：若要解决此错误，请参阅 [使用 Azure 数据工厂复制和转换 Azure Data Lake Storage Gen2 中的数据](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication)。

- **原因**：如果错误消息中包含字符串 "InternalServerError"，Azure Data Lake Storage Gen2 将返回错误。

- **建议**：错误可能是由暂时性故障引起的。 如果是，请重试操作； 如果问题仍然存在，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>请求 Azure Data Lake Storage Gen2 帐户导致超时错误

- **消息**： 
  * 错误代码 = `UserErrorFailedBlobFSOperation`
  * 错误消息 = `BlobFS operation failed for: A task was canceled.`

- **原因**：此问题是由 Azure Data Lake Storage Gen2 接收器超时错误引起的，此错误通常发生在 (IR) 计算机上的自承载 Integration Runtime 上。

- **建议**： 

    - 如果可能，请将自承载 IR 计算机和目标 Azure Data Lake Storage Gen2 帐户置于同一区域。 这有助于避免随机超时错误，并获得更好的性能。

    - 检查是否有特殊的网络设置，如 ExpressRoute，并确保网络具有足够的带宽。 建议在总体带宽较低时降低自承载 IR 并发作业设置。 这样做有助于避免跨多个并发作业的网络资源竞争。

    - 如果文件大小为适中或较小，请使用较小的块大小非二进制复制来减少此类超时错误。 有关详细信息，请参阅 [Blob 存储 Put 块](/rest/api/storageservices/put-block)。

       若要指定自定义块大小，请在 JSON 文件编辑器中编辑属性，如下所示：
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure 文件存储

### <a name="error-code--azurefileoperationfailed"></a>错误代码：AzureFileOperationFailed

- 消息：`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**： Azure 文件存储操作存在问题。

- **建议**：若要查看错误详细信息，请参阅 [Azure 文件帮助](https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes)。 有关更多帮助，请联系 Azure 文件团队。


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics、Azure SQL 数据库和 SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>错误代码：SqlFailedToConnect

- 消息：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**：对于 Azure SQL，如果错误消息包含字符串 "SqlErrorNumber = 47073"，则表示在连接设置中拒绝公共网络访问。

- **建议**：在 Azure SQL 防火墙上，将 " **拒绝公共网络访问** " 选项设置为 " *否*"。 有关详细信息，请参阅 [AZURE SQL 连接设置](https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access)。

- **原因**：对于 azure sql，如果错误消息包含 SQL 错误代码，例如 "SqlErrorNumber = [errorcode]"，请参阅 Azure sql 故障排除指南。

- **建议**：有关建议，请参阅 [使用 azure Sql 数据库和 Azure sql 托管实例排查连接问题和其他错误](https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues)。

- **原因**：检查端口1433是否位于防火墙允许列表中。

- **建议**：有关详细信息，请参阅 [SQL Server 使用的端口](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-)。

- **原因**：如果错误消息包含字符串 "SqlException"，则 SQL 数据库错误表示某些特定操作失败。

- **建议**：有关详细信息，请按 " [数据库引擎错误](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors)" 中的 SQL 错误代码进行搜索。 有关更多帮助，请联系 Azure SQL 支持。

- **原因**：如果这是暂时性问题 (例如，instable 网络连接) ，请在活动策略中添加 "重试" 以减轻。

- **建议**：有关详细信息，请参阅 [Azure 数据工厂中的管道和活动](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy)。

- **原因**：如果错误消息包含字符串 "具有 IP 地址的客户端 ..."不允许访问服务器，并且你正在尝试连接到 Azure SQL 数据库，此错误通常是由 Azure SQL 数据库防火墙问题导致的。

- **建议**：在 Azure SQL Server 防火墙配置中，启用 " **允许 Azure 服务和资源访问此服务器** " 选项。 有关详细信息，请参阅 [AZURE SQL 数据库和 Azure SYNAPSE IP 防火墙规则](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)。


### <a name="error-code--sqloperationfailed"></a>错误代码：SqlOperationFailed

- 消息：`A database operation failed. Please search error to get more details.`

- **原因**：如果错误消息包含字符串 "SqlException"，则 SQL 数据库会引发一个错误，指示某些特定操作失败。

- **建议**：如果不清除 SQL 错误，请尝试将数据库更改为最新兼容级别 "150"。 它可能会引发最新版本的 SQL 错误。 有关详细信息，请参阅[本文档](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)。

    有关解决 SQL 问题的详细信息，请按 [数据库引擎错误](https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors)中的 SQL 错误代码进行搜索。 有关更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含字符串 "PdwManagedToNativeInteropException"，则通常是由于源列和接收器列大小不匹配造成的。

- **建议**：检查源列和接收器列的大小。 有关更多帮助，请联系 Azure SQL 支持。

- **原因**：如果错误消息包含字符串 "InvalidOperationException"，则通常是由于输入数据无效导致的。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 有关详细信息，请参阅 [Azure 数据工厂中复制活动的容错能力](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)。


### <a name="error-code--sqlunauthorizedaccess"></a>错误代码：SqlUnauthorizedAccess

- 消息：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：检查以确保登录帐户有足够的权限访问 SQL 数据库。


### <a name="error-code--sqlopenconnectiontimeout"></a>错误代码：SqlOpenConnectionTimeout

- 消息：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：此问题可能是 SQL 数据库暂时性失败。

- **建议**：重试该操作以更新连接超时值较大的链接服务连接字符串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>错误代码：SqlAutoCreateTableTypeMapFailed

- 消息：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：启用表无法满足源要求。

- **建议**：在 *映射* 中更新列类型，或在目标服务器中手动创建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>错误代码：SqlDataTypeNotSupported

- 消息：`A database operation failed. Check the SQL errors.`

- **原因**：如果 SQL 源中出现问题并且错误与 SqlDateTime 溢出相关，则数据值超出逻辑类型范围 (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 PM) 。

- **建议**：将类型转换为源 SQL 查询中的字符串，或在复制活动列映射中将列类型更改为 *字符串*。

- **原因**：如果 SQL 接收器上出现问题，并且错误与 SqlDateTime 溢出相关，则数据值超出了接收器表中允许的范围。

- **建议**：将相应的列类型更新为接收器表中的 *datetime2* 类型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>错误代码：SqlInvalidDbStoredProcedure

- 消息：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 这可能是因为存储过程没有返回任何数据。

- **建议**：使用 SQL 工具验证存储过程。 请确保存储过程可以返回数据。


### <a name="error-code--sqlinvaliddbquerystring"></a>错误代码：SqlInvalidDbQueryString

- 消息：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 原因可能是查询不返回任何数据。

- **建议**：使用 sql 工具验证 sql 查询。 请确保查询可以返回数据。


### <a name="error-code--sqlinvalidcolumnname"></a>错误代码：SqlInvalidColumnName

- 消息：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：找不到此列，因为配置可能不正确。

- **建议**：验证查询中的列、数据集中的 *结构* 和活动中的 *映射* 。


### <a name="error-code--sqlbatchwritetimeout"></a>错误代码：SqlBatchWriteTimeout

- 消息：`Timeouts in SQL write operation.`

- **原因**：此问题可能是由 SQL 数据库暂时性故障引起的。

- **建议**：重试该操作。 如果问题仍然存在，请联系 Azure SQL 支持。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>错误代码：SqlBatchWriteTransactionFailed

- 消息：`SQL transaction commits failed.`

- **原因**：如果异常详细信息不断指出事务超时，则集成运行时和数据库之间的网络延迟大于默认阈值30秒。

- **建议**：使用大于或等于120的 *连接超时* 值更新 SQL 链接服务连接字符串，然后重新运行该活动。

- **原因**：如果异常详细信息间歇指出 SQL 连接中断，则可能是暂时性的网络故障或 sql 数据库端问题。

- **建议**：重试活动，并查看 SQL 数据库端指标。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>错误代码：SqlBulkCopyInvalidColumnLength

- 消息：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**： SQL 大容量复制失败，因为从大容量复制程序实用工具接收到 (bcp) 客户端的列长度无效。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能。 这可以将有问题的行重定向到存储，以便进一步调查。 有关详细信息，请参阅 [Azure 数据工厂中复制活动的容错能力](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)。


### <a name="error-code--sqlconnectionisclosed"></a>错误代码：SqlConnectionIsClosed

- 消息：`The connection is closed by SQL Database.`

- **原因**： sql 数据库连接在高并发运行时关闭，服务器终止连接。

- **建议**：重试连接。 如果问题仍然存在，请联系 Azure SQL 支持。


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：将字符串转换为唯一标识符失败

- **症状**：在使用暂存复制和 PolyBase 将数据从表格格式数据源 (例如 SQL Server) 复制到 Azure Synapse Analytics 时，会收到以下错误：

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **原因**： Azure Synapse Analytics PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器的 "PolyBase 设置" 下，将 " **使用类型默认值** " 设置为 " *false*"。


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型：DECIMAL(x,x)，违规值

- **症状**：通过使用暂存复制和 PolyBase 将数据从表格格式数据源 (例如 SQL Server) 复制到 Azure Synapse Analytics 时，会收到以下错误：

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **原因**： Azure Synapse Analytics PolyBase 无法将空字符串 (null 值) 插入到十进制列中。

- **解决方法**：在复制活动接收器的 "PolyBase 设置" 下，将 " **使用类型默认值** " 设置为 "false"。


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息：HdfsBridge::CreateRecordReader

- **症状**：使用 PolyBase 将数据复制到 Azure Synapse Analytics，并收到以下错误：

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **原因**：原因可能是架构 (合计列宽度) 太大 (大于 1 MB) 。 通过添加所有列的大小来检查目标 Azure Synapse Analytics 表的架构：

    - Int = 4 字节
    - Bigint = 8 字节
    - Varchar (n) ，char (n) ，binary (n) ，varbinary (n) = n 字节
    - Nvarchar (n) ，nchar (n) = n * 2 字节
    - 日期 = 6 个字节
    - Datetime/ (2) ，smalldatetime = 16 字节
    - Datetimeoffset = 20 个字节
    - Decimal = 19 个字节
    - Float = 8 字节
    - Money = 8 字节
    - Smallmoney = 4 个字节
    - Real = 4 字节
    - Smallint = 2 字节
    - 时间 = 12 个字节
    - Tinyint = 1 字节

- **解决方法**： 
    - 将列宽缩小为小于 1 MB。
    - 或通过禁用 PolyBase 使用大容量插入方法。


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure Synapse Analytics 请求数据，并收到以下错误：

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **原因**：在 azure 存储中查询外部表时，Azure Synapse Analytics 遇到问题。

- **解决方法**：在 SQL Server Management Studio 中 (SSMS) 运行相同的查询，并查看是否获得相同的结果。 如果这样做，请向 Azure Synapse Analytics 提供支持票证，并提供 Azure Synapse Analytics 服务器和数据库名称。


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>性能层等级较低，导致复制失败

- **症状**：将数据复制到 Azure SQL 数据库并收到以下错误： `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**： Azure SQL 数据库 s1 (i/o) 限制的输入/输出。

- **解决方法**：升级 Azure SQL 数据库性能层可解决此问题。 


### <a name="sql-table-cant-be-found"></a>找不到 SQL 表 

- **症状**：将数据从混合复制到本地 SQL Server 表，并收到以下错误：`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**：当前 SQL 帐户没有足够的权限，无法执行由 .Net SqlBulkCopy WriteToServer 发出的请求。

- **解决方法**：切换到权限更高的一个 SQL 帐户。


### <a name="error-message-string-or-binary-data-is-truncated"></a>错误消息：字符串或二进制数据被截断

- **症状**：将数据复制到本地 Azure SQL Server 表时出现错误。 

- **原因**： Cx SQL 表架构定义中的一个或多个列的长度比预期的长。

- **解决方法**：若要解决此问题，请尝试以下操作：

    1. 若要解决哪些行有问题，请应用 SQL 接收器 [容错](./copy-activity-fault-tolerance.md)，特别是 "redirectIncompatibleRowSettings"。

        > [!NOTE]
        > 容错可能需要额外的执行时间，这可能会导致更高的成本。

    2. 仔细检查重定向的数据和 SQL 表架构列长度，以查看需要更新的列。

    3. 请相应地更新表架构。


## <a name="azure-table-storage"></a>Azure 表存储

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>错误代码：AzureTableDuplicateColumnsFromSource

- 消息：`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **原因**：重复的源列可能由以下原因之一导致：
   * 使用数据库作为源，并已应用表联接。
   * 在标头行中具有重复列名称的非结构化 CSV 文件。

- **建议**：根据需要仔细检查并修复源列。


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>错误代码：DB2DriverRunFailed

- 消息：`Error thrown from driver. Sql code: '%code;'`

- **原因**：如果错误消息包含字符串 "SQLSTATE = 51002 SQLCODE =-805"，请遵循 [使用 Azure 数据工厂从 DB2 复制数据](https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties)中的 "提示"。

- **建议**：尝试在属性中设置 "NULLID" `packageCollection`  。


## <a name="delimited-text-format"></a>带分隔符的文本格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>错误代码：DelimitedTextColumnNameNotAllowNull

- 消息：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活动中设置 "firstRowAsHeader" 时，第一行用作列名称。 此错误表示第一行包含空值 (例如 "ColumnA，ColumnB" ) 。

- **建议**：检查第一行，如果该值为空，则修复它。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>错误代码：DelimitedTextMoreColumnsThanDefined

- 消息：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因：** 有问题的行的列计数大于第一行的列计数。 这可能是由数据问题或不正确的列分隔符或引号字符设置导致的。

- **建议**：从错误消息中获取行计数，检查行的列，然后修复数据。

- **原因**：如果错误消息中所需的列计数为 "1"，则可能是指定了错误的压缩或格式设置，这会导致数据工厂错误地分析文件。

- **建议**：检查格式设置，确保它们与源文件匹配。

- **原因**：如果源是文件夹，则指定文件夹下的文件可能具有不同的架构。

- **建议**：确保指定文件夹中的文件具有相同的架构。


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365、Common Data Service 和 Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>错误代码：DynamicsCreateServiceClientError

- 消息：`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **原因**：此问题是 Dynamics 服务器端的暂时性问题。

- **建议**：重新运行管道。 如果再次失败，请尝试降低并行度。 如果问题仍然存在，请联系 Dynamics 支持部门。


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>导入架构或预览数据时缺少列

- **症状**：导入架构或预览数据时，缺少某些列。 错误消息：`The valid structure information (column name and type) are required for Dynamics source.`

- **原因**：此问题是设计使然，因为数据工厂无法显示前10条记录中不包含任何值的列。 请确保已添加的列的格式正确。 

- **建议**：手动在 "映射" 选项卡中添加列。


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>错误代码：DynamicsMissingTargetForMultiTargetLookupField

- 消息：`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**：目标列在源中或列映射中不存在。

- **建议**：  
  1. 请确保源包含目标列。 
  2. 在列映射中添加目标列。 确保接收器列的格式为 *{fieldName} @EntityReference*。


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>错误代码：DynamicsInvalidTargetForMultiTargetLookupField

- 消息：`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **原因：** 提供了错误的实体名称作为多目标查找字段的目标实体。

- **建议**：为多目标查找字段提供有效的实体名称。


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>错误代码：DynamicsInvalidTypeForMultiTargetLookupField

- 消息：`The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**：目标列中的值不是字符串。

- **建议**：在“多目标查找目标”列中提供有效字符串。


### <a name="error-code--dynamicsfailedtorequetserver"></a>错误代码：DynamicsFailedToRequetServer

- 消息：`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **原因**： Dynamics 服务器 instable 或无法访问，或网络遇到问题。

- **建议**：有关更多详细信息，请检查网络连接或检查 Dynamics 服务器日志。 有关更多帮助，请联系 Dynamics 支持部门。
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>错误代码：FtpFailedToConnectToFtpServer

- 消息：`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **原因**：不正确的链接服务类型可能用于 FTP 服务器，如使用安全 FTP (SFTP) 链接服务连接到 FTP 服务器。

- **建议**：检查目标服务器的端口。 FTP 使用端口21。


## <a name="http"></a>HTTP

### <a name="error-code--httpfilefailedtoread"></a>错误代码：HttpFileFailedToRead

- 消息：`Failed to read data from http server. Check the error from http server：%message;`

- **原因**：当 Azure 数据工厂与 http 服务器通信时，如果 http 请求操作失败，则会出现此错误。

- **建议**：检查错误消息中的 HTTP 状态代码，并修复远程服务器问题。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>错误代码：ArgumentOutOfRangeException

- 消息：`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**：在数据工厂中，支持从 0001-01-01 00:00:00 到 9999-12-31 23:59:59 的范围内的日期时间值。 但是，Oracle 支持范围更广的日期时间值，例如 BC 世纪或 min/sec>59，这会导致数据工厂出错。

- **建议**： 

    若要查看 Oracle 中的值是否在数据工厂范围内，请运行 `select dump(<column name>)` 。 

    若要了解结果中的字节序列，请参阅 [如何在 Oracle 中存储日期？](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)。


## <a name="orc-format"></a>ORC 格式

### <a name="error-code--orcjavainvocationexception"></a>错误代码：OrcJavaInvocationException

- 消息：`An error occurred when invoking Java, message: %javaException;.`

- **原因**：如果错误消息中包含字符串 "OutOfMemory"、"java 堆空间" 和 "doubleCapacity"，则这通常是早期版本的 integration runtime 中的内存管理问题。

- **建议**：如果使用自承载 Integration Runtime，则建议升级到最新版本。

- **原因**：当错误消息包含字符串 "OutOfMemory" 时，集成运行时没有足够的资源来处理这些文件。

- **建议**：限制集成运行时中的并发运行。 对于自承载 IR，增加到内存等于或大于 8 GB 的强大计算机。

- **原因**：当错误消息包含字符串 "NullPointerReference" 时，原因可能是暂时性错误。

- **建议**：重试该操作。 如果问题仍然存在，请联系支持部门。

- **原因**：当错误消息包含字符串 "BufferOverflowException" 时，原因可能是暂时性错误。

- **建议**：重试该操作。 如果问题仍然存在，请联系支持部门。

- **原因**：如果错误消息包含字符串 "ClassCastException:org 不能转换为 org. org.apache.hadoop.hive.serde2.columnar.lazybinarycolumnarserde"，则原因可能是 Java 运行时中的类型转换问题导致的。）可能是 Java 运行时中的类型转换问题。 通常，这意味着在 Java 运行时中无法正常处理源数据。

- **建议**：这是一个数据问题。 尝试在 ORC 格式的数据中使用字符串而不是 char 或 varchar。

### <a name="error-code--orcdatetimeexceedlimit"></a>错误代码：OrcDateTimeExceedLimit

- 消息：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**：如果 datetime 值为 "0001-01-01 00:00:00"，则可能是由 [儒略历和公历](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)之间的差异导致的。

- **建议**：检查时钟周期值，并避免使用日期/时间值“0001-01-01 00:00:00”。


## <a name="parquet-format"></a>Parquet 格式

### <a name="error-code--parquetjavainvocationexception"></a>错误代码：ParquetJavaInvocationException

- 消息：`An error occurred when invoking java, message: %javaException;.`

- **原因**：如果错误消息包含字符串 "OutOfMemory"、"java 堆空间" 和 "doubleCapacity"，则这通常是旧版本的 Integration Runtime 中的内存管理问题。

- **建议**：如果使用自承载 IR 并且版本早于3.20.7159.1，则建议升级到最新版本。

- **原因**：当错误消息包含字符串 "OutOfMemory" 时，集成运行时没有足够的资源来处理这些文件。

- **建议**：限制集成运行时中的并发运行。 对于自承载 IR，增加到具有等于或大于 8 GB 的内存的强大计算机。

- **原因**：如果错误消息包含字符串 "NullPointerReference"，则可能是暂时性错误。

- **建议**：重试该操作。 如果问题仍然存在，请联系支持部门。


### <a name="error-code--parquetinvalidfile"></a>错误代码：ParquetInvalidFile

- 消息：`File is not a valid Parquet file.`

- **原因**：这是一个 Parquet 的文件问题。

- **建议**：检查输入是否为有效的 Parquet 文件。


### <a name="error-code--parquetnotsupportedtype"></a>错误代码：ParquetNotSupportedType

- 消息：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因：** Azure 数据工厂不支持 Parquet 格式。

- **建议**：通过 [Azure 数据工厂中的复制活动转到支持的文件格式和压缩编解码器，](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs)来仔细检查源数据。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>错误代码：ParquetMissedDecimalPrecisionScale

- 消息：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：分析了数字的精度和小数位数，但未提供此类信息。

- **建议**：源不会返回正确的精度和小数位数信息。 有关信息，请查看问题列。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>错误代码：ParquetInvalidDecimalPrecisionScale

- 消息：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因：** 架构无效。

- **建议**：检查问题列的精度和小数位数。


### <a name="error-code--parquetcolumnnotfound"></a>错误代码：ParquetColumnNotFound

- 消息：`Column %column; does not exist in Parquet file.`

- **原因**：源架构与接收器架构不匹配。

- **建议**：检查活动中的映射。 请确保源列可以映射到正确的接收器列。


### <a name="error-code--parquetinvaliddataformat"></a>错误代码：ParquetInvalidDataFormat

- 消息：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：无法将数据转换为在 "映射源" 中指定的类型。

- **建议**：仔细检查源数据或在复制活动列映射中为此列指定正确的数据类型。 有关详细信息，请参阅 [Azure 数据工厂中的复制活动支持的文件格式和压缩编解码器](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs)。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>错误代码：ParquetDataCountNotMatchColumnCount

- 消息：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列计数和接收器列计数不匹配。

- **建议**：仔细检查以确保源列计数与 "映射" 中的接收器列计数相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>错误代码：ParquetDataTypeNotMatchColumnType

- **消息**：`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **原因**：源中的数据无法转换为接收器中定义的类型。

- **建议**：在 mapping.sink 中指定正确的类型。


### <a name="error-code--parquetbridgeinvaliddata"></a>错误代码：ParquetBridgeInvalidData

- 消息：`%message;`

- **原因**：数据值超出了限制。

- **建议**：重试该操作。 如果问题仍然存在，请联系我们。


### <a name="error-code--parquetunsupportedinterpretation"></a>错误代码：ParquetUnsupportedInterpretation

- 消息：`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**：不支持此方案。

- **建议**：“ParquetInterpretFor”不应为“sparkSql”。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>错误代码：ParquetUnsupportFileLevelCompressionOption

- 消息：`File level compression is not supported for Parquet.`

- **原因**：不支持此方案。

- **建议**：在负载中删除 "CompressionType"。


### <a name="error-code--usererrorjniexception"></a>错误代码：UserErrorJniException

- 消息：`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**：无法创建 JAVA 虚拟机 (JVM) ，因为已设置某些非法 (全局) 参数。

- **建议**：登录到托管自承载 IR 的 *每个节点* 的计算机。 检查以确保正确设置系统变量，如下所示： `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` 。 重新启动所有 IR 节点，然后重新运行该管道。


### <a name="arithmetic-overflow"></a>算术溢出

- **症状**：复制 Parquet 文件时出现错误消息： `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**：将文件从 Oracle 复制到 Parquet 时，当前仅支持精度 <= 38 和整数部分的长度 <= 20。 

- **解决** 方法：作为一种解决方法，可以将出现此问题的所有列转换为 VARCHAR2。


### <a name="no-enum-constant"></a>无枚举常量

- **症状**：将数据复制到 Parquet 格式时出现错误消息： `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 或： `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` 。

- **原因**： 

    此问题可能是由空格或不受支持的特殊字符引起的，如 (， {} 列名称中 ( # A2\n\t =) ，因为 Parquet 不支持此类格式。 

    例如，诸如 *contoso (测试)* 的列名称将从 [代码](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java)中的括号内分析该类型 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` 。 由于没有这样的 "测试" 类型，因此会引发错误。

    若要检查支持的类型，请参阅 GitHub [apache/parquet-mr 站点](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)。

- **解决方法**： 

    仔细检查以确定：
    - 接收器列名称中有空格。
    - 带有空格的第一行用作列名称。
    - 支持类型 OriginalType。 尝试避免使用这些特殊字符： `,;{}()\n\t=` 。 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>错误代码：RestSinkCallFailed

- 消息：`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**：当 Azure 数据工厂通过 HTTP 协议与 REST 终结点通信，并且请求操作失败时，会出现此错误。

- **建议**：检查 HTTP 状态代码或错误消息中的消息，并解决远程服务器问题。

### <a name="unexpected-network-response-from-the-rest-connector"></a>来自 REST 连接器的意外网络响应

- **症状**：终结点有时会收到来自 REST 连接器 (400、401、403、500) 的意外响应。

- **原因**： REST 源连接器在构造 HTTP 请求时使用链接的服务/数据集/复制源中的 URL 和 HTTP 方法/标头/正文作为参数。 此问题很可能是由一个或多个指定参数中的某些错误引起的。

- **解决方法**： 
    - 在命令提示符窗口中使用 "卷" 可以查看参数是 (**接受** 的原因，并且应始终包括 **用户代理** 标头) ：
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      如果该命令返回相同的意外响应，请在返回预期响应之前，先将前面的参数替换为 "卷曲"。 

      还可以使用 "卷--帮助" 来更高级地使用该命令。

    - 如果只有数据工厂 REST 连接器返回意外响应，请联系 Microsoft 支持部门以进一步进行故障排除。
    
    - 请注意，"卷" 可能不适合重现 SSL 证书验证问题。 在某些情况下，已成功执行 "卷" 命令，而不会遇到任何 SSL 证书验证问题。 但在浏览器中执行相同的 URL 时，实际上不会为客户端返回与服务器建立信任关系的 SSL 证书。

      建议将 **Postman** 和 **Fiddler** 等工具用于前面的示例。


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>错误代码：SftpOperationFail

- 消息：`Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**： SFTP 操作出现问题。

- **建议**：检查 SFTP 中的错误详细信息。


### <a name="error-code--sftprenameoperationfail"></a>错误代码：SftpRenameOperationFail

- 消息：`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**： SFTP 服务器不支持重命名临时文件。

- **建议**：将 "useTempFileRename" 设置为复制接收器中的 false，以禁止上传到临时文件。


### <a name="error-code--sftpinvalidsftpcredential"></a>错误代码：SftpInvalidSftpCredential

- 消息：`Invalid SFTP credential provided for '%type;' authentication type.`

- **原因**：私钥内容从 Azure 密钥保管库或 SDK 提取，但未正确编码。

- **建议**：  

    如果私钥内容来自你的密钥保管库，则在将其直接上传到 SFTP 链接服务时，原始密钥文件可以工作。

    有关详细信息，请参阅 [使用 Azure 数据工厂将数据从复制到 SFTP 服务器](https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication)。 私钥内容是 base64 编码的 SSH 私钥内容。

    用 base64 编码对 *整个* 原始私钥文件进行编码，并将编码的字符串存储在密钥保管库中。 如果从文件中选择 " **上传** "，则可以在 SFTP 链接服务上使用原来的私钥文件。

    下面是一些可以用来生成字符串的示例：

    - 使用 C# 代码：

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - 使用 Python 代码：

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - 使用第三方 base64 转换工具。 建议 [编码为 Base64 格式](https://www.base64encode.org/) 的工具。

- **原因**：选择了错误的密钥内容格式。

- **建议**：  

    PKCS # 8 格式 SSH 私钥 (以 "-----开始加密私钥-----) " 开头，当前不支持访问数据工厂中的 SFTP 服务器。 

    若要将密钥转换为传统 SSH 密钥格式，请从 "-----BEGIN RSA PRIVATE KEY-----" 开始，运行以下命令：

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**：凭据或私钥内容无效。

- **建议**：若要查看密钥文件或密码是否正确，请仔细检查工具（如 WinSCP）。

### <a name="sftp-copy-activity-failed"></a>SFTP 复制活动失败

- **症状**： 
  * 错误代码： UserErrorInvalidColumnMappingColumnNotFound 
  * 错误消息：`Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **原因**：源不包含名为 "AccMngr" 的列。

- **解决方法**：若要确定是否存在 "AccMngr" 列，请通过映射目标数据集列来仔细检查数据集配置。


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>错误代码：SftpFailedToConnectToSftpServer

- 消息：`Failed to connect to SFTP server '%server;'.`

- **原因**：如果错误消息包含字符串 "套接字读取操作在30000毫秒后超时"，则可能是因为 SFTP 服务器使用了错误的链接服务类型。 例如，你可能使用 FTP 链接服务连接到 SFTP 服务器。

- **建议**：检查目标服务器的端口。 默认情况下，SFTP 使用端口22。

- **原因**：如果错误消息包含字符串 "服务器响应不包含 SSH 协议标识"，则一个可能的原因是 SFTP 服务器已阻止连接。 数据工厂会创建多个连接以从 SFTP 服务器并行下载，有时会遇到 SFTP 服务器限制。 通常，不同的服务器在遇到限制时会返回不同的错误。

- **建议**：  

    将 SFTP 数据集的最大并发连接数指定为1，然后重新运行复制活动。 如果活动成功，可以确保限制是原因。

    如果要提升低吞吐量，请与 SFTP 管理员联系以增加并发连接计数限制，或者可以执行以下操作之一：

    * 如果使用自承载 IR，请将自承载 IR 计算机的 IP 添加到允许列表中。
    * 如果使用 Azure IR，请添加 [Azure Integration Runtime 的 IP 地址](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)。 如果不想向 SFTP 服务器允许列表添加一系列 Ip，请改用自承载 IR。

## <a name="sharepoint-online-list"></a>SharePoint Online 列表

### <a name="error-code--sharepointonlineauthfailed"></a>错误代码：SharePointOnlineAuthFailed

- 消息：`The access token generated failed, status code: %code;, error message: %message;.`

- **原因**：服务主体 ID 和密钥可能未正确设置。

- **建议**：检查已注册的应用程序 (服务主体 ID) 和键，以查看它们是否设置正确。


## <a name="xml-format"></a>XML 格式

### <a name="error-code--xmlsinknotsupported"></a>错误代码：XmlSinkNotSupported

- 消息：`Write data in XML format is not supported yet, choose a different format!`

- **原因**： XML 数据集用作复制活动中的接收器数据集。

- **建议**：使用不同于接收器数据集的格式的数据集。


### <a name="error-code--xmlattributecolumnnameconflict"></a>错误代码：XmlAttributeColumnNameConflict

- 消息：`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**：使用了属性前缀，导致了冲突。

- **建议**：为 "attributePrefix" 属性设置不同的值。


### <a name="error-code--xmlvaluecolumnnameconflict"></a>错误代码：XmlValueColumnNameConflict

- 消息：`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**：其中一个子元素名称用作元素值的列名称。

- **建议**：为 "valueColumn" 属性设置不同的值。


### <a name="error-code--xmlinvalid"></a>错误代码：XmlInvalid

- 消息：`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**：输入 XML 文件的格式不正确。

- **建议**：更正 XML 文件，使其格式正确。


## <a name="general-copy-activity-error"></a>常规复制活动错误

### <a name="error-code--jrenotfound"></a>错误代码：JreNotFound

- 消息：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**：自承载 IR 找不到 Java 运行时。 读取特定源时需要 Java 运行时。

- **建议**：检查集成运行时环境，请参阅 [使用自承载 Integration Runtime](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)。


### <a name="error-code--wildcardpathsinknotsupported"></a>错误代码：WildcardPathSinkNotSupported

- 消息：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**：接收器数据集不支持通配符值。

- **建议**：检查接收器数据集，并在不使用通配符值的情况下重写路径。


### <a name="fips-issue"></a>FIPS 问题

- **症状**：复制活动在启用 FIPS 的自托管 IR 计算机上失败，并出现以下错误消息： `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **原因**：将数据复制到 Azure BLOB、SFTP 等连接器时可能会发生此错误。 联邦信息处理标准 (FIPS) 定义允许使用的一组特定加密算法。 当计算机上启用了 FIPS 模式时，某些情况下会阻止复制活动所依赖的某些加密类。

- **解决方法**：了解 [为什么我们不再建议 "FIPS 模式"](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)，并评估是否可以在自承载 IR 计算机上禁用 fips。

    或者，如果只想让 Azure 数据工厂绕过 FIPS 并使活动运行成功，则执行以下操作：

    1. 打开安装自承载 IR 的文件夹。 路径通常是 *C:\Program Files\Microsoft Integration Runtime \<IR version> \Shared*。

    2. 打开 *diawp.exe.config* 文件，然后在部分的末尾 `<runtime>` 添加 `<enforceFIPSPolicy enabled="false"/>` ，如下所示：

        ![显示禁用 FIPS 的 diawp.exe.config 文件部分的屏幕截图。](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. 保存该文件，然后重新启动自承载 IR 计算机。


## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&页面](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
