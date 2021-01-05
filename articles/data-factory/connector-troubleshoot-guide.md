---
title: 排查 Azure 数据工厂连接器问题
description: 了解如何排查 Azure 数据工厂中的连接器问题。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: e6591762ed6a7e2b462a209730276f3198d86ae8
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821462"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>排查 Azure 数据工厂连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了常见的 Azure 数据工厂连接器故障排除方法。
  
## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code-azurebloboperationfailed"></a>错误代码： AzureBlobOperationFailed

- 消息：`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **原因：** Blob 存储操作遇到问题。

- **建议**：检查详细信息中的错误。 参阅 Blob 帮助文档： https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes 。 如需帮助，请联系存储团队。


### <a name="invalid-property-during-copy-activity"></a>复制活动期间属性无效

- 消息：`Copy activity <Activity Name> has an invalid "source" property. The source type is not compatible with the dataset <Dataset Name> and its linked service <Linked Service Name>. Please verify your input against.`

- **原因**：数据集中定义的类型与复制活动中定义的源/接收器类型不一致。

- **解决方法**：编辑数据集或管道 JSON 定义，使类型一致并重新运行部署。


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求过大

- **症状**：将数据复制到使用默认写入批大小的 Azure Cosmos DB 时，遇到错误“请求过大”。

- **原因：** Cosmos DB 将单个请求的大小限制为 2 MB。 公式为请求大小 = 单个文档大小 * 写入批大小。 如果文档过大，默认行为会导致请求过大。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减小“写入批大小”值（默认值为 10000）。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **原因：** 有两个可能的原因：

    - 如果使用“插入”作为写入行为，则此错误表示源数据包含具有相同 ID 的行/对象。
    - 如果使用“更新插入”作为写入行为，并设置了容器的另一个唯一键，则此错误表示源数据中的行/对象使用了定义的唯一键的不同 ID，但使用了该键的相同值。

- **解决方法**： 

    - 对于原因 1，请将“更新插入”设置为写入行为。
    - 对于原因 2，请确保每个文档使用定义的唯一键的不同值。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **症状**：将数据复制到 Cosmos DB 时遇到以下错误：

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **原因：** 使用的请求单位大于 Cosmos DB 中配置的可用 RU。 在[此处](../cosmos-db/request-units.md#request-unit-considerations)了解 Cosmos DB 如何计算 RU。

- **解决方法**：下面是两种解决方法：

    - **增加容器 RU**，使之大于 Cosmos DB 中的值，这可以提高复制活动的性能，不过会增大 Cosmos DB 的费用。 
    - 将 **writeBatchSize** 减至更小的值（例如 1000），并将 **parallelCopies** 设置为更小的值（例如 1），这会导致复制运行性能比当前更糟，但不会增大 Cosmos DB 的费用。

### <a name="column-missing-in-column-mapping"></a>列映射中缺少列

- **症状**：导入用于列映射的 Cosmos DB 的架构时缺少某些列。 

- **原因：** ADF 从前 10 个 Cosmos DB 文档推断架构。 如果某些列/属性在这些文档中没有值，则它们不会被 ADF 检测到，因此也就不会显示。

- **解决方法**：可按如下所示优化查询，以强制在结果集中显示带有空值的列：（假设前 10 个文档中缺少“impossible”列）。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **症状**：从包含 UUID 字段的 Cosmos DB MongoAPI/MongoDB 复制数据时遇到以下错误：

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **原因：** 可通过两种方式表示 BSON 中的 UUID - UuidStardard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会出现错误。

- **解决方法**：在 MongoDB 连接字符串中，添加选项 "**uuidRepresentation=standard**"。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code--cosmosdbsqlapioperationfailed"></a>错误代码： CosmosDbSqlApiOperationFailed

- 消息：`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**： CosmosDbSqlApi 操作遇到问题。

