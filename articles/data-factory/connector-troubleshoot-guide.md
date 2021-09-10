---
title: 连接器疑难解答
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 27e9f92f7ea2be3ebdafbf973c4d1def179d5636
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864139"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>排查 Azure 数据工厂 Azure Synapse Analytics 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨排查 Azure 数据工厂和 Azure Synapse 连接器问题的常见方法。

## <a name="azure-blob-storage"></a>Azure Blob 存储

### <a name="error-code-azurebloboperationfailed"></a>错误代码：AzureBlobOperationFailed

- **消息**：“Blob 操作失败。 ContainerName: %containerName;，path: %path;。”

- **原因**：Blob 存储操作存在问题。

- **建议**：若要查看错误详细信息，请参阅 [Blob 存储错误代码](/rest/api/storageservices/blob-service-error-codes)。 如需更多帮助，请联系 Blob 存储团队。


### <a name="invalid-property-during-copy-activity"></a>执行复制活动期间属性无效

- 消息：`Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **原因**：数据集中定义的类型与复制活动中定义的源或接收器类型不一致。

- **解决方法**：编辑数据集或管道 JSON 定义以使类型一致，然后重新运行部署。

### <a name="error-code-fipsmodeisnotsupport"></a>错误代码：FIPSModeIsNotSupport

- **消息**：`Fail to read data form Azure Blob Storage for Azure Blob connector needs MD5 algorithm which can't co-work with FIPS mode. Please change diawp.exe.config in self-hosted integration runtime install directory to disable FIPS policy following https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/runtime/enforcefipspolicy-element.`

- **原因**：在安装了自承载集成运行时的 VM 上启用了 FIPS 策略。

- **建议**：在安装了自承载集成运行时的 VM 上禁用 FIPS 模式。 Windows不建议使用 FIPS 模式。

### <a name="error-code-azureblobinvalidblocksize"></a>错误代码：AzureBlobInvalidBlockSize

- **消息**：`Block size should between %minSize; MB and 100 MB.`

- **原因**：块大小超过 Blob 限制。

### <a name="error-code-azurestorageoperationfailedconcurrentwrite"></a>错误代码：AzureStorageOperationFailedConcurrentWrite

- **消息**：`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`

- **原因**：有多个并发复制活动运行或应用程序写入到同一文件。

### <a name="error-code-azureappendblobconcurrentwriteconflict"></a>错误代码：AzureAppendBlobConcurrentWriteConflict

- **消息**：`Detected concurrent write to the same append blob file, it's possible because you have multiple concurrent copy activities runs or applications writing to the same file '%name;'. Please check your ADF configuration and retry.`

- **原因**：存在多个并发写入请求，这会导致文件内容冲突。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>错误消息：请求过大

- **故障描述**：将数据复制到具有默认写入批大小的 Azure Cosmos DB 时，遇到以下错误：`Request size is too large.`

- **原因**：Azure Cosmos DB 限制单个请求的大小不超过 2 MB。 公式是“请求大小 = 单个文档大小 \* 写入批大小”。 如果文档大小太大，则默认行为将导致请求大小过大。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减小“写入批大小”值（默认值为 10000）。

### <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **故障描述**：将数据复制到 Azure Cosmos DB 时，遇到以下错误：

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **原因：** 有两个可能的原因：

    - 原因 1：如果写入行为是“插入”，则此错误表示源数据包含 ID 相同的行或对象。
    - 原因 2：如果写入行为是“更新插入”，且你为容器另外设置了唯一键，则此错误表示源数据中有些行或对象对定义的唯一键来说具有不同的 ID 但相同的值。

- **解决方法**： 

    - 如果是原因 1，请将“更新插入”设置为写入行为。
    - 如果是原因 2，请确保每个文档对定义的唯一键具有不同的值。

### <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **故障描述**：将数据复制到 Azure Cosmos DB 时，遇到以下错误：

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **原因**：使用的请求单位 (RU) 数量大于在 Azure Cosmos DB 中配置的可用 RU 数量。 若要了解 Azure Cosmos DB 如何计算 RU，请参阅 [Azure Cosmos DB 中的请求单位](../cosmos-db/request-units.md#request-unit-considerations)。

- **解决方法**：尝试以下两个解决方案之一：

    - 在 Azure Cosmos DB 中，增加容器 RU 的数量。 此解决方案将提高复制活动性能，但会在 Azure Cosmos DB 中产生更多成本。 
    - 将“writeBatchSize”设置为更小的值（例如 1000），将“parallelCopies”也设置为更小的值（例如 1） 。 此解决方案会降低复制运行性能，但不会在 Azure Cosmos DB 中产生更多成本。

### <a name="columns-missing-in-column-mapping"></a>列映射中缺少列

- **故障描述**：为 Azure Cosmos DB 导入架构进行列映射时，缺少一些列。 

- **原因**：Azure 数据工厂和 Synapse 管道从前 10 个 Azure Cosmos DB 文档中推断架构。 如果某些文档列或属性不包含值，则不会检测到架构，因此不会显示架构。

- **解决方法**：可按下面的代码所示优化查询，强制列值以空值显示在结果集中。 假设前 10 个文档中缺少“不可能”列。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **故障描述**：使用通用唯一标识符 (UUID) 字段从 Azure Cosmos DB MongoAPI 或 MongoDB 复制数据时，遇到以下错误：

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **原因**：有两种方式来用二进制 JSON (BSON) 表示 UUID：UuidStardard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会遇到错误。

- **解决方法**：在 MongoDB 连接字符串中，添加 uuidRepresentation=standard 选项。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>错误代码：CosmosDbSqlApiOperationFailed

- **消息**：`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**：CosmosDbSqlApi 操作存在问题。

- **建议**：若要查看错误详细信息，请参阅 [Azure Cosmos DB 帮助文档](../cosmos-db/troubleshoot-dot-net-sdk.md)。 如需更多帮助，请联系 Azure Cosmos DB 团队。

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>错误消息：基础连接已关闭:无法建立 SSL/TLS 安全通道的信任关系。

- **症状**：复制活动失败，出现以下错误： 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **原因**：在 TLS 握手期间，证书验证失败。

- **解决方法**：要解决此问题，请使用暂存复制跳过对 Azure Data Lake Storage Gen1 的传输层安全性 (TLS) 验证。 你需要重现此问题并收集网络监视器 (netmon) 的跟踪数据，然后与网络团队合作来检查本地网络配置。

    ![用于解决问题的 Azure Data Lake Storage Gen1 连接关系图。](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>错误消息：远程服务器返回了错误：(403)已禁止

- **症状**：复制活动失败，出现以下错误： 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **原因**：一个可能原因是，你使用的服务主体或托管身份无权访问某个文件夹或文件。

- **解决方法**：授予对要复制的所有文件夹和子文件夹的相应权限。 有关详细信息，请参阅[向/从 Azure Data Lake Storage Gen1 复制数据](connector-azure-data-lake-store.md#linked-service-properties)。

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>错误消息：无法使用服务主体来获取访问令牌。 ADAL 错误: service_unavailable

- **症状**：复制活动失败，出现以下错误：

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **原因**：如果 Azure Active Directory 拥有的 Service Token Server (STS) 不可用（即太忙而无法处理请求），则会返回 HTTP 错误 503。 

- **解决方法**：数分钟后重新运行复制活动。


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>错误代码：ADLSGen2OperationFailed

- 消息：`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

  | 原因分析                                               | 建议                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 如果该错误是由 Azure Data Lake Storage Gen2 引发的，则表明某个操作失败。| 请查看 Azure Data Lake Storage Gen2 引发的错误的详细消息。 如果错误是暂时性故障，请重试该操作。 如需更多帮助，请联系 Azure 存储支持部门并提供错误消息中的请求 ID。 |
  | 如果错误消息包含“禁止”这一字符串，则你使用的服务主体或托管标识可能没有足够的权限访问 Azure Data Lake Storage Gen2。 | 若要排查此错误，请参阅[在 Azure Data Lake Storage Gen2 中复制和转换数据](./connector-azure-data-lake-storage.md#service-principal-authentication)。 |
  | 如果错误消息包含字符串“InternalServerError”，则该错误是由 Azure Data Lake Storage Gen2 返回的。 | 此错误可能是由暂时性故障导致的。 如果是，请重试操作； 如果问题仍然存在，请联系 Azure 存储支持部门并提供错误消息中的请求 ID。 |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>“请求 Azure Data Lake Storage Gen2 帐户导致超时”错误

- **消息**： 
  * 错误代码 = `UserErrorFailedBlobFSOperation`
  * 错误消息 = `BlobFS operation failed for: A task was canceled.`

- **原因**：此问题是由 Azure Data Lake Storage Gen2 接收器超时错误引起的，此错误通常发生在自承载集成运行时 (IR) 计算机上。

- **建议**： 

    - 如果可能，请将自承载 IR 计算机和目标 Azure Data Lake Storage Gen2 帐户置于同一区域中。 这可避免随机超时错误，还可提高性能。

    - 检查是否有特殊的网络设置（例如 ExpressRoute），并确保网络具有足够的带宽。 建议在总体带宽较低时调低自承载 IR 并发作业设置。 这样做有助于避免跨多个并发作业的网络资源竞争。

    - 如果文件大小适中或较小，请为非二进制复制使用较小的块大小，以缓解此类超时错误。 有关详细信息，请参阅 [Blob 存储放置块](/rest/api/storageservices/put-block)。

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

        
## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

### <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>错误代码：AzurePostgreSqlNpgsqlDataTypeNotSupported

- **消息**：`The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **建议**：选择包含 int、bigint、smallint、serial、bigserial、smallserial、timestamp（带或不带时区、不带时区或日期数据类型的时间）的分区列。

### <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>错误代码：AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **消息**：`Partition column name must be specified.`

- **原因**：未提供分区列名称，并且无法自动确定该名称。
                  
## <a name="azure-files-storage"></a>Azure 文件存储

### <a name="error-code-azurefileoperationfailed"></a>错误代码：AzureFileOperationFailed

- **消息**：`Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **原因**：Azure 文件存储操作存在问题。

- **建议**：若要查看错误详细信息，请参阅 [Azure 文件帮助](/rest/api/storageservices/file-service-error-codes)。 如需更多帮助，请联系 Azure 文件存储团队。


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure Synapse Analytics、Azure SQL 数据库和 SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>错误代码：SqlFailedToConnect

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
    
### <a name="error-code-sqloperationfailed"></a>错误代码：SqlOperationFailed

- **消息**：`A database operation failed. Please search error to get more details.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串“SqlException”，则该错误是由 SQL 数据库的，表示某个特定操作失败。 | 如果 SQL 错误不明确，请尝试将数据库更改为最新的兼容性级别“150”。 它可能会引发最新版本 SQL 错误。 有关详细信息，请参阅[本文档](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat)。 <br/> 若要详细了解如何排查 SQL 问题，请按[数据库引擎错误](/sql/relational-databases/errors-events/database-engine-events-and-errors)中的 SQL 错误代码进行搜索。 如需更多帮助，请联系 Azure SQL 支持部门。 |
    | 如果错误消息包含字符串“PdwManagedToNativeInteropException”，则这通常是由于源列和接收器列的大小不匹配导致的。 | 请检查源列和接收器列的大小。 如需更多帮助，请联系 Azure SQL 支持部门。 |
    | 如果错误消息包含字符串“InvalidOperationException”，则通常是由于输入数据无效导致的。 | 若要确定是哪一行遇到了问题，请在复制活动上启用容错功能，该功能可将有问题的行重定向到存储，以便进一步调查。 有关详细信息，请参阅[复制活动的容错](./copy-activity-fault-tolerance.md)。 |


### <a name="error-code-sqlunauthorizedaccess"></a>错误代码：SqlUnauthorizedAccess

- **消息**：`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **原因**：凭据不正确，或登录帐户无法访问 SQL 数据库。

- **建议**：请检查确保登录帐户有足够的权限访问 SQL 数据库。


### <a name="error-code-sqlopenconnectiontimeout"></a>错误代码：SqlOpenConnectionTimeout

- **消息**：`Open connection to database timeout after '%timeoutValue;' seconds.`

- **原因**：此问题可能是 SQL 数据库暂时性故障。

- **建议**：请重试操作，使用更大的连接超时值更新链接的服务连接字符串。


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>错误代码：SqlAutoCreateTableTypeMapFailed

- **消息**：`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **原因**：自动创建表无法满足源要求。

- **建议**：更新映射中的列类型，或者在目标服务器中手动创建接收器表。


### <a name="error-code-sqldatatypenotsupported"></a>错误代码：SqlDataTypeNotSupported

- **消息**：`A database operation failed. Check the SQL errors.`

- **原因**：如果问题发生在 SQL 源中，并且错误与 SqlDateTime 溢出有关，则数据值超出逻辑类型范围 (1753/1/1 12:00:00 AM - 9999/12/31 11:59:59 PM)。

- **建议**：在源 SQL 查询中将类型强制转换为字符串，或在复制活动列映射中将列类型更改为“字符串”。

- **原因**：如果问题发生在 SQL 接收器上，并且错误与 SqlDateTime 溢出有关，则数据值超出了接收器表中允许的范围。

- **建议**：在接收器表中将相应的列类型更新为“datetime2”类型。


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>错误代码：SqlInvalidDbStoredProcedure

- **消息**：`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **原因**：指定的存储过程无效。 原因可能是存储过程没有返回任何数据。

- **建议**：使用 SQL 工具验证存储过程。 请确保存储过程可返回数据。


### <a name="error-code-sqlinvaliddbquerystring"></a>错误代码：SqlInvalidDbQueryString

- **消息**：`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **原因**：指定的 SQL 查询无效。 原因可能是查询没有返回任何数据。

- **建议**：使用 SQL 工具验证 SQL 查询。 确保查询可返回数据。


### <a name="error-code-sqlinvalidcolumnname"></a>错误代码：SqlInvalidColumnName

- **消息**：`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **原因**：配置可能有误，找不到此列。

- **建议**：验证查询中的列、数据集中的结构和活动中的映射 。


### <a name="error-code-sqlbatchwritetimeout"></a>错误代码：SqlBatchWriteTimeout

- **消息**：`Timeouts in SQL write operation.`

- **原因**：此问题可能是由 SQL 数据库暂时性故障导致的。

- **建议**：重试操作。 如果仍然出现此问题，请联系 Azure SQL 支持部门。


### <a name="error-code-sqlbatchwritetransactionfailed"></a>错误代码：SqlBatchWriteTransactionFailed

- **消息**：`SQL transaction commits failed.`

- **原因**：如果异常详细信息持续指示事务超时，则集成运行时和数据库之间的网络延迟高于默认阈值 30 秒。

- **建议**：使用大于等于 120 的连接超时值更新 SQL 链接服务连接字符串，然后重新运行活动。

- **原因**：如果异常详细信息间歇性地显示 SQL 连接已断开，则可能只是暂时性的网络故障或 SQL 数据库端问题。

- **建议**：重试活动并审阅 SQL 数据库端指标。


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>错误代码：SqlBulkCopyInvalidColumnLength

- **消息**：`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **原因**：SQL 大容量复制失败，因为它从大容量复制程序实用工具 (bcp) 客户端接收到无效的列长度。

- **建议**：若要确定是哪一行遇到了问题，请在复制活动上启用容错功能。 这可将有问题的行重定向到存储，以供进一步调查。 有关详细信息，请参阅[复制活动的容错](./copy-activity-fault-tolerance.md)。


### <a name="error-code-sqlconnectionisclosed"></a>错误代码：SqlConnectionIsClosed

- **消息**：`The connection is closed by SQL Database.`

- **原因**：在高并发运行且服务器终止连接时，SQL 数据库关闭了 SQL 连接。

- **建议**：重试连接。 如果仍然出现此问题，请联系 Azure SQL 支持部门。

### <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>错误代码：SqlServerInvalidLinkedServiceCredentialMissing

- **消息**：`The SQL Server linked service is invalid with its credential being missing.`

- **原因**：链接服务的配置不正确。

- **建议**：验证并修复 SQL Server 链接服务。 

### <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>错误代码：SqlParallelFailedToDetectPartitionColumn

- **消息**：`Failed to detect the partition column with command '%command;', %message;.`

- **原因**：表中没有主键或唯一键。

- **建议**：检查表以确保创建了主键或唯一索引。 

### <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>错误代码：SqlParallelFailedToDetectPhysicalPartitions

- **消息**：`Failed to detect the physical partitions with command '%command;', %message;.`

- **原因**：没有为表创建物理分区。 请检查数据库。

- **建议**：若要解决此问题，请参阅 [创建已分区表和索引](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true)。

### <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>错误代码：SqlParallelFailedToGetPartitionRangeSynapse

- **消息**：`Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **原因**：没有为表创建物理分区。 请检查数据库。

- **建议**：若要解决此问题，请参阅 [ SQL 池中的分区表](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md)。

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>错误消息：将字符串转换为唯一标识符失败

- **故障描述**：使用暂存复制和 PolyBase 将数据从表格数据源（例如 SQL Server）复制到 Azure Synapse Analytics 时，遇到以下错误：

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **原因**：Azure Synapse Analytics PolyBase 无法将空字符串转换为 GUID。

- **解决方法**：在复制活动接收器的 PolyBase 设置下，将“使用类型默认值”选项设置为 false。


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>错误消息：预期的数据类型：DECIMAL(x,x)，违规值

- **故障描述**：使用暂存复制和 PolyBase 将表格数据源（例如 SQL Server）中的数据复制到 Azure Synapse Analytics 中时，遇到以下错误：

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **原因**：Azure Synapse Analytics PolyBase 无法将空字符串（null 值）插入十进制列。

- **解决方法**：在复制活动接收器的 PolyBase 设置下，将“使用类型默认值”选项设置为 false。


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>错误消息：Java 异常消息：HdfsBridge::CreateRecordReader

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


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>错误消息：不满足使用 HTTP 条件标头指定的条件

- **故障描述**：使用 SQL 查询从 Azure Synapse Analytics 提取数据时，遇到以下错误：

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **原因**：Azure Synapse Analytics 在查询 Azure 存储中的外部表时遇到问题。

- **解决方法**：在 SQL Server Management Studio (SSMS) 中运行相同的查询，并检查是否获得相同的结果。 如果需要，请打开 Azure Synapse Analytics 的支持票证，并提供 Azure Synapse Analytics 服务器和数据库名称。


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>性能层等级较低，导致复制失败

- **故障描述**：将数据复制到 Azure SQL 数据库时，遇到以下错误：`Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **原因**：Azure SQL 数据库 s1 已达到输入/输出 (I/O) 限制。

- **解决方法**：升级 Azure SQL 数据库性能层可解决此问题。 


### <a name="sql-table-cant-be-found"></a>找不到 SQL 表 

- **故障描述**：将数据从混合复制到本地 SQL Server 表时，遇到以下错误：`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **原因**：当前的 SQL 帐户没有足够的权限来执行 .NET SqlBulkCopy.WriteToServer 发出的请求。

- **解决方法**：切换到权限更高的一个 SQL 帐户。


### <a name="error-message-string-or-binary-data-is-truncated"></a>错误消息：字符串或二进制数据被截断

- **故障描述**：将数据复制到本地 Azure SQL Server 表时出错。 

- **原因**：Cx SQL 表架构定义包含一个或多个长度小于预期值的列。

- **解决方法**：请尝试以下操作来解决此问题：

    1. 若要排查哪些行出现此问题，请应用 SQL 接收器[容错](./copy-activity-fault-tolerance.md)，尤其是“redirectIncompatibleRowSettings”）。

        > [!NOTE]
        > 容错可能需要额外的执行时间，这可能导致更高的成本。

    2. 根据 SQL 表架构列长度仔细检查重定向的数据，查看哪些列需要更新。

    3. 相应地更新表架构。

### <a name="error-code-faileddboperation"></a>错误代码：FailedDbOperation

- **消息**：`User does not have permission to perform this action.`

- **建议**：确保在使用 PolyBase 加载数据时，Azure Synapse Analytics 连接器中配置的用户必须对目标数据库具有“CONTROL”权限。 有关详细信息，请参阅此[文档](./connector-azure-sql-data-warehouse.md#required-database-permission)。


## <a name="azure-table-storage"></a>Azure 表存储

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>错误代码：AzureTableDuplicateColumnsFromSource

- **消息**：`Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **原因**：源列重复可能由下列原因之一导致：
   * 将数据库用作源并应用表联接。
   * 你具有标题行中列名重复的非结构化 CSV 文件。

- **建议**：仔细检查并根据需要修复源列。


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>错误代码：DB2DriverRunFailed

- **消息**：`Error thrown from driver. Sql code: '%code;'`

- **原因**：如果错误消息包含字符串“SQLSTATE=51002 SQLCODE=-805”，请按照 [从 DB2 复制数据](./connector-db2.md#linked-service-properties)中的“提示”进行操作。

- **建议**：尝试在 `packageCollection` 属性中设置“NULLID”。


## <a name="delimited-text-format"></a>带分隔符的文本格式

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>错误代码：DelimitedTextColumnNameNotAllowNull

- **消息**：`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **原因**：在活动中设置“firstRowAsHeader”时，第一行用作列名。 此错误表示第一行包含空值（例如“ColumnA, ColumnB”）。

- **建议**：检查第一行，如果为空则修复值。


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>错误代码：DelimitedTextMoreColumnsThanDefined

- **消息**：`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

  | 原因分析                                               | 建议                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | 有问题的行的列计数大于第一行的列计数。 它可能是由于数据问题或列分隔符/引号字符设置有误而导致的。 | 请从错误消息中获取行计数，检查行的列，并修复数据。 |
  | 如果错误消息中预期的列计数为“1”，则可能指定了错误的压缩或格式设置，这导致文件未正确解析。 | 请检查格式设置，确保它们与源文件匹配。 |
  | 如果源是文件夹，则指定文件夹下的文件可能具有不同的架构。 | 请确保指定文件夹中的文件具有相同的架构。 |


## <a name="dynamics-365-dataverse-common-data-service-and-dynamics-crm"></a>Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>错误代码：DynamicsCreateServiceClientError

- **消息**：`This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **原因**：此问题是 Dynamics 服务器端的暂时性问题。

- **建议**：重新运行管道。 如果再次失败，请尝试降低并行度。 如果问题仍然存在，请联系 Azure 支持部门。


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>导入架构或预览数据时缺少列

- **故障描述**：导入架构或预览数据时，缺少某些列。 错误消息：`The valid structure information (column name and type) are required for Dynamics source.`

- **原因**：此问题是设计使然的，因为数据工厂和 Synapse 管道无法在前 10 条记录中显示不包含值的列。 请确保添加的列格式正确。 

- **建议**：在“映射”选项卡中手动添加列。


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>错误代码：DynamicsMissingTargetForMultiTargetLookupField

- **消息**：`Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **原因**：源或列映射中不存在目标列。

- **建议**：  
  1. 请确保源包含目标列。 
  2. 在列映射中添加目标列。 确保接收器列的格式为“{fieldName}@EntityReference”。


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>错误代码：DynamicsInvalidTargetForMultiTargetLookupField

- 消息：`The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **原因：** 提供了错误的实体名称作为多目标查找字段的目标实体。

- **建议**：为多目标查找字段提供有效的实体名称。


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>错误代码：DynamicsInvalidTypeForMultiTargetLookupField

- **消息**：`The provided target type is not a valid string. Field: '%fieldName;'.`

- **原因**：目标列中的值不是字符串。

- **建议**：在“多目标查找目标”列中提供有效字符串。


### <a name="error-code-dynamicsfailedtorequetserver"></a>错误代码：DynamicsFailedToRequetServer

- **消息**：`The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **原因**：Dynamics 服务器不稳定或无法访问，或者网络出现问题。

- **建议**：有关详细信息，请检查网络连接或查看 Dynamics 服务器日志。 如需更多帮助，请联系 Dynamics 支持部门。


### <a name="error-code-dynamicsfailedtoconnect"></a>错误代码：DynamicsFailedToConnect 
 
 - **消息**：`Failed to connect to Dynamics: %message;` 
 
 - **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 你会看到 `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` 或者 `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'`。如果用例符合以下三个条件： <br/> 1. 要连接到 Dynamics 365、Common Data Service 或 Dynamics CRM。<br/>  2. 正在使用 Office365 身份验证。<br/>  3. 租户和用户在 Azure Active Directory 中配置为[条件访问](../active-directory/conditional-access/overview.md)和/或多重身份验证是必需验证方法（点击此[链接](/powerapps/developer/data-platform/authenticate-office365-deprecation)以参阅 Dataverse 文档）。<br/>  在这种情况下，连接曾在 2021/6/8 之前成功。 由于区域发现服务弃用，从 2021/6/9 开始，连接将会失败（请参阅此[链接](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)）。| 如果租户和用户在 Azure Active Directory 中配置为[条件访问](../active-directory/conditional-access/overview.md)和/或多重身份验证是必需验证方法，则在 2021 年 6 月 8 日后必须使用“Azure AD 服务主体”进行身份验证。 请点击此[链接](./connector-dynamics-crm-office-365.md#prerequisites)，了解详细步骤。|
    |如果在错误消息中看到 `Office 365 auth with OAuth failed`，则表明服务器可能有一些与 OAuth 不兼容的配置。| 1. 若要获取帮助，请联系 Dynamics 支持团队并提供详细的错误消息。 <br/> 2. 使用服务主体身份验证，你可以参考以下文章：[示例：使用 Azure AD 服务主体和证书身份验证的 Dynamics 联机](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)。 
    |如果在错误消息中看到 `Unable to retrieve authentication parameters from the serviceUri`，则表示你输入了错误的 Dynamics 服务 URL 或代理/防火墙来拦截流量。 |1. 请确保已将正确的服务 URI 置于链接服务中。<br/> 2. 如果使用自承载 IR，请确保防火墙/代理不会拦截对 Dynamics 服务器的请求。 |
    |如果在错误消息中看到 `An unsecured or incorrectly secured fault was received from the other party`，则表示从服务器端获得了意外响应。  | 1. 如果使用 Office 365 身份验证，请确保用户名和密码正确。 <br/> 2. 请确保已输入正确的服务 URI。 <br/> 3. 如果使用区域 CRM URL（URL 在“crm”后有一个数字），请确保使用正确的区域标识符。<br/> 4. 请联系 Dynamics 支持团队以获得帮助。 |
    |如果在错误消息中看到 `No Organizations Found`，则表示你的组织名有误，或者你在服务 URL 中使用了错误的 CRM 区域标识符。|1. 请确保已输入正确的服务 URI。<br/>2. 如果使用区域 CRM URL（URL 在“crm”后有一个数字），请确保使用正确的区域标识符。 <br/> 3. 请联系 Dynamics 支持团队以获得帮助。 |
    | 如果看到 `401 Unauthorized` 和 AAD 相关的错误消息，则表示服务主体存在问题。 |按照错误消息中的指导解决服务主体问题。 |
   |对于其他错误，问题通常出在服务器端。 |使用 [XrmToolBox](https://www.xrmtoolbox.com/) 建立连接。 如果错误仍然存在，请与 Dynamics 支持团队联系以获取帮助。 |

### <a name="error-code-dynamicsoperationfailed"></a>错误代码：DynamicsOperationFailed 
 
- **消息**：`Dynamics operation failed with error code: %code;, error message: %message;.` 

- **原因**：此操作在服务器端失败。 

- **建议**：从错误消息 `Dynamics operation failed with error code: {code}` 中提取 Dynamics 操作的错误代码，并参阅 [Web 服务错误代码](/powerapps/developer/data-platform/org-service/web-service-error-codes)一文以了解更多详细信息。 如有必要，可联系 Dynamics 支持团队。 
 
 
### <a name="error-code-dynamicsinvalidfetchxml"></a>错误代码：DynamicsInvalidFetchXml 
  
- **消息**：`The Fetch Xml query specified is invalid.` 

- **原因**：提取 XML 中存在错误。  

- **建议**：修复提取 XML 中的错误。 
 
 
### <a name="error-code-dynamicsmissingkeycolumns"></a>错误代码：DynamicsMissingKeyColumns 
 
- **消息**：`Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **原因**：源数据不包含接收器实体的键列。 

- **建议**：确认键列在源数据中或将源列映射到接收器实体上的键列。 
 
 
### <a name="error-code-dynamicsprimarykeymustbeguid"></a>错误代码：DynamicsPrimaryKeyMustBeGuid 
 
- **消息**：`The primary key attribute '%attribute;' must be of type guid.` 
 
- **原因**：主键列的类型不是“Guid”。 
 
- **建议**：请确保源数据中的主键列是“Guid”类型。 
 

### <a name="error-code-dynamicsalternatekeynotfound"></a>错误代码：DynamicsAlternateKeyNotFound 
 
- **消息**：`Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **原因**：提供的备用键不存在，这可能是由错误的键名称或权限不足所引起。 
 
- **建议**： <br/> 
    1. 更正键名称中的拼写错误。<br/> 
    1. 请确保对该实体具有足够的权限。 
 
 
### <a name="error-code-dynamicsinvalidschemadefinition"></a>错误代码：DynamicsInvalidSchemaDefinition 
 
- **消息**：`The valid structure information (column name and type) are required for Dynamics source.` 
 
- **原因**：列映射中的接收器列缺少“type”属性。 
 
- **建议**：使用门户上的 JSON 编辑器，可在列映射中将“type”属性添加到这些列。 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>错误代码：FtpFailedToConnectToFtpServer

- **消息**：`Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **原因**：对 FTP 服务器使用的链接服务类型可能不正确，例如使用安全 FTP (SFTP) 链接服务连接到 FTP 服务器。

- **建议**：检查目标服务器的端口。 FTP 使用端口 21。

### <a name="error-code-ftpfailedtoreadftpdata"></a>错误代码：FtpFailedToReadFtpData

- **消息**：`Failed to read data from ftp: The remote server returned an error: 227 Entering Passive Mode (*,*,*,*,*,*).`

- **原因**：对于数据工厂或 Synapse 管道支持的被动模式下的数据传输，未打开端口范围 1024 到 65535。

- **建议**：检查目标服务器的防火墙设置。 打开端口 1024-65535 或在 FTP 服务器中将端口范围指定为 SHIR/Azure IR IP 地址。


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>错误代码：HttpFileFailedToRead

- **消息**：`Failed to read data from http server. Check the error from http server：%message;`

- **原因**：当数据工厂或 Synapse 管道与 HTTP 服务器通信时，如果 HTTP 请求操作失败，则会发生此错误。

- **建议**：查看错误消息中的 HTTP 状态代码，并修复远程服务器问题。


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>错误代码：ArgumentOutOfRangeException

- 消息：`Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **原因**：在 Azure 数据工厂和 Synapse 管道中，支持从 0001-01-01 00:00:00 到 9999-12-31 23:59:59 范围内的日期时间值。 但是，Oracle 支持范围更广的日期时间值（例如公元前世纪或 min/sec>59），这会导致失败。

- **建议**： 

    若要查看 Oracle 中的值是否处于支持的日期范围内，请运行 `select dump(<column name>)`。 

    若要了解结果中的字节序列，请参阅[如何在 Oracle 中存储日期？](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)。


## <a name="orc-format"></a>ORC 格式

### <a name="error-code-orcjavainvocationexception"></a>错误代码：OrcJavaInvocationException

- **消息**：`An error occurred when invoking Java, message: %javaException;.`
- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串时“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，这通常是 Integration Runtime 旧版本中的内存管理问题。 | 如果使用自承载集成运行时，则建议升级到最新版本。 |
    | 如果错误消息包含字符串“java.lang.OutOfMemory”，则集成运行时没有足够的资源来处理文件。 | 限制集成运行时中的并发运行。 对于自承载 IR，请纵向扩展到具有 8 GB 或更大内存的强大计算机。 |
    |如果错误消息包含字符串“NullPointerReference”，则可能是暂时性错误导致的。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |
    | 如果错误消息包含字符串“BufferOverflowException”，则可能是暂时性错误导致的。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |
    | 如果错误消息包含字符串“java.lang.ClassCastException:org.apache.hadoop.hive.serde2.io.HiveCharWritable can't be cast to org.apache.hadoop.io.Text”，则可能是 Java 运行时内部的类型转换问题导致的。 通常，这意味着在 Java 运行时中无法正常处理源数据。 | 这是数据问题。 请尝试在 ORC 格式的数据中使用字符串而不是 char 或 varchar。 |

### <a name="error-code-orcdatetimeexceedlimit"></a>错误代码：OrcDateTimeExceedLimit

- **消息**：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因**：如果 datetime 值为“0001-01-01 00:00:00”，则可能是由于 [儒略历和格里历](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)之间的差异导致的。

- **建议**：检查时钟周期值，并避免使用日期/时间值“0001-01-01 00:00:00”。


## <a name="parquet-format"></a>Parquet 格式

### <a name="error-code-parquetjavainvocationexception"></a>错误代码：ParquetJavaInvocationException

- **消息**：`An error occurred when invoking java, message: %javaException;.`

- **原因和建议**：此错误可能由不同的原因导致。 请查看以下列表，了解可能的原因分析和相关建议。

    | 原因分析                                               | 建议                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 如果错误消息包含字符串时“java.lang.OutOfMemory”、“Java 堆空间”和“doubleCapacity”，这通常是 Integration Runtime 旧版本中的内存管理问题。 | 如果使用的是自承载 IR，而且版本低于 3.20.7159.1，建议升级到最新版本。 |
    | 如果错误消息包含字符串“java.lang.OutOfMemory”，则集成运行时没有足够的资源来处理文件。 | 限制集成运行时中的并发运行。 对于自承载 IR，请纵向扩展到具有 8 GB 或更大内存的强大计算机。 |
    | 如果错误消息包含字符串“NullPointerReference”，则可能是暂时性错误。 | 请重试操作即可。 如果问题仍然存在，请联系支持部门。 |

### <a name="error-code-parquetinvalidfile"></a>错误代码：ParquetInvalidFile

- **消息**：`File is not a valid Parquet file.`

- **原因**：这是一个 Parquet 文件问题。

- **建议**：检查输入是否为有效的 Parquet 文件。


### <a name="error-code-parquetnotsupportedtype"></a>错误代码：ParquetNotSupportedType

- 消息：`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **原因**：Azure 数据工厂和 Synapse 管道不支持 Parquet 格式。

- **建议**：通过转到 [复制活动支持的文件格式和压缩编解码器](./supported-file-formats-and-compression-codecs.md)来仔细检查源数据。


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>错误代码：ParquetMissedDecimalPrecisionScale

- **消息**：`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **原因**：分析了数字的精度和小数位数，但未提供此类信息。

- **建议**：源没有返回正确的精度和小数位数信息。 有关信息，请查看问题列。


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>错误代码：ParquetInvalidDecimalPrecisionScale

- 消息：`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **原因：** 架构无效。

- **建议**：检查问题列的精度和小数位数。


### <a name="error-code-parquetcolumnnotfound"></a>错误代码：ParquetColumnNotFound

- **消息**：`Column %column; does not exist in Parquet file.`

- **原因**：源架构与接收器架构不匹配。

- **建议**：检查活动中的映射。 确保源列可映射到正确的接收器列。


### <a name="error-code-parquetinvaliddataformat"></a>错误代码：ParquetInvalidDataFormat

- **消息**：`Incorrect format of %srcValue; for converting to %dstType;.`

- **原因**：数据无法转换为 mappings.source 中指定的类型。

- **建议**：仔细检查源数据，或在复制活动列映射中为此列指定正确的数据类型。 有关详细信息，请参阅[活动支持的文件格式和压缩编解码器](./supported-file-formats-and-compression-codecs.md)。


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>错误代码：ParquetDataCountNotMatchColumnCount

- **消息**：`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **原因**：源列计数和接收器列计数不匹配。

- **建议**：仔细检查，确保源列计数与“映射”中的接收器列计数相同。


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>错误代码：ParquetDataTypeNotMatchColumnType

- **消息**：`The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **原因**：源中的数据无法转换为接收器中定义的类型。

- **建议**：在 mapping.sink 中指定正确的类型。


### <a name="error-code-parquetbridgeinvaliddata"></a>错误代码：ParquetBridgeInvalidData

- **消息**：`%message;`

- **原因**：数据值超出了限制。

- **建议**：重试操作。 如果问题仍然存在，请联系我们。


### <a name="error-code-parquetunsupportedinterpretation"></a>错误代码：ParquetUnsupportedInterpretation

- **消息**：`The given interpretation '%interpretation;' of Parquet format is not supported.`

- **原因**：此方案不受支持。

- **建议**：“ParquetInterpretFor”不应为“sparkSql”。


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>错误代码：ParquetUnsupportFileLevelCompressionOption

- **消息**：`File level compression is not supported for Parquet.`

- **原因**：此方案不受支持。

- **建议**：删除有效负载中的“CompressionType”。


### <a name="error-code-usererrorjniexception"></a>错误代码：UserErrorJniException

- **消息**：`Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **原因**：无法创建 Java 虚拟机 (JVM)，因为设置了一些非法（全局）参数。

- **建议**：登录到托管自承载 IR 的每个节点的计算机。 检查确保系统变量设置正确，如下所示：`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`。 重启所有 IR 节点，然后重新运行该管道。


### <a name="arithmetic-overflow"></a>算术溢出

- **故障描述**：复制 Parquet 文件时出现错误消息：`Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **原因**：将文件从 Oracle 复制到 Parquet 时，当前仅支持精度 <= 38 且整数部分的长度 <= 20 的十进制数。 

- **解决方法**：若要进行解决，可将出现此问题的所有列都转换为 VARCHAR2。


### <a name="no-enum-constant"></a>无枚举常量

- **故障描述**：将数据复制为 Parquet 格式时，出现以下错误消息：`java.lang.IllegalArgumentException:field ended by &apos;;&apos;` 或 `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`。

- **原因**： 

    此问题可能是由于列名中的空格或不受支持的特殊字符（例如 ,;{}()\n\t=）导致的，因为 Parquet 不支持此类格式。 

    例如，contoso(test) 之类的列名会分析[代码](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` 中用括号括起的类型。 由于没有这样的“测试”类型，因此了引发了错误。

    若要查看支持的类型，请转到 GitHub [apache/parquet-mr 站点](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)。

- **解决方法**： 

    仔细检查以查看：
    - 接收器列名称中是否有空格。
    - 包含空格的第一行是否用作列名。
    - OriginalType 类型是否受支持。 请尽量不要使用以下特殊字符：`,;{}()\n\t=`。 

### <a name="error-code-parquetdatetimeexceedlimit"></a>错误代码：ParquetDateTimeExceedLimit

- 消息：`The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **原因：** 如果日期/时间值为“0001-01-01 00:00:00”，则可能是儒略历和公历之间的差异导致的。 有关更多详细信息，请参阅[儒略历和外推格里历日期之间的差异](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)。

- **解决方法**：检查时钟周期值，并避免使用日期/时间值“0001-01-01 00:00:00”。

### <a name="error-code-parquetinvalidcolumnname"></a>错误代码：ParquetInvalidColumnName

- **消息**：`The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **原因**：列名包含无效字符。

- **解决方法**：添加或修改列映射，使接收器列名有效。

## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>错误代码：RestSinkCallFailed

- **消息**：`Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **原因**：如果数据工厂或 Synapse 管道通过 HTTP 协议与 REST 终结点通信，并且请求操作失败，则会发生此错误。

- **建议**：检查错误消息中的 HTTP 状态代码或消息，并修复远程服务器问题。

### <a name="error-code-restsourcecallfailed"></a>错误代码：RestSourceCallFailed

- **消息**：`The HttpStatusCode %statusCode; indicates failure.&#xA;Request URL: %requestUri;&#xA;Response payload:%payload;`

- **原因**：当 Azure 数据工厂通过 HTTP 协议与 REST 终结点通信，并且请求操作失败时，发生此错误。

- **建议**：检查错误消息中的 HTTP 状态代码、请求 URL 或响应工作负载，并修复远程服务器问题。

### <a name="error-code-restsinkunsupportedcompressiontype"></a>错误代码：RestSinkUNSupportedCompressionType

- **消息**：`User Configured CompressionType is Not Supported By Azure Data Factory：%message;`

- **建议**：检查 REST 接收器支持的压缩类型。

### <a name="unexpected-network-response-from-the-rest-connector"></a>来自 REST 连接器的意外网络响应

- **故障描述**：终结点有时从 REST 连接器收到意外响应（400、401、403、500）。

- **原因**：构造 HTTP 请求时，REST 源连接器使用链接服务/数据集/复制源的 URL 和 HTTP 方法/标头/正文作为参数。 此问题很可能是由一个或多个指定参数中的某些错误引起的。

- **解决方法**： 
    - 在命令提示窗口中使用“curl”查看该问题是否是参数导致的（应始终包含“接受”和“用户代理”标头） ）：
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      如果命令返回相同的意外响应，请使用“curl”修复前面的参数，直到它返回预期的响应。 

      也可使用“curl--help”来更高级地使用命令。

    - 如果只有 REST 连接器返回意外响应，请联系 Microsoft 支持人员进行进一步的故障排除。
    
    - 请注意，“curl”可能不适合重现 SSL 证书验证问题。 在某些情况下，“curl”命令成功执行，没有遇到任何 SSL 证书验证问题。 但是，当在浏览器中执行相同的 URL 时，实际上不会返回任何 SSL 证书以供客户端与服务器建立信任。

      对于上述情况，建议使用 Postman 和 Fiddler 之类的工具 。


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>错误代码：SftpOperationFail

- **消息**：`Failed to '%operation;'. Check detailed error from SFTP.`

- **原因**：SFTP 操作出现问题。

- **建议**：检查来自 SFTP 的错误详细信息。


### <a name="error-code-sftprenameoperationfail"></a>错误代码：SftpRenameOperationFail

- **消息**：`Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **原因**：SFTP 服务器不支持重命名临时文件。

- **建议**：在复制接收器中将“useTempFileRename”设置为 false，以禁止上传到临时文件。


### <a name="error-code-sftpinvalidsftpcredential"></a>错误代码：SftpInvalidSftpCredential

- **消息**：`Invalid SFTP credential provided for '%type;' authentication type.`

- **原因**：私钥内容是从 Azure 密钥保管库或 SDK 提取的，但编码有误。

- **建议**：  

    如果私钥内容来自密钥保管库，则在将原始密钥文件直接上传到 SFTP 链接服务时，该文件有效。

    有关详细信息，请参阅[使用数据工厂或 Synapse 管道从/向 SFTP 服务器复制数据](./connector-sftp.md#use-ssh-public-key-authentication)。 私钥内容是 base64 编码的 SSH 私钥内容。

    请使用 base64 编码对整个原始私钥文件进行编码，并将已编码的字符串存储在密钥保管库中。 如果选择从文件上传，则可在 SFTP 链接服务上使用原始私钥文件。

    下面是可用于生成字符串的一些示例：

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

    - 使用第三方 base64 转换工具。 建议使用[编码为 Base64 格式](https://www.base64encode.org/)工具。

- **原因**：选择的密钥内容格式有误。

- **建议**：  

    当前不支持使用 PKCS#8 格式的 SSH 私钥（开头为“-----BEGIN ENCRYPTED PRIVATE KEY-----”）访问 SFTP 服务器。 

    若要将密钥转换为传统的 SSH 密钥格式（开头为“-----BEGIN RSA PRIVATE KEY-----”），请运行以下命令：

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **原因**：凭据或私钥内容无效。

- **建议**：若要查看密钥文件或密码是否正确，请使用 WinSCP 等工具仔细检查。

### <a name="sftp-copy-activity-failed"></a>SFTP 复制活动失败

- **症状**： 
  * 错误代码：UserErrorInvalidColumnMappingColumnNotFound 
  * 错误消息：`Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **原因**：源不包含名为“AccMngr”的列。

- **解决方法**：若要确定“AccMngr”列是否存在，请映射目标数据集列来仔细检查数据集配置。


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>错误代码：SftpFailedToConnectToSftpServer

- **消息**：`Failed to connect to SFTP server '%server;'.`

- **原因**：如果错误消息包含“套接字读取操作在 30,000 毫秒后超时”这一字符串，则可能是由于对 SFTP 服务器使用了错误的链接服务类型而导致的。 例如，你可能正在使用 FTP 链接服务连接到 SFTP 服务器。

- **建议**：检查目标服务器的端口。 SFTP 默认使用端口 22。

- **原因**：如果错误消息包含“服务器响应不包含 SSH 协议标识”这一字符串，则可能是由于 SFTP 服务器限制了连接而导致的。 创建了多个连接来从 SFTP 服务器并行下载，有时会遇到 SFTP 服务器限制。 通常，不同的服务器在遇到限制时会返回不同的错误。

- **建议**：  

    将 SFTP 数据集的最大并发连接数指定为 1，然后重新运行复制活动。 如果活动成功，则可确定这是限制导致的。

    若要提升低吞吐量，请与 SFTP 管理员联系以增加并发连接计数限制，或者可执行以下操作之一：

    * 如果使用自承载 IR，请将自承载 IR 计算机的 IP 添加到允许列表中。
    * 如果使用的是 Azure IR，请添加 [Azure Integration Runtime IP 地址](./azure-integration-runtime-ip-addresses.md)。 如果不想将一系列 IP 添加到 SFTP 服务器允许列表中，请改用自承载 IR。


#### <a name="error-code-sftppermissiondenied"></a>错误代码：SftpPermissionDenied

- **消息**：`Permission denied to access '%path;'`

- **原因**：指定的用户在操作时没有对文件夹或文件的读取或写入权限。

- **建议**：授予用户对 SFTP 服务器上文件夹或文件的读取或写入权限。
 
### <a name="error-code-sftpauthenticationfailure"></a>错误代码：SftpAuthenticationFailure

- **消息**：`Meet authentication failure when connect to Sftp server '%server;' using '%type;' authentication type. Please make sure you are using the correct authentication type and the credential is valid. For more details, see our troubleshooting docs.`

- **原因**：指定的凭据（密码或私钥）无效。

- **建议**：检查凭据。

- **原因**：指定的身份验证类型不被允许或不足以在 SFTP 服务器中完成身份验证。

- **建议**：应用以下选项以使用正确的身份验证类型：
    - 如果服务器需要密码，请使用“基本”。
    - 如果服务器需要私钥，请使用“SSH 公钥身份验证”。
    - 如果服务器需要“密码”和“私钥”，请使用“多重身份验证”。

- **原因**： SFTP 服务器需要“键盘 - 交互式”身份验证，但提供的是“密码”。

- **建议**： 

    “键盘 - 交互式”是一种特殊的身份验证方法，不同于“密码”。 这意味着登录服务器时，必须手动输入密码，并且无法使用之前保存的密码。 但 Azure 数据工厂 (ADF) 是一项计划的数据传输服务，并且它没有可以让你在运行时提供密码的弹出输入框。 <br/> 
    
    作为一个折衷方案，系统提供了一个选项来模拟后台的输入，而不是实际的手动输入，这等效于将“键盘 - 交互式”更改为“密码”。 如果可以接受此安全问题，请按照以下步骤进行启用：<br/> 
    1. 在 ADF 门户中，将鼠标悬停在 SFTP 链接服务上，并通过选择“代码”按钮打开其有效负载。
    1. 在“typeProperties”部分中添加 `"allowKeyboardInteractiveAuth": true`。
 
## <a name="sharepoint-online-list"></a>SharePoint Online 列表

### <a name="error-code-sharepointonlineauthfailed"></a>错误代码：SharePointOnlineAuthFailed

- **消息**：`The access token generated failed, status code: %code;, error message: %message;.`

- **原因**：服务主体 ID 和密钥可能设置有误。

- **建议**：检查注册的应用程序（服务主体 ID）和密钥，看看它们是否设置正确。


## <a name="xml-format"></a>XML 格式

### <a name="error-code-xmlsinknotsupported"></a>错误代码：XmlSinkNotSupported

- **消息**：`Write data in XML format is not supported yet, choose a different format!`

- **原因**：XML 数据集在复制活动中用作接收器数据集。

- **建议**：使用与接收器数据集格式不同的数据集。


### <a name="error-code-xmlattributecolumnnameconflict"></a>错误代码：XmlAttributeColumnNameConflict

- **消息**：`Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **原因**：使用了属性前缀，这导致出现冲突。

- **建议**：为“attributePrefix”属性设置其他值。


### <a name="error-code-xmlvaluecolumnnameconflict"></a>错误代码：XmlValueColumnNameConflict

- **消息**：`Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **原因**：其中一个子元素名用作了元素值的列名。

- **建议**：为“valueColumn”属性设置其他值。


### <a name="error-code-xmlinvalid"></a>错误代码：XmlInvalid

- **消息**：`Input XML file '%file;' is invalid with parsing error '%error;'.`

- **原因**：输入 XML 文件的格式不正确。

- **建议**：更正 XML 文件以使其格式正确.


## <a name="general-copy-activity-error"></a>常规复制活动错误

### <a name="error-code-jrenotfound"></a>错误代码：JreNotFound

- **消息**：`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **原因**：自承载 IR 找不到 Java 运行时。 读取特定的源时需要 Java 运行时。

- **建议**：检查集成运行时环境，具体请参阅 [使用自承载集成运行时](./format-parquet.md#using-self-hosted-integration-runtime)。


### <a name="error-code-wildcardpathsinknotsupported"></a>错误代码：WildcardPathSinkNotSupported

- **消息**：`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **原因**：接收器数据集不支持通配符值。

- **建议**：检查接收器数据集并重写路径（不使用通配符值）。


### <a name="fips-issue"></a>FIPS 问题

- **故障描述**：复制活动在启用了 FIPS 的自承载 IR 计算机上失败，错误消息如下：`This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **原因**：使用 Azure Blob 和 SFTP 等连接器复制数据时，可能会发生此错误。 美国联邦信息处理标准 (FIPS) 定义了允许使用的一组特定加密算法。 当计算机上启用了 FIPS 模式时，某些情况下会阻止复制活动所依赖的某些加密类。

- **解决方案**：了解 [为什么我们不再推荐“FIPS 模式”](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)，并评估你是否可在自承载 IR 计算机上禁用 FIPS。

    此外，如果只想绕过 FIPS 并使活动运行成功，请执行以下操作：

    1. 打开安装自承载 IR 的文件夹。 路径通常是 C:\Program Files\Microsoft Integration Runtime \<IR version>\Shared。

    2. 打开 diawp.exe.config 文件，然后在 `<runtime>` 部分的末尾添加 `<enforceFIPSPolicy enabled="false"/>`，如下所示：

        ![显示已禁用 FIPS 的 diawp.exe.config 文件部分的屏幕截图。](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. 保存文件，然后重启自承载 IR 计算机。

### <a name="error-code-jniexception"></a>错误代码：JniException

- **消息**：`An error occurred when invoking Java Native Interface.`

- **原因**：如果错误消息包含“无法创建 JVM: JNI 返回代码 [-6][JNI 调用失败: 参数无效。]”，则可能的原因是设置了一些非法的（全局）参数，导致无法创建 JVM。

- **建议**：登录托管自承载集成运行时的每个节点的计算机。 检查确保系统变量设置正确，如下所示：`_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8G`。 重启所有集成运行时节点，然后重新运行该管道。

### <a name="error-code-getoauth2accesstokenerrorresponse"></a>错误代码：GetOAuth2AccessTokenErrorResponse

- **消息**：`Failed to get access token from your token endpoint. Error returned from your authorization server: %errorResponse;.`

- **原因**：客户端 ID 或客户端机密无效，并且身份验证在授权服务器中失败。

- **建议**：更正授权服务器的所有 OAuth2 客户端凭据流设置。

### <a name="error-code-failedtogetoauth2accesstoken"></a>错误代码：FailedToGetOAuth2AccessToken

- **消息**：`Failed to get access token from your token endpoint. Error message: %errorMessage;.`

- **原因**：OAuth2 客户端凭据流设置无效。

- **建议**：更正授权服务器的所有 OAuth2 客户端凭据流设置。

### <a name="error-code-oauth2accesstokentypenotsupported"></a>错误代码：OAuth2AccessTokenTypeNotSupported

- **消息**：`The toke type '%tokenType;' from your authorization server is not supported, supported types: '%tokenTypes;'.`

- **原因**：不支持授权服务器。

- **建议**：使用可以返回具有受支持令牌类型的令牌的授权服务器。

### <a name="error-code-oauth2clientidcolonnotallowed"></a>错误代码：OAuth2ClientIdColonNotAllowed

- **消息**：`The character colon(:) is not allowed in clientId for OAuth2ClientCredential authentication.`

- **原因**：客户端 ID 包含无效的字符冒号 (`:`)。

- **建议**：使用有效的客户端 ID。

### <a name="error-code-managedidentitycredentialobjectnotsupported"></a>错误代码：ManagedIdentityCredentialObjectNotSupported

- **消息**：`Managed identity credential is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **建议**：检查支持的版本，并将集成运行时升级到更高版本。

### <a name="error-code-querymissingformatsettingsindataset"></a>错误代码：QueryMissingFormatSettingsInDataset

- **消息**：`The format settings are missing in dataset %dataSetName;.`

- **原因**：数据集类型为不受支持的二进制类型。

- **建议**：请改为使用 DelimitedText、Json、Avro、Orc 或 Parquet 数据集。

- **原因**：对于文件存储，数据集中缺少格式设置。

- **建议**：取消选择数据集中的“二进制副本”并设置正确的格式设置。

### <a name="error-code-queryunsupportedcommandbehavior"></a>错误代码：QueryUnsupportedCommandBehavior

- **消息**：`The command behavior "%behavior;" is not supported.`

- **建议**：不要将命令行为添加为可供预览的参数或 GetSchema API 请求 URL 的参数。

### <a name="error-code-dataconsistencyfailedtogetsourcefilemetadata"></a>错误代码：DataConsistencyFailedToGetSourceFileMetadata

- **消息**：`Failed to retrieve source file ('%name;') metadata to validate data consistency.`

- **原因**：接收器数据存储存在暂时性问题，或者不允许从接收器数据存储检索元数据。

### <a name="error-code-dataconsistencyfailedtogetsinkfilemetadata"></a>错误代码：DataConsistencyFailedToGetSinkFileMetadata

- **消息**：`Failed to retrieve sink file ('%name;') metadata to validate data consistency.`

- **原因**：接收器数据存储存在暂时性问题，或者不允许从接收器数据存储检索元数据。

### <a name="error-code-dataconsistencyvalidationnotsupportedfornondirectbinarycopy"></a>错误代码：DataConsistencyValidationNotSupportedForNonDirectBinaryCopy

- **消息**：`Data consistency validation is not supported in current copy activity settings.`

- **原因**：仅直接二进制复制场景中支持数据一致性验证。

- **建议**：删除复制活动有效负载中的“validateDataConsistency”属性。

### <a name="error-code-dataconsistencyvalidationnotsupportedforlowversionselfhostedintegrationruntime"></a>错误代码：DataConsistencyValidationNotSupportedForLowVersionSelfHostedIntegrationRuntime

- **消息**：`'validateDataConsistency' is not supported in this version ('%version;') of Self Hosted Integration Runtime.`

- **建议**：检查支持的集成运行时版本，将其升级到更高版本，或者从复制活动中删除“validateDataConsistency”属性。

### <a name="error-code-skipmissingfilenotsupportedfornondirectbinarycopy"></a>错误代码：SkipMissingFileNotSupportedForNonDirectBinaryCopy

- **消息**：`Skip missing file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“fileMissing”。

### <a name="error-code-skipinconsistencydatanotsupportedfornondirectbinarycopy"></a>错误代码：SkipInconsistencyDataNotSupportedForNonDirectBinaryCopy

- **消息**：`Skip inconsistency is not supported in current copy activity settings, it's only supported with direct binary copy when validateDataConsistency is true.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“dataInconsistency”。

### <a name="error-code-skipforbiddenfilenotsupportedfornondirectbinarycopy"></a>错误代码：SkipForbiddenFileNotSupportedForNonDirectBinaryCopy

- **消息**：`Skip forbidden file is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“fileForbidden”。

### <a name="error-code-skipforbiddenfilenotsupportedforthisconnector"></a>错误代码：SkipForbiddenFileNotSupportedForThisConnector

- **消息**：`Skip forbidden file is not supported for this connector: ('%connectorName;').`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“fileForbidden”。

### <a name="error-code-skipinvalidfilenamenotsupportedfornondirectbinarycopy"></a>错误代码：SkipInvalidFileNameNotSupportedForNonDirectBinaryCopy

- **消息**：`Skip invalid file name is not supported in current copy activity settings, it's only supported with direct binary copy with folder.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“invalidFileName”。

### <a name="error-code-skipinvalidfilenamenotsupportedforsource"></a>错误代码：SkipInvalidFileNameNotSupportedForSource

- **消息**：`Skip invalid file name is not supported for '%connectorName;' source.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“invalidFileName”。

### <a name="error-code-skipinvalidfilenamenotsupportedforsink"></a>错误代码：SkipInvalidFileNameNotSupportedForSink

- **消息**：`Skip invalid file name is not supported for '%connectorName;' sink.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“invalidFileName”。

### <a name="error-code-skipallerrorfilenotsupportedfornonbinarycopy"></a>错误代码：SkipAllErrorFileNotSupportedForNonBinaryCopy

- **消息**：`Skip all error file is not supported in current copy activity settings, it's only supported with binary copy with folder.`

- **建议**：删除复制活动有效负载中 skipErrorFile 设置的“allErrorFile”。

### <a name="error-code-deletefilesaftercompletionnotsupportedfornondirectbinarycopy"></a>错误代码：DeleteFilesAfterCompletionNotSupportedForNonDirectBinaryCopy

- **消息**：`'deleteFilesAfterCompletion' is not support in current copy activity settings, it's only supported with direct binary copy.`

- **建议**：删除“deleteFilesAfterCompletion”设置或使用直接二进制副本。

### <a name="error-code-deletefilesaftercompletionnotsupportedforthisconnector"></a>错误代码：DeleteFilesAfterCompletionNotSupportedForThisConnector

- **消息**：`'deleteFilesAfterCompletion' is not supported for this connector: ('%connectorName;').`

- **建议**：删除复制活动有效负载中的“deleteFilesAfterCompletion”设置。

### <a name="error-code-failedtodownloadcustomplugins"></a>错误代码：FailedToDownloadCustomPlugins

- **消息**：`Failed to download custom plugins.`

- **原因**：下载链接无效或暂时性连接问题。

- **建议**：如果消息显示它是暂时性问题，请重试。 如果问题持续出现，请联系技术支持。

## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](/answers/topics/azure-data-factory.html)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