- **建议**：检查详细信息中的错误。 请参阅 [CosmosDb 帮助文档](https://docs.microsoft.com/azure/cosmos-db/troubleshoot-dot-net-sdk)。 如果需要帮助，请联系 CosmosDb 团队。


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>错误消息：基础连接已关闭：无法为 SSL/TLS 安全通道建立信任关系。

- **症状**：复制活动失败，出现以下错误： 

    ```
    Message: ErrorCode = `UserErrorFailedFileOperation`, Error Message = `The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`.
    ```

- **原因**：在 TLS 握手期间证书验证失败。

- **解决** 方法：解决方法：使用暂存复制跳过 ADLS GEN1 的 TLS 验证。 你需要重现此问题并收集 netmon 跟踪，然后与网络团队合作来检查本地网络配置。

    ![排查 ADLS Gen1](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回了错误：(403)已禁止

- **症状**：复制活动失败，出现以下错误： 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **原因**：一个可能原因是，你使用的服务主体或托管标识无权访问某个文件夹/文件。

- **解决方法**：在需要复制的所有文件夹和子文件夹上授予相应权限。 请参阅[这篇文档](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体来获取访问令牌。 ADAL 错误: service_unavailable

- **症状**：复制活动失败，出现以下错误：

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **原因**：如果 Azure Active Directory 拥有的 Service Token Server (STS) 不可用（即太忙而无法处理请求），则会返回 HTTP 错误 503。 

- **解决方法**：数分钟后重新运行复制活动。


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>错误代码： ADLSGen2OperationFailed

- 消息：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因：** ADLS Gen2 引发了指明操作失败的错误。

- **建议**：检查 ADLS Gen2 引发的详细错误消息。 如果这是由暂时性故障导致的，请重试。 如果需要进一步的帮助，请联系 Azure 存储支持，并提供错误消息中的请求 ID。

- **原因：** 如果错误消息包含“被禁止”，则你使用的服务主体或托管标识可能没有足够的权限来访问 ADLS Gen2。

- **建议**：参阅帮助文档： https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication 。

- **原因：** 当错误消息包含“InternalServerError”时，错误是由 ADLS Gen2 返回的。

- **建议**：这可能是由暂时性失败导致的，请重试。 如果此问题仍然存在，请联系 Azure 存储支持部门，并提供错误消息中的请求 ID。

### <a name="request-to-adls-gen2-account-met-timeout-error"></a>请求 ADLS Gen2 帐户满足超时错误

- **消息**：错误代码 = `UserErrorFailedBlobFSOperation` ，错误消息 = `BlobFS operation failed for: A task was canceled` 。

- **原因**：此问题是由在自承载 IR 计算机上经常发生的 ADLS Gen2 接收器超时错误引起的。

- **建议**： 

    - 如果可能，请将自承载 IR 计算机和目标 ADLS Gen2 帐户置于同一区域。 这可以避免随机超时错误，并获得更好的性能。

    - 检查是否有任何特殊的网络设置（如 ExpressRoute），并确保网络具有足够的带宽。 建议在总体带宽较低时降低自承载 IR 并发作业设置，这样可以避免跨多个并发作业的网络资源争用。

    - 如果文件大小适中或较小，请使用较小的块大小（对于非二进制副本）来减少此类超时错误。 请参阅 [Blob 存储 Put 块](https://docs.microsoft.com/rest/api/storageservices/put-block)。

       若要指定自定义块大小，可以在 "json 编辑器" 中编辑属性：
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-file-storage"></a>Azure 文件存储

### <a name="error-code--azurefileoperationfailed"></a>错误代码： AzureFileOperationFailed

- 消息：`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**： Azure 文件存储操作遇到问题。

- **建议**：检查详细信息中的错误。 请参阅 Azure 文件帮助文档： https://docs.microsoft.com/rest/api/storageservices/file-service-error-codes 。 如果需要帮助，请联系存储团队。


## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure Synapse Analytics/Azure SQL Database/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>错误代码：SqlFailedToConnect

- 消息：`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **原因**： Azure SQL：如果错误消息包含 "SqlErrorNumber = 47073"，则表示在连接设置中拒绝公共网络访问。

- **建议**：在 Azure SQL 防火墙上，将 "拒绝公共网络访问" 选项设置为 "否"。 了解详细信息 https://docs.microsoft.com/azure/azure-sql/database/connectivity-settings#deny-public-network-access 。

- **原因**： Azure Sql：如果错误消息包含 SQL 错误代码，如 "SqlErrorNumber = [errorcode]"，请参阅 Azure sql 故障排除指南。

- **建议**：了解更多 https://docs.microsoft.com/azure/azure-sql/database/troubleshoot-common-errors-issues 。

- **原因**：检查端口1433是否在防火墙允许列表中。

- **建议**：遵循此参考文档： https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by- 。

- **原因：** 如果错误消息包含“SqlException”，则 SQL 数据库将引发错误，指示某个特定操作失败。

- **建议**：如需了解更多详细信息，请按 SQL 错误代码在以下参考文档中进行搜索： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如需进一步的帮助，请联系 Azure SQL 支持。

- **原因**：如果这是暂时性问题 (例如，instable 网络连接) ，请在活动策略中添加 "重试" 以减轻。

- **建议**：按照此参考文档进行操作： https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities#activity-policy 。

- **原因**：如果错误消息包含“不允许 IP 地址为 '...' 的客户端访问服务器”，并且你正在尝试连接到 Azure SQL 数据库，表明这通常是由于 Azure SQL 数据库防火墙问题所致。

- **建议**：在 Azure SQL Server 防火墙配置中，启用“允许 Azure 服务和资源访问此服务器”选项。 参考文档： https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure 。


### <a name="error-code--sqloperationfailed"></a>错误代码：SqlOperationFailed

- 消息：`A database operation failed. Please search error to get more details.`

- **原因：** 如果错误消息包含“SqlException”，则 SQL 数据库将引发错误，指示某个特定操作失败。

- **建议**：如果 SQL 错误不明确，请尝试将数据库更改为最新兼容级别“150”。 它可能会引发最新版本 SQL 错误。 请参阅[详细信息文档](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)。

    若要排查 SQL 错误，请根据 SQL 错误代码在此参考文档中搜索以了解更多详细信息： https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors 。 如需进一步的帮助，请联系 Azure SQL 支持。

- **原因：** 如果错误消息包含“PdwManagedToNativeInteropException”，这通常是由于源列和接收器列大小不匹配导致的。

- **建议**：检查源列和接收器列的大小。 如需进一步的帮助，请联系 Azure SQL 支持。

- **原因：** 如果错误消息包含“InvalidOperationException”，这通常是由于输入数据无效导致的。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlunauthorizedaccess"></a>错误代码：SqlUnauthorizedAccess

- 消息：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因：** 凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：检查登录帐户是否有足够的权限来访问 SQL 数据库。


### <a name="error-code--sqlopenconnectiontimeout"></a>错误代码：SqlOpenConnectionTimeout

- 消息：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因：** 可能是 SQL 数据库暂时性失败。

- **建议**：重试，以使用更大的连接超时值来更新链接服务连接字符串。


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>错误代码：SqlAutoCreateTableTypeMapFailed

- 消息：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因：** 自动创建表不能满足源要求。

- **建议**：更新“mappings”中的列类型，或者在目标服务器中手动创建接收器表。


### <a name="error-code--sqldatatypenotsupported"></a>错误代码：SqlDataTypeNotSupported

- 消息：`A database operation failed. Check the SQL errors.`

- **原因：** 如果在 SQL 源上出现问题，并且错误与 SqlDateTime 溢出有关，则数据值将超出逻辑类型范围 (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM)。

- **建议**：在源 SQL 查询中将类型转换为字符串，或在复制活动列映射中将列类型更改为“String”。

- **原因：** 如果在 SQL 接收器上出现问题，并且错误与 SqlDateTime 溢出有关，则数据值将超出接收器表中的允许范围。

- **建议**：在接收器表中将相应的列类型更新为“datetime2”类型。


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>错误代码：SqlInvalidDbStoredProcedure

- 消息：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因：** 指定的存储过程无效。 这可能是因为存储过程不返回任何数据。

- **建议**：通过 SQL 工具验证存储过程。 请确保存储过程可以返回数据。


### <a name="error-code--sqlinvaliddbquerystring"></a>错误代码：SqlInvalidDbQueryString

- 消息：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因：** 指定的 SQL 查询无效。 这可能是因为查询不返回任何数据。

- **建议**：通过 SQL 工具验证 SQL 查询。 请确保查询可以返回数据。


### <a name="error-code--sqlinvalidcolumnname"></a>错误代码：SqlInvalidColumnName

- 消息：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因：** 找不到列。 可能存在配置错误。

- **建议**：验证查询中的列、数据集中的“结构”和活动中的“映射”。


### <a name="error-code--sqlbatchwritetimeout"></a>错误代码：SqlBatchWriteTimeout

- 消息：`Timeouts in SQL write operation.`

- **原因：** 可能是 SQL 数据库暂时性失败。

- **建议**：重试。 如果问题重现，请联系 Azure SQL 支持人员。


### <a name="error-code--sqlbatchwritetransactionfailed"></a>错误代码：SqlBatchWriteTransactionFailed

- 消息：`SQL transaction commits failed`

- **原因：** 如果异常详细信息持续指出事务超时，则表示集成运行时与数据库之间的网络延迟高于默认阈值（30 秒）。

- **建议**：使用 120 或更大的“连接超时”值更新 SQL 链接服务连接字符串更新，然后重新运行活动。

- **原因：** 如果异常详细信息间歇性指出 sqlconnection 中断，则原因可能只是与暂时性网络故障或 SQL 数据库端的问题有关

- **建议**：重试活动，并审阅 SQL 数据库端指标。


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>错误代码：SqlBulkCopyInvalidColumnLength

- 消息：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因：** 由于从 BCP 客户端接收到无效的列长度，SQL 批量复制失败。

- **建议**：若要确定哪个行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 参考文档： https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance 。


### <a name="error-code--sqlconnectionisclosed"></a>错误代码：SqlConnectionIsClosed

- 消息：`The connection is closed by SQL Database.`

- **原因：** 当高并发运行和服务器终止连接时，SQL 数据库关闭了 SQL 连接。

- **建议**：远程服务器关闭了 SQL 连接。 Retry。 如果问题重现，请联系 Azure SQL 支持人员。


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：将字符串转换为唯一标识符失败

- **症状**：使用暂存复制和 PolyBase 将表格数据源（例如 SQL Server）中的数据复制到 Azure Synapse Analytics 时，遇到以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **原因：** Azure Synapse Analytics PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器中的 Polybase 设置下，将“use type default”选项设置为 false。


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型：DECIMAL(x,x)，违规值

- **症状**：使用暂存复制和 PolyBase 将表格数据源（例如 SQL Server）中的数据复制到 Azure Synapse Analytics 时，遇到以下错误：

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **原因：** Azure Synapse Analytics Polybase 无法将空字符串（null 值）插入十进制列。

- **解决方法**：在复制活动接收器中的 Polybase 设置下，将“use type default”选项设置为 false。


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息：HdfsBridge::CreateRecordReader

- **症状**：使用 PolyBase 将数据复制到 Azure Synapse Analytics 时遇到以下错误：

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **原因：** 可能的原因是架构（总列宽）太大（大于 1 MB）。 通过添加所有列的大小来检查目标 Azure Synapse Analytics 表的架构：

    - Int -> 4 字节
    - Bigint -> 8 字节
    - Varchar(n),char(n),binary(n), varbinary(n) -> n 字节
    - Nvarchar(n), nchar(n) -> n*2 字节
    - Date -> 6 字节
    - Datetime/(2), smalldatetime -> 16 字节
    - Datetimeoffset -> 20 字节
    - Decimal -> 19 字节
    - Float -> 8 字节
    - Money -> 8 字节
    - Smallmoney -> 4 字节
    - Real -> 4 字节
    - Smallint -> 2 字节
    - Time -> 12 字节
    - Tinyint -> 1 字节

- **解决方法**： 
    - 将列宽缩小为小于 1 MB。
    - 或者通过禁用 Polybase 来使用批量插入方法。


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **症状**：使用 SQL 查询从 Azure Synapse Analytics 提取数据时遇到以下错误：

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **原因：** Azure Synapse Analytics 在查询 Azure 存储中的外部表时遇到问题。

- **解决方法**：在 SSMS 中运行同一查询，检查是否看到相同的结果。 如果是，请创建 Azure Synapse Analytics 支持票证，并提供 Azure Synapse Analytics 服务器和数据库名称以进一步排查问题。
            

### <a name="low-performance-when-load-data-into-azure-sql"></a>将数据加载到 Azure SQL 时性能较低

- **症状**：将数据复制到 Azure SQL 会使速度变慢。

- **原因**：此问题的根本原因主要由 Azure SQL 端的瓶颈触发。 下面是一些可能的原因：

    - Azure DB 层不够高。

    - Azure DB DTU 使用率接近100%。 可以 [监视性能](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) 并考虑升级数据库层。

    - 未正确设置索引。 在数据加载之前删除所有索引，并在加载完成后重新创建它们。

    - WriteBatchSize 不够大，无法容纳架构行大小。 尝试放大此问题的属性。

    - 使用的是存储过程，而不是大容量嵌入，这会使性能更差。 

- **解决方法**：请参阅用于 [复制活动性能](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)的 TSG


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>性能层不足，导致复制失败

- **症状**：将数据复制到 Azure SQL 时出现以下错误消息： `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**：正在使用 Azure SQL s1，这种情况下会命中 IO 限制。

- **解决方法**：升级 Azure SQL 性能层以解决此问题。 


### <a name="sql-table-cannot-be-found"></a>找不到 SQL 表 

- **症状**：将数据从混合转换到本地 SQL Server 表时出错：`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**：当前 SQL 帐户的权限不足，无法执行由 .Net SqlBulkCopy WriteToServer 发出的请求。

- **解决方法**：切换到更有权限的 SQL 帐户。


### <a name="error-message-string-or-binary-data-would-be-truncated"></a>错误消息：将截断字符串或二进制数据

- **症状**：将数据复制到本地/Azure SQL Server 表时出错： 

- **原因**： Cx Sql 表架构定义中的一个或多个列的长度小于预期。

- **解决方法**：尝试执行以下步骤来解决此问题：

    1. 应用 SQL 接收器 [容错](https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance)，尤其是 "redirectIncompatibleRowSettings"，用于排查哪些行有问题。

        > [!NOTE]
        > 请注意，容错可能会引入额外的执行时间，这可能会导致更高的成本。

    2. 请仔细检查带有 SQL 表架构列长度的重定向数据，以查看需要更新)  (的列。

    3. 相应地更新表架构。


## <a name="azure-table-storage"></a>Azure 表存储

### <a name="error-code--azuretableduplicatecolumnsfromsource"></a>错误代码： AzureTableDuplicateColumnsFromSource

- 消息：`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink`

- **原因**：对于联接或非结构化 csv 文件，sql 查询可能很常见。

- **建议**：再次检查源列并相应地进行修复。


## <a name="db2"></a>DB2

### <a name="error-code--db2driverrunfailed"></a>错误代码： DB2DriverRunFailed

- 消息：`Error thrown from driver. Sql code: '%code;'`

- **原因**：如果错误消息包含 "SQLSTATE = 51002 SQLCODE =-805"，请参阅本文档中的提示： https://docs.microsoft.com/azure/data-factory/connector-db2#linked-service-properties

- **建议**：尝试在属性 "packageCollection" 中设置 "NULLID"


## <a name="delimited-text-format"></a>带分隔符的文本格式

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>错误代码：DelimitedTextColumnNameNotAllowNull

- 消息：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因：** 在活动中设置“firstRowAsHeader”时，第一行将用作列名。 此错误表示第一行包含空值。 例如：“ColumnA, ColumnB”。

- **建议**：检查第一行，如果存在空值，请修复值。


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>错误代码：DelimitedTextMoreColumnsThanDefined

- 消息：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因：** 有问题的行的列计数大于第一行的列计数。 原因可能是数据有问题，或者列分隔符/引号字符设置不正确。

- **建议**：获取错误消息中的行计数，检查行的列并修复数据。

- **原因**：如果所需的列计数在错误消息中为 "1"，则可能是因为指定了错误的压缩或格式设置。 因此，ADF 分析文件 () 错误。

- **建议**：检查格式设置，确保它与源文件相匹配。

- **原因：** 如果源是文件夹，则原因可能是指定的文件夹中的文件采用了不同的架构。

- **建议**：确保给定文件夹中的文件采用相同的架构。


## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>错误代码：DynamicsCreateServiceClientError

- 消息：`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **原因：** Dynamics 服务器端出现了暂时性问题。

- **建议**：重新运行管道。 如果仍旧失败，请尝试降低并行度。 如果还是失败，请联系 Dynamics 支持人员。


### <a name="columns-are-missing-when-previewingimporting-schema"></a>预览/导入架构时缺少列

- **症状**：导入架构或预览数据时缺少某些列。 错误消息：`The valid structure information (column name and type) are required for Dynamics source.`

- **原因**：此问题本质上是设计使然，因为 ADF 无法显示前10条记录中没有值的列。 请确保所添加的列的格式正确。 

- **建议**：在 "映射" 选项卡中手动添加列。


### <a name="error-code--dynamicsmissingtargetformultitargetlookupfield"></a>错误代码： DynamicsMissingTargetForMultiTargetLookupField

- 消息：`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**：源列在映射中不存在。

- **建议**：1。 请确保源包含目标列。 2. 在列映射中添加目标列。 请确保接收器列的模式为 "{fieldName} @EntityReference "。


### <a name="error-code--dynamicsinvalidtargetformultitargetlookupfield"></a>错误代码： DynamicsInvalidTargetForMultiTargetLookupField

- 消息：`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;"`

- **原因**：向多目标查找字段的目标实体提供了错误的实体名称。

- **建议**：为多目标查找字段提供有效的实体名称。


### <a name="error-code--dynamicsinvalidtypeformultitargetlookupfield"></a>错误代码： DynamicsInvalidTypeForMultiTargetLookupField

- 消息：`The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**：目标列中的值不是字符串

- **建议**：提供多目标查找目标列中的有效字符串。


### <a name="error-code--dynamicsfailedtorequetserver"></a>错误代码： DynamicsFailedToRequetServer

- 消息：`The dynamics server or the network is experiencing issues. Check network connectivity or check dynamics server log for more details.`

- **原因**： dynamics 服务器 instable 或无法访问或网络出现问题。

- **建议**：检查网络连接或查看 dynamics server 日志以了解更多详细信息。 若要获得更多帮助，请联系 dynamics 支持部门。


## <a name="excel-format"></a>Excel 格式

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>分析大型 Excel 文件时超时或性能降低

- **症状**：

    - 当你创建 Excel 数据集并从连接/存储区导入架构、预览数据、列出或刷新工作表时，如果 Excel 文件大小大，则可能会遇到超时错误。

    - 使用复制活动将数据从大型 Excel 文件复制 ( # B0 = 100 MB) 到其他数据存储时，可能会遇到性能下降或 OOM 问题。

- **原因**： 

    - 对于导入架构、预览数据以及在 excel 数据集上列出工作表这样的操作，超时为100秒，并为静态。 对于大型 Excel 文件，这些操作可能无法在超时值内完成。

    - ADF 复制活动将整个 Excel 文件读入内存，然后查找指定的工作表和单元格以读取数据。 此行为是由基础 SDK ADF 使用的。

- **解决方法**： 

    - 对于导入架构，你可以生成一个较小的示例文件，该文件是原始文件的子集，并选择 "从示例文件导入架构"，而不是 "从连接中导入架构"。

    - 对于列表工作表，在 "工作表" 下拉列表中，可以单击 "编辑"，并改为输入工作表名称/索引。

    - 若要将 ( # B0 100 MB) 的大型 excel 文件复制到其他存储，可以使用运动流式处理读取和执行的数据流 Excel 源。
    

## <a name="ftp"></a>FTP

### <a name="error-code--ftpfailedtoconnecttoftpserver"></a>错误代码： FtpFailedToConnectToFtpServer

- 消息：`Failed to connect to FTP server. Please make sure the provided server informantion is correct, and try again.`

- **原因**：链接服务类型不正确可能用于 ftp 服务器，如使用 SFTP 链接服务连接到 ftp 服务器。

- **建议**：检查目标服务器的端口。 默认情况下，FTP 使用端口21。


## <a name="http"></a>Http

### <a name="error-code--httpfilefailedtoread"></a>错误代码： HttpFileFailedToRead

- 消息：`Failed to read data from http server. Check the error from http server：%message;`

- **原因**：当 Azure 数据工厂与 http 服务器通信时，会发生此错误，但 http 请求操作将失败。

- **建议**：检查错误消息中的 http 状态代码 \ 消息，并修复远程服务器问题。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>错误代码： ArgumentOutOfRangeException

- 消息：`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**：在 ADF 中，在 0001-01-01 00:00:00 到 9999-12-31 23:59:59 范围内支持 DateTime 值。 但是，Oracle 支持范围更广的日期时间值 (例如 BC 世纪或 min/sec>59) ，这会导致 ADF 失败。

- **建议**： 

    运行 `select dump(<column name>)` 以检查 Oracle 中的值是否在 ADF 的范围内。 

    如果希望在结果中知道字节序列，请检查 https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle 。


## <a name="orc-format"></a>Orc 格式

### <a name="error-code--orcjavainvocationexception"></a>错误代码： OrcJavaInvocationException

- 消息：`An error occurred when invoking java, message: %javaException;.`

- **原因：** 如果错误消息包含“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，则原因通常与旧版集成运行时中的内存管理问题有关。

- **建议**：如果使用自承载 Integration Runtime，则建议升级到最新版本。

- **原因：** 如果错误消息包含“java.lang.OutOfMemory”，则原因是集成运行时不能提供足够的资源来处理文件。

- **建议**：限制集成运行时中的并发运行。 对于自承载集成运行时，请扩展到具有 8 GB 或更大内存的强大计算机。

- **原因：** 如果错误消息包含“NullPointerReference”，则原因可能是出现了暂时性错误。

- **建议**：重试。 如果问题仍然出现，请联系支持人员。

- **原因**：如果错误消息包含 "BufferOverflowException"，则可能是暂时性错误。

- **建议**：重试。 如果问题仍然出现，请联系支持人员。

- **原因**：当错误消息包含 "ClassCastException:org 不能转换为 org. Org.apache.hadoop.hive.serde2.columnar.lazybinarycolumnarserde" 时，这是 Java 运行时中的类型转换问题的问题，这是 Java 运行时中的类型转换问题。 通常，在 Java 运行时中无法正确处理源数据导致的情况。

- **建议**：这是数据问题。 在 orc 格式的数据中尝试使用字符串而不是 char/varchar。

### <a name="error-code--orcdatetimeexceedlimit"></a>错误代码： OrcDateTimeExceedLimit

- 消息：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**：如果 datetime 值为 "0001-01-01 00:00:00"，则可能是由于儒略历和公历之间的差异导致的。 https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates.

- **建议**：检查计时周期值，并避免使用 datetime 值 "0001-01-01 00:00:00"。


## <a name="parquet-format"></a>Parquet 格式

### <a name="error-code--parquetjavainvocationexception"></a>错误代码：ParquetJavaInvocationException

- 消息：`An error occurred when invoking java, message: %javaException;.`

- **原因：** 如果错误消息包含“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，则原因通常与旧版集成运行时中的内存管理问题有关。

- **建议**：如果使用的是自承载集成运行时，且版本低于 3.20.7159.1，建议升级到最新版本。

- **原因：** 如果错误消息包含“java.lang.OutOfMemory”，则原因是集成运行时不能提供足够的资源来处理文件。

- **建议**：限制集成运行时中的并发运行。 对于自承载集成运行时，请扩展到具有 8 GB 或更大内存的强大计算机。

- **原因：** 如果错误消息包含“NullPointerReference”，则原因可能是出现了暂时性错误。

- **建议**：重试。 如果问题仍然出现，请联系支持人员。


### <a name="error-code--parquetinvalidfile"></a>错误代码：ParquetInvalidFile

- 消息：`File is not a valid Parquet file.`

- **原因：** Parquet 文件问题。

- **建议**：检查输入是否为有效的 Parquet 文件。


### <a name="error-code--parquetnotsupportedtype"></a>错误代码：ParquetNotSupportedType

- 消息：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**： Azure 数据工厂不支持 Parquet 格式。

- **建议**：反复检查源数据。 参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>错误代码：ParquetMissedDecimalPrecisionScale

- 消息：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因：** 尝试分析数字的精度和小数位数，但系统未提供此类信息。

- **建议**：“Source”不会返回正确的精度和小数位数。 检查问题列的精度和小数位数。


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>错误代码：ParquetInvalidDecimalPrecisionScale

- 消息：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因：** 架构无效。

- **建议**：检查问题列的精度和小数位数。


### <a name="error-code--parquetcolumnnotfound"></a>错误代码：ParquetColumnNotFound

- 消息：`Column %column; does not exist in Parquet file.`

- **原因：** 源架构与接收器架构不匹配。

- **建议**：检查“activity”中的“mappings”。 确保源列可映射到正确的接收器列。


### <a name="error-code--parquetinvaliddataformat"></a>错误代码：ParquetInvalidDataFormat

- 消息：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因：** 数据无法转换为 mappings.source 中指定的类型

- **建议**：反复检查源数据，或者在复制活动列映射中指定此列的正确数据类型。 参阅文档： https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs 。


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>错误代码：ParquetDataCountNotMatchColumnCount

- 消息：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因：** 源列计数和接收器列计数不匹配

- **建议**：反复检查源列计数是否与“mapping”中的接收器列计数相同。


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>错误代码：ParquetDataTypeNotMatchColumnType

- **消息**：数据类型 %srcType; 与列“% columnIndex;”的给定列类型 %dstType; 不匹配。

- **原因：** 源中的数据无法转换为接收器中定义的类型

- **建议**：在 mapping.sink 中指定正确的类型。


### <a name="error-code--parquetbridgeinvaliddata"></a>错误代码：ParquetBridgeInvalidData

- 消息：`%message;`

- **原因：** 数据值超出限制

- **建议**：重试。 如果问题仍然出现，请联系我们。


### <a name="error-code--parquetunsupportedinterpretation"></a>错误代码：ParquetUnsupportedInterpretation

- 消息：`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因：** 不支持的方案

- **建议**：“ParquetInterpretFor”不应为“sparkSql”。


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>错误代码：ParquetUnsupportFileLevelCompressionOption

- 消息：`File level compression is not supported for Parquet.`

- **原因：** 不支持的方案

- **建议**：删除有效负载中的“CompressionType”。


### <a name="error-code--usererrorjniexception"></a>错误代码： UserErrorJniException

- 消息：`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**：无法创建 JVM，因为设置了一些非法 (全局) 参数。

- **建议**：登录到托管自承载 IR 的 **每个节点** 的计算机。 检查是否正确设置了系统变量，如下所示： `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` 。 重新启动所有 IR 节点，然后重新运行该管道。


### <a name="arithmetic-overflow"></a>算术溢出

- **症状**：复制 Parquet 文件时出现错误消息： `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**：将文件从 Oracle 复制到 Parquet 时，当前仅支持精度 <= 38 和整数部分的长度 <= 20。 

- **解决** 方法：可以将此类问题的列作为一种解决方法转换为 VARCHAR2。


### <a name="no-enum-constant"></a>无枚举常量

- **症状**：将数据复制到 Parquet 格式时出现错误消息： `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 或： `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` 。

- **原因**： 

    此问题可能是由空格或不受支持的字符引起的，如 (， {} ( # A2\n\t =) 列名称，因为 Parquet 不支持此类格式。 

    例如，如 *contoso (测试)* 的列名称将从 [代码](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java)中分析方括号内的类型 `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` 。 将引发错误，因为不存在这样的 "测试" 类型。

    若要检查支持的类型，可以在 [此处](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)查看。

- **解决方法**： 

    - 如果接收器列名称中有空格，请仔细检查。

    - 仔细检查是否将具有空格的第一行用作列名称。

    - 请仔细检查是否支持类型 OriginalType。 尝试避免这些特殊符号 `,;{}()\n\t=` 。 


## <a name="rest"></a>REST

### <a name="error-code--restsinkcallfailed"></a>错误代码： RestSinkCallFailed

- 消息：`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**：当 Azure 数据工厂通过 http 协议与 Rest 终结点通信时，会发生此错误，请求操作将失败。

- **建议**：检查错误消息中的 http 状态代码 \ 消息，并修复远程服务器问题。

### <a name="unexpected-network-response-from-rest-connector"></a>来自 REST 连接器的意外网络响应

- **症状**：终结点有时从 REST 连接器收到意外响应 (400/401/403/500)。

- **原因：** 构造 HTTP 请求时，REST 源连接器将来自链接服务/数据集/复制源的 URL 和 HTTP 方法/标头/正文用作参数。 此问题很可能是由一个或多个指定参数中的某些错误引起的。

- **解决方法**： 
    - 在 cmd 窗口中使用“curl”检查参数是否是问题的原因（Accept 和 User-Agent 标头应始终包括在内 ）：
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      如果命令返回相同的意外响应，请使用“curl”修复以上参数，直到返回预期的响应。 

      此外，还可以使用 "卷--帮助" 来更高级地使用该命令。

    - 如果仅 ADF REST 连接器返回意外响应，请联系 Microsoft 支持以进一步进行故障排除。
    
    - 请注意，“curl”可能不适合重现 SSL 证书验证问题。 在某些情况下，“curl”命令已成功执行，但没有遇到任何 SSL 证书验证问题。 但是，当在浏览器中执行相同的 URL 时，客户端实际上不会首先返回任何 SSL 证书来建立与服务器的信任。

      对于上述情况，建议使用 Postman 和 Fiddler 之类的工具 。


## <a name="sftp"></a>SFTP

#### <a name="error-code--sftpoperationfail"></a>错误代码： SftpOperationFail

- 消息：`Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**： Sftp 操作遇到问题。

- **建议**：检查 SFTP 的详细错误。


### <a name="error-code--sftprenameoperationfail"></a>错误代码： SftpRenameOperationFail

- 消息：`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, please set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**： SFTP 服务器不支持对临时文件进行重命名。

- **建议**：将 "useTempFileRename" 设置为复制接收器中的 false，以禁止上传到临时文件。


### <a name="error-code--sftpinvalidsftpcredential"></a>错误代码： SftpInvalidSftpCredential

- 消息：`Invalid Sftp credential provided for '%type;' authentication type.`

- **原因**：私钥内容从 AKV/SDK 提取，但未正确编码。

- **建议**：  

    如果私钥内容来自 AKV，并且如果客户将其直接上传到 SFTP 链接服务，则可以使用原始密钥文件

    请参阅 https://docs.microsoft.com/azure/data-factory/connector-sftp#using-ssh-public-key-authentication privatekey.ppk 内容是 Base64 编码的 SSH 私钥内容。

    请将 **原始私钥文件的整个内容** 编码为 base64 编码，并将编码的字符串存储到 AKV。 如果单击 "从文件上传"，则原始私钥文件是可以在 SFTP 链接服务上工作的文件。

    下面是用于生成字符串的一些示例：

    - 使用 c # 代码：
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

    - 使用第三方 base64 转换工具

        https://www.base64encode.org/建议使用类似工具。

- **原因**：选择了错误的密钥内容格式

- **建议**：  

    PKCS # 8 format SSH 私钥 (以 "-----开始加密私钥-----) " 开头，当前不支持在 ADF 中访问 SFTP 服务器。 

    运行以下命令，将密钥转换为传统 SSH 密钥格式， (以 "-----开始 RSA 私钥-----" 开头 ) ：

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**：凭据或私钥内容无效

- **建议**：双击工具（如 WinSCP），查看密钥文件或密码是否正确。

### <a name="sftp-copy-activity-failed"></a>SFTP 复制活动失败

- **症状**：错误代码： UserErrorInvalidColumnMappingColumnNotFound。 错误消息：`Column &apos;AccMngr&apos; specified in column mapping cannot be found in source data.`

- **原因**：源不包含名为 "AccMngr" 的列。

- **解决方法**：通过映射 "目标数据集" 列来确认是否存在此类 "AccMngr" 列，请仔细检查数据集的配置方式。


### <a name="error-code--sftpfailedtoconnecttosftpserver"></a>错误代码： SftpFailedToConnectToSftpServer

- 消息：`Failed to connect to Sftp server '%server;'.`

- **原因**：如果错误消息包含 "套接字读取操作在30000毫秒后超时"，则可能是因为 SFTP 服务器使用的链接服务类型不正确，如使用 FTP 链接服务连接到 sftp 服务器。

- **建议**：检查目标服务器的端口。 默认情况下，SFTP 使用端口22。

- **原因**：如果错误消息包含 "服务器响应不包含 SSH 协议标识"，则可能是 SFTP 服务器阻止了连接。 ADF 会创建多个连接，以从 SFTP 服务器并行下载，有时会命中 SFTP 服务器限制。 在实际情况下，不同的服务器会在命中限制时返回不同的错误。

- **建议**：  

    指定 SFTP 数据集的最大并发连接数为1，然后重新运行副本。 如果它成功通过，我们可以确保限制是原因。

    如果你想要提升低吞吐量，请联系 SFTP 管理员以增加并发连接计数限制，或将下面的 IP 添加到允许列表：

    - 如果使用的是托管 IR，请添加 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
      或者，如果不想将大型 IP 范围列表添加到 SFTP 服务器允许列表中，则可以安装自承载 IR。

    - 如果使用自承载 IR，请将安装 SHIR 的计算机 IP 添加到允许列表。


## <a name="sharepoint-online-list"></a>SharePoint Online 列表

### <a name="error-code--sharepointonlineauthfailed"></a>错误代码： SharePointOnlineAuthFailed

- 消息：`The access token generated failed, status code: %code;, error message: %message;.`

- **原因**：服务主体 ID 和密钥可能未正确设置。

- **建议**：检查已注册的应用程序 (服务主体 ID) 和密钥是否正确设置。


## <a name="xml-format"></a>Xml 格式

### <a name="error-code--xmlsinknotsupported"></a>错误代码： XmlSinkNotSupported

- 消息：`Write data in xml format is not supported yet, please choose a different format!`

- **原因**：将 Xml 数据集用作复制活动中的接收器数据集

- **建议**：使用不同于副本接收器格式的数据集。


### <a name="error-code--xmlattributecolumnnameconflict"></a>错误代码： XmlAttributeColumnNameConflict

- 消息：`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**：使用了导致冲突的属性前缀。

- **建议**：设置 "attributePrefix" 属性的其他值。


### <a name="error-code--xmlvaluecolumnnameconflict"></a>错误代码： XmlValueColumnNameConflict

- 消息：`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**：使用了一个子元素名称作为元素值的列名称。

- **建议**：设置 "valueColumn" 属性的其他值。


### <a name="error-code--xmlinvalid"></a>错误代码： XmlInvalid

- 消息：`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**：输入 xml 文件的格式不正确。

- **建议**：更正 xml 文件，使其格式正确。


## <a name="general-copy-activity-error"></a>常规复制活动错误

### <a name="error-code--jrenotfound"></a>错误代码：JreNotFound

- 消息：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因：** 自承载集成运行时找不到 Java 运行时。 读取特定的源时需要 Java 运行时。

- **建议**：检查集成运行时环境，并参阅文档： https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>错误代码：WildcardPathSinkNotSupported

- 消息：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因：** 接收器数据集不支持通配符。

- **建议**：检查接收器数据集，并修复路径（不使用通配符值）。


### <a name="fips-issue"></a>FIPS 问题

- **症状**：在启用 FIPS 的自承载 Integration Runtime 计算机上复制活动失败，并出现错误消息 `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 。 在将数据复制到 Azure Blob、SFTP 等连接器时可能会发生这种情况。

- **原因**： FIPS (联邦信息处理标准) 定义允许使用的一组特定加密算法。 在计算机上启用 FIPS 模式时，某些情况下会阻止复制活动依赖的某些加密类。

- **解决方法**：可以从 [本文](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)了解 Windows 中关于 fips 模式的当前情况，并评估是否可以在自承载 INTEGRATION RUNTIME 计算机上禁用 fips。

    另一方面，如果只想让 Azure 数据工厂绕过 FIPS 并使活动运行成功，则可以执行以下步骤：

    1. 打开安装自承载 Integration Runtime 的文件夹，通常在下 `C:\Program Files\Microsoft Integration Runtime\<IR version>\Shared` 。

    2. 打开 "diawp.exe.config"，将添加到如下所 `<enforceFIPSPolicy enabled="false"/>` `<runtime>` 示的部分中。

        ![禁用 FIPS](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. 重新启动自承载 Integration Runtime 计算机。


## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 问题页](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
