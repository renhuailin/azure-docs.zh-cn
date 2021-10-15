---
title: 常见映射数据流错误和消息
description: 了解如何排查 Azure 数据工厂中映射数据流的常见错误代码和消息问题。
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: 58b608c7e848dba2ea0d0e56532e51dcc4fe5a7b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390376"
---
# <a name="common-error-codes-and-messages"></a>常见错误代码和消息 

本文列出了 Azure 数据工厂中映射数据流报告的常见错误代码和消息以及相关的原因和建议。

## <a name="error-code-df-executor-sourceinvalidpayload"></a>错误代码:DF-Executor-SourceInvalidPayload
- **消息**：数据预览、调试和管道数据流执行失败，因为容器不存在
- **原因**：数据集包含存储中不存在的容器。
- **建议**：确保数据集中引用的容器存在并可访问。

## <a name="error-code-df-executor-systeminvalidjson"></a>错误代码:DF-Executor-SystemInvalidJson

- **消息**：JSON 分析错误、编码不受支持或多行
- **原因**：JSON 文件可能存在的问题：编码不受支持、字节损坏或在多个嵌套行上使用 JSON 源作为单文档。
- **建议**：验证 JSON 文件的编码是否受支持。 在使用 JSON 数据集的源转换中展开“JSON 设置”，然后打开“单一文档”。 
 
## <a name="error-code-df-executor-broadcasttimeout"></a>错误代码:DF-Executor-BroadcastTimeout

- **消息**：广播联接超时错误。请确保在调试运行中广播流在 60 秒内生成数据，在作业运行中广播流在 300 秒内生成数据
- **原因**：在调试运行中，广播的默认超时为 60 秒，而在作业运行中为 300 秒。 为广播选择的流太大，无法在此限制内生成数据。
- **建议**：检查 join、exists 和 lookup 的数据流转换中的“优化”选项卡。 广播的默认选项为“自动”。如果已设置“自动”，或你要手动将左侧或右侧设置为“固定”下的广播，可以设置较大的 Azure Integration Runtime (IR) 配置，或关闭广播  。 为了在数据流中获得最佳性能，建议允许 Spark 使用“自动”进行广播，并使用内存优化的 Azure IR。 
 
  如果要通过调试管道运行在调试测试执行中运行数据流，则可能会更频繁地遇到这种情况。 这是因为，Azure 数据工厂将广播超时限制为 60 秒以维持更快的调试体验。 可将超时延长为触发的运行的 300 秒超时。 为此，可以使用“调试” > “使用活动运行时”选项来利用“执行数据流”管道活动中定义的 Azure IR。 

- **消息**：广播联接超时错误，可对 join/exists/lookup 转换中的广播选项选择“关闭”来避免此问题。 如果你想要广播联接选项来提高性能，请确保广播流可以在 60 秒（在调试运行中）或 300 秒（在作业运行中）内生成数据。
- **原因**：广播在调试运行中的默认超时为 60 秒，在作业运行中的默认超时为 300 秒。 在广播联接中，为广播选择的流太大，无法在此限制内生成数据。 如果不使用广播联接，则数据流完成的默认广播可以达到相同的限制。
- **建议**：关闭广播选项，或避免广播其处理时间可能超过 60 秒的大型数据流。 选择更小的流进行广播。 大型 Azure SQL 数据仓库表和源文件通常不是很好的选择。 缺少广播联接时如果发生此错误，请使用更大的群集。

## <a name="error-code-df-executor-conversion"></a>错误代码:DF-Executor-Conversion

- **消息**：无法转换为日期或时间，因为字符无效
- **原因**：数据未采用预期的格式。
- **建议**：使用正确的数据类型。

## <a name="error-code-df-executor-invalidcolumn"></a>错误代码:DF-Executor-InvalidColumn
- **消息**：需要在查询中指定列名称。如果使用的是 SQL 函数，请设置别名
- **原因**：未指定列名。
- **建议**：如果使用 min() 或 max() 等 SQL 函数，请设置别名。

## <a name="error-code-df-executor-drivererror"></a>错误代码：DF-Executor-DriverError
- **消息**：INT96 是旧式时间戳类型，ADF Dataflow 不支持此类型。 请考虑将列类型升级到最新类型。
- **原因**：驱动程序错误。
- **建议**：INT96 是旧式时间戳类型，不受 Azure 数据工厂数据流的支持。 请考虑将列类型升级到最新类型。

## <a name="error-code-df-executor-blockcountexceedslimiterror"></a>错误代码：DF-Executor-BlockCountExceedsLimitError
- **消息**：未提交的块数不能超过上限值（100,000 个块）。 检查 blob 配置。
- **原因**：Blob 中未提交的最大块数为 100,000。
- **建议**：有关此问题的更多详细信息，请与 Microsoft 产品团队联系。

## <a name="error-code-df-executor-partitiondirectoryerror"></a>错误代码：DF-Executor-PartitionDirectoryError
- **消息**：指定的源路径要么包含多个分区目录（例如 &lt;Source Path&gt;/<Partition Root Directory 1>/a=10/b=20、&lt;Source Path&gt;/&lt;Partition Root Directory 2&gt;/c=10/d=30），要么包含具有其他文件的分区目录，要么包含非分区目录（例如 &lt;Source Path&gt;/&lt;Partition Root Directory 1&gt;/a=10/b=20、&lt;Source Path&gt;/Directory 2/file1），请从源路径中删除分区根目录并通过单独的源转换读取该目录。
- **原因**：源路径要么包含多个分区目录或具有其他文件的分区目录，要么包含非分区目录。
- **建议**：从源路径中删除分区根目录，并通过单独的源转换读取该目录。

## <a name="error-code-df-executor-invalidtype"></a>错误代码：DF-Executor-InvalidType
- **消息**：请确保参数类型与传入的值类型匹配。 当前不支持从管道传递 float 参数。
- 原因：声明的类型与实际参数值之间的数据类型不兼容。
- **建议**：检查传入到数据流中的参数值是否与声明的类型匹配。

## <a name="error-code-df-executor-parseerror"></a>错误代码：DF-Executor-ParseError
- **消息**：无法分析表达式。
- **原因**：由于格式不正确，表达式生成了分析错误。
- **建议**：检查表达式中的格式。

## <a name="error-code-df-executor-systemimplicitcartesian"></a>错误代码:DF-Executor-SystemImplicitCartesian
- **消息**：用于 INNER 联接的隐式笛卡尔乘积不受支持。请改用 CROSS JOIN。 联接中使用的列应为行创建唯一键。
- **原因**：逻辑计划之间 INNER 联接的隐式笛卡尔乘积不受支持。 如果要在联接中使用列，请创建一个唯一键。
- **建议**：对于基于不相等性的联接，请使用 CROSS JOIN。

## <a name="error-code-getcommand-outputasync-failed"></a>错误代码:GetCommand OutputAsync 失败
- **消息**：在数据流调试和数据预览期间：GetCommand OutputAsync 失败，出现...
- **原因**：此错误为后端服务错误。 
- **建议**：重试操作并重启调试会话。 如果重试和重启不能解决问题，请与客户支持联系。 

## <a name="error-code-df-executor-outofmemoryerror"></a>错误代码：DF-Executor-OutOfMemoryError
 
- **消息**：群集在执行期间遇到内存不足问题，请使用具有更大核心数和/或内存优化计算类型的集成运行时重试
- **原因**：群集内存不足。
- **建议**：调试群集仅用于开发。 请使用数据采样和适当的计算类型与大小来运行有效负载。 有关性能提示，请参阅[映射数据流性能指南](concepts-data-flow-performance.md)。

## <a name="error-code-df-executor-illegalargument"></a>错误代码：DF-Executor-illegalArgument

- **消息**：请确保链接服务中的访问密钥正确
- **原因**：帐户名称或访问密钥不正确。
- 建议：请确保链接服务中指定的帐户名或访问密钥正确。 

## <a name="error-code-df-executor-columnunavailable"></a>错误代码：DF-Executor-ColumnUnavailable
- **消息**：表达式中使用的列名不可用或无效。
- **原因**：表达式中使用的列名无效或不可用。
- **建议**：检查表达式中使用的列名。

 ## <a name="error-code-df-executor-outofdiskspaceerror"></a>错误代码：DF-Executor-OutOfDiskSpaceError
- **消息**：内部服务器错误
- **原因**：群集磁盘空间不足。
- **建议**：重试管道。 如果这样做仍然无法解决问题，请与客户支持联系。


 ## <a name="error-code-df-executor-storeisnotdefined"></a>错误代码：DF-Executor-StoreIsNotDefined
- **消息**：未定义存储配置。 此错误的可能原因是管道中的参数赋值无效。
- 原因：提供的存储配置无效。
- 建议：检查管道中的参数赋值。 参数表达式可能包含无效字符。


## <a name="error-code-4502"></a>错误代码：4502
- **消息**：存在大量的并发 MappingDataflow 执行，导致集成运行时引发限制，进而导致失败。
- **原因**：大量数据流活动运行在集成运行时中并发出现。 有关详细信息，请参阅 [Azure 数据工厂限制](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)。
- **建议**：若要并行运行更多的数据流活动，请将其分配到多个集成运行时。

## <a name="error-code-4510"></a>错误代码：4510
- 消息：在执行过程中意外失败。 
- 原因：由于调试群集的工作方式不同于作业群集，过多的调试运行可能导致群集在一段时间内经受损耗，从而导致内存问题和突发性重启。
- 建议：重启调试群集。 如果你在调试会话期间运行多个数据流，请改用活动运行，因为活动级别的运行会创建单独的会话，而不会给主调试群集造成过重的负担。

## <a name="error-code-invalidtemplate"></a>错误代码：InvalidTemplate
- **消息**：无法评估管道表达式。
- **原因**：由于语法错误，未正确处理传入到数据流活动中的管道表达式。
- **建议**：检查活动监视中的活动以验证表达式。

## <a name="error-code-2011"></a>错误代码：2011
- **消息**：活动在 Azure Integration Runtime 中运行，无法通过自承载集成运行时解密数据存储或联网计算的凭据。 请检查与此活动关联的链接服务的配置，并确保使用正确的集成运行时类型。
- **原因**：数据流不支持自承载集成运行时中的链接服务。
- **建议**：将数据流配置为在托管虚拟网络集成运行时中运行。

## <a name="error-code-df-xml-invalidvalidationmode"></a>错误代码：DF-Xml-InvalidValidationMode
- **消息**：提供了无效的 xml 验证模式。
- 原因：提供的 XML 验证模式无效。
- **建议**：检查参数值并指定正确的验证模式。

## <a name="error-code-df-xml-invaliddatafield"></a>错误代码：DF-Xml-InvalidDataField
- **消息**：损坏的记录的字段必须是字符串类型，并且可为空。
- 原因：XML 源中提供的列 `\"_corrupt_record\"` 数据类型无效。
- 建议：请确保 XML 源中的列 `\"_corrupt_record\"` 具有字符串数据类型且可为空。

## <a name="error-code-df-xml-malformedfile"></a>错误代码：DF-Xml-MalformedFile
- 消息：FAILFAST 模式中，带有路径的 XML 格式错误。
- 原因：FAILFAST 模式中，带有路径的 XML 格式错误。
- **建议**：将 XML 文件的内容更新为正确的格式。

## <a name="error-code-df-xml-invalidreferenceresource"></a>错误代码：DF-Xml-InvalidReferenceResource
- 消息：无法解析 XML 数据文件中的参考资源。
- 原因：无法解析 XML 数据文件中的参考资源。
- **建议**：应检查 XML 数据文件中的参考资源。

## <a name="error-code-df-xml-invalidschema"></a>错误代码：DF-Xml-InvalidSchema
- **消息**：架构验证失败。
- 原因：XML 源中提供的架构无效。
- 建议：检查 XML 源中的架构设置，确保其为源数据的子集架构。

## <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>错误代码：DF-Xml-UnsupportedExternalReferenceResource
- **消息**：不支持 xml 数据文件中的外部参考资源。
- 原因：不支持 XML 数据文件中的外部参考资源。
- **建议**：如果当前不支持外部参考资源，请更新 XML 文件内容。

## <a name="error-code-df-gen2-invalidaccountconfiguration"></a>错误代码：DF-GEN2-InvalidAccountConfiguration
- **消息**：应指定帐户密钥或 tenant/spnId/spnCredential/spnCredentialType 或 miServiceUri/miServiceToken 之一。
- 原因：ADLS Gen2 链接服务中提供的凭据无效。
- 建议：更新 ADLS Gen2 链接服务，以获得正确的凭据配置。

## <a name="error-code-df-gen2-invalidauthconfiguration"></a>错误代码：DF-GEN2-InvalidAuthConfiguration
- **消息**：只能指定三种身份验证方法（密钥、服务主体和 MI）之一。
- 原因：ADLS Gen2 链接服务中提供的身份验证方法无效。
- 建议：更新 ADLS Gen2 链接服务，以获得密钥、服务主体和 MI 三种身份验证方法之一。

## <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>错误代码：DF-GEN2-InvalidServicePrincipalCredentialType
- 消息：服务主体凭据类型无效。
- 原因：服务主体凭据类型无效。
- 建议：请更新 ADLS Gen2 链接服务，以设置正确的服务主体凭据类型。

## <a name="error-code-df-blob-invalidaccountconfiguration"></a>错误代码：DF-Blob-InvalidAccountConfiguration
- 消息：应指定帐户密钥或 SAS 令牌中的任一个。
- 原因：Azure Blob 链接服务中提供的凭据无效。
- 建议：使用 Azure Blob 链接服务的帐户密钥或 SAS 令牌。

## <a name="error-code-df-blob-invalidauthconfiguration"></a>错误代码：DF-Blob-InvalidAuthConfiguration
- **消息**：只能指定两种身份验证方法（密钥、SAS）之一。
- 原因：链接服务中提供的身份验证方法无效。
- 建议：使用 Azure Blob 链接服务的密钥或 SAS 身份验证。

## <a name="error-code-df-cosmos-partitionkeymissed"></a>错误代码：DF-Cosmos-PartitionKeyMissed
- **消息**：应为更新和删除操作指定分区键路径。
- 原因：Azure Cosmos DB 接收器中缺少分区键路径。
- 建议：使用 Azure Cosmos DB 接收器设置中提供的分区键。

## <a name="error-code-df-cosmos-invalidpartitionkey"></a>错误代码：DF-Cosmos-InvalidPartitionKey
- **消息**：对于更新和删除操作，分区键路径不能为空。
- 原因：对于更新和删除操作，分区键路径为空。
- 建议：使用 Azure Cosmos DB 接收器设置中提供的分区键。

- 消息：对于删除和更新操作，未在接收器中映射分区键。
- 原因：提供的分区键无效。
- 建议：在 Cosmos DB 接收器设置中，使用与容器的分区键相同的正确分区键。

## <a name="error-code-df-cosmos-idpropertymissed"></a>错误代码：DF-Cosmos-IdPropertyMissed
- **消息**：对于更新和删除操作，应映射“id”属性。
- 原因：更新和删除操作缺少 `id` 属性。
- 建议：确保 Cosmos DB 接收器设置中的输入数据具有 `id` 列。 如果没有，请使用“选择或派生转换”在接收器之前生成此列。

## <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>错误代码：DF-Cosmos-InvalidPartitionKeyContent
- **消息**：分区键应该以“/”开头。
- 原因：提供的分区键无效。
- 建议：确保 Cosmos DB 接收器设置中的分区键以 `/` 开头，例如 `/movieId`。

## <a name="error-code-df-cosmos-invalidconnectionmode"></a>错误代码：DF-Cosmos-InvalidConnectionMode
- 消息：连接模式无效。
- 原因：提供的连接模式无效。
- 建议：确认 Cosmos DB 设置中支持的模式为“网关”和“DirectHttps”。

## <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>错误代码：DF-Cosmos-InvalidAccountConfiguration
- **消息**：应指定 accountName 或 accountEndpoint。
- 原因：提供的帐户信息无效。
- 建议：在 Cosmos DB 链接服务中，指定帐户名或帐户终结点。

## <a name="error-code-df-github-writenotsupported"></a>错误代码：DF-Github-WriteNotSupported
- **消息**：Github 存储不允许写入。
- 原因：GitHub 存储为只读。
- 建议：将存储实体定义置于其他某个位置。
  
## <a name="error-code-df-pgsql-invalidcredential"></a>错误代码：DF-PGSQL-InvalidCredential
- **消息**：应指定用户/密码。
- 原因：缺少用户/密码。
- 建议：确保在相关的 PostgreSQL 链接服务中具有正确的凭据设置。

## <a name="error-code-df-snowflake-invalidstageconfiguration-only-blob-storage-can-be-used-as-stage"></a>错误代码：DF-Snowflake-InvalidStageConfiguration（仅 Blob 存储可用作阶段）
- **消息**：仅 blob 存储类型可用作 snowflake 读/写操作的阶段。
- 原因：Snowflake 中提供的暂存配置无效。
- 建议：更新 Snowflake 暂存设置，以确保仅使用 Azure Blob 链接服务。

- 消息：应通过 Azure Blob + SAS 身份验证指定 Snowflake 阶段属性。
- 原因：Snowflake 中提供的暂存配置无效。
- 建议：确保在 Snowflake 暂存设置中仅指定 Azure Blob + SAS 身份验证。

## <a name="error-code-df-snowflake-invaliddatatype"></a>错误代码：DF-Snowflake-InvalidDataType
- **消息**：Snowflake 中不支持此 Spark 类型。
- 原因：Snowflake 中提供的数据类型无效。
- 建议：在应用 Snowflake 接收器之前，请使用派生转换将输入数据的相关列更新为字符串类型。

## <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>错误代码：DF-Hive-InvalidBlobStagingConfiguration
- **消息**：应指定 Blob 存储暂存属性。
- 原因：配置单元中提供的暂存配置无效。
- 建议：请检查在用于暂存的相关 Blob 链接服务中，帐户密钥、帐户名和容器是否设置正确。

## <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>错误代码：DF-Hive-InvalidGen2StagingConfiguration
- **消息**：ADLS Gen2 存储暂存仅支持服务主体密钥凭据。
- 原因：配置单元中提供的暂存配置无效。
- 建议：请更新用作暂存的相关 ADLS Gen2 链接服务。 目前仅支持服务主体密钥凭据。

- **消息**：应指定 ADLS Gen2 存储暂存属性。 需要密钥或 tenant/spnId/spnKey 或 miServiceUri/miServiceToken 之一。
- 原因：配置单元中提供的暂存配置无效。
- 建议：使用在配置单元中用作暂存的正确凭据，更新相关 ADLS Gen2 链接服务。

## <a name="error-code-df-hive-invaliddatatype"></a>错误代码：DF-Hive-InvalidDataType
- **消息**：列不受支持。
- 原因：提供的列不受支持。
- 建议：更新输入数据的列，以匹配配置单元支持的数据类型。

## <a name="error-code-df-hive-invalidstoragetype"></a>错误代码：DF-Hive-InvalidStorageType
- **消息**：存储类型可以是 blob，也可以是 gen2。
- 原因：仅支持 Azure Blob 或 ADLS Gen2 存储类型。
- 建议：从 Azure Blob 或 ADLS Gen2 中选择正确的存储类型。

## <a name="error-code-df-delimited-invalidconfiguration"></a>错误代码：DF-Delimited-InvalidConfiguration
- **消息**：应指定空行或自定义标头中的任一个。
- 原因：提供的分隔配置无效。
- 建议：请更新 CSV 设置，以指定空行或自定义标头中的任一个。

## <a name="error-code-df-delimited-columndelimitermissed"></a>错误代码：DF-Delimited-ColumnDelimiterMissed
- **消息**：分析需要列分隔符。
- 原因：缺少列分隔符。
- 建议：在 CSV 设置中，确认具有分析所需的列分隔符。 

## <a name="error-code-df-mssql-invalidcredential"></a>错误代码：DF-MSSQL-InvalidCredential
- **消息**：应指定 user/pwd 或 tenant/spnId/spnKey 或 miServiceUri/miServiceToken 之一。
- 原因：MSSQL 链接服务中提供的凭据无效。
- 建议：请使用正确的凭据更新相关 MSSQL 链接服务，并且应指定 user/pwd 或 tenant/spnId/spnKey 或 miServiceUri/miServiceToken 之一。

## <a name="error-code-df-mssql-invaliddatatype"></a>错误代码：DF-MSSQL-InvalidDataType
- **消息**：字段不受支持。
- 原因：提供的字段不受支持。
- **建议**：修改输入数据列，使其与 MSSQL 支持的数据类型相匹配。

## <a name="error-code-df-mssql-invalidauthconfiguration"></a>错误代码：DF-MSSQL-InvalidAuthConfiguration
- **消息**：只能指定三种身份验证方法（密钥、服务主体和 MI）之一。
- 原因：MSSQL 链接服务中提供的身份验证方法无效。
- 建议：在相关 MSSQL 链接服务中，只能指定三种身份验证方法（密钥、服务主体和 MI）之一。

## <a name="error-code-df-mssql-invalidcloudtype"></a>错误代码：DF-MSSQL-InvalidCloudType
- **消息**：云类型无效。
- 原因：提供的云类型无效。
- **建议**：检查相关 MSSQL 链接服务中的云类型。

## <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>错误代码：DF-SQLDW-InvalidBlobStagingConfiguration
- **消息**：应指定 Blob 存储暂存属性。
- 原因：提供的 Blob 存储暂存设置无效
- 建议：请检查用于暂存的 Blob 链接服务是否具有正确的属性。

## <a name="error-code-df-sqldw-invalidstoragetype"></a>错误代码：DF-SQLDW-InvalidStorageType
- **消息**：存储类型可以是 blob，也可以是 gen2。
- 原因：为暂存提供的存储类型无效。
- 建议：检查用于暂存的链接服务的存储类型，并确保类型为 Blob 或 Gen2。

## <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>错误代码：DF-SQLDW-InvalidGen2StagingConfiguration
- **消息**：ADLS Gen2 存储暂存仅支持服务主体密钥凭据。
- 原因：为 ADLS Gen2 存储暂存提供的凭据无效。
- 建议：使用用于暂存的 Gen2 链接服务的服务主体密钥凭据。
 

## <a name="error-code-df-sqldw-invalidconfiguration"></a>错误代码：DF-SQLDW-InvalidConfiguration
- **消息**：应指定 ADLS Gen2 存储暂存属性。 需要密钥或 tenant/spnId/spnCredential/spnCredentialType 或 miServiceUri/miServiceToken 之一。
- 原因：提供的 ADLS Gen2 暂存属性无效。
- 建议：请更新 ADLS Gen2 存储暂存设置，以获得密钥或 tenant/spnId/spnCredential/spnCredentialType 或 miServiceUri/miServiceToken 之一。

## <a name="error-code-df-delta-invalidconfiguration"></a>错误代码：DF-DELTA-InvalidConfiguration
- **消息**：不能同时设置时间戳和版本。
- 原因：不能同时设置时间戳和版本。
- 建议：在增量设置中设置时间戳或版本。

## <a name="error-code-df-delta-keycolumnmissed"></a>错误代码：DF-DELTA-KeyColumnMissed
- **消息**：对于不可插入的操作，应指定键列。
- 原因：不可插入的操作缺少键列。
- 建议：在增量接收器上指定键列，以执行不可插入的操作。

## <a name="error-code-df-delta-invalidtableoperationsettings"></a>错误代码：DF-DELTA-InvalidTableOperationSettings
- **消息**：无法同时指定重新创建和截断选项。
- 原因：无法同时指定重新创建和截断选项。
- 建议：更新增量设置，以执行重新创建或截断操作。

## <a name="error-code-df-excel-worksheetconfigmissed"></a>错误代码：DF-Excel-WorksheetConfigMissed
- **消息**：需要指定 Excel 工作表名称或索引。
- 原因：提供的 Excel 工作表配置无效。
- 建议：检查参数值并指定工作表名称或索引，以读取 Excel 数据。

## <a name="error-code-df-excel-invalidworksheetconfiguration"></a>错误代码：DF-Excel-InvalidWorksheetConfiguration
- **消息**：Excel 工作表名称和索引不能同时存在。
- 原因：同时提供 Excel 工作表名称和索引。
- 建议：检查参数值并指定工作表名称或索引，以读取 Excel 数据。

## <a name="error-code-df-excel-invalidrange"></a>错误代码：DF-Excel-InvalidRange
- **消息**：提供的范围无效。
- 原因：提供的范围无效。
- **建议**：检查参数值并通过以下引用指定有效范围：[Azure 数据工厂数据集属性中的 Excel 格式](./format-excel.md#dataset-properties)。

## <a name="error-code-df-excel-worksheetnotexist"></a>错误代码：DF-Excel-WorksheetNotExist
- **消息**：Excel 工作表不存在。
- 原因：提供的工作表名称或索引无效。
- 建议：检查参数值并指定有效的工作表名称或索引，以读取 Excel 数据。

## <a name="error-code-df-excel-differentschemanotsupport"></a>错误代码：DF-Excel-DifferentSchemaNotSupport
- **消息**：目前不支持读取具有不同架构的 Excel 文件。
- 原因：目前不支持读取具有不同架构的 Excel 文件。
- 建议：若要解决此问题，请应用以下选项之一：
    - 使用 ForEach  +  数据流活动逐个读取 Excel 工作表。 
    - 在读取数据之前，手动更新每个工作表架构，使其具有相同的列。

## <a name="error-code-df-excel-invaliddatatype"></a>错误代码：DF-Excel-InvalidDataType
- **消息**：数据类型不受支持。
- 原因：数据类型不受支持。
- 建议：请将相关输入数据列的数据类型更改为“字符串”。

## <a name="error-code-df-excel-invalidfile"></a>错误代码：DF-Excel-InvalidFile
- **消息**：提供了无效的 Excel 文件，仅支持 .xlsx 和 .xls。
- 原因：提供的 Excel 文件无效。
- 建议：读取数据前，使用通配符筛选和获取 `.xls` 和 `.xlsx` Excel 文件。

## <a name="error-code-df-executor-outofmemorysparkbroadcasterror"></a>错误代码：DF-Executor-OutOfMemorySparkBroadcastError
- 消息：使用“左/右”选项显式广播的数据集应足够小，以适合节点的内存。 可以在加入/存在/查找转换中选择“关闭”广播选项，以避免此问题，或使用具有更高内存的集成运行时。
- 原因：广播表的大小远远超过了节点内存的限制。
- 建议：广播“左/右”选项应仅用于较小的数据集大小，可容纳在节点内存中，因此请确保适当地配置节点大小或关闭广播选项。

## <a name="error-code-df-mssql-invalidfirewallsetting"></a>错误代码：DF-MSSQL-InvalidFirewallSetting
- 消息：与主机的 TCP/IP 连接失败。 请确保 SQL Server 实例正在主机上运行并在端口接受 TCP/IP 连接。 请确保防火墙未阻止到端口的 TCP 连接。
- 原因：SQL 数据库的防火墙设置阻止了要访问的数据流。
- 建议：请检查 SQL 数据库的防火墙设置，并允许 Azure 服务和资源访问此服务器。

## <a name="error-code-df-executor-acquirestoragememoryfailed"></a>错误代码：DF-Executor-AcquireStorageMemoryFailed
- 消息：将展开内存传输到存储内存失败。 群集在执行过程中内存不足。 请使用具有更多内核和/或内存优化计算类型的集成运行时重试。
- 原因：群集内存不足。
- 建议：请使用具有更多内核和/或内存优化计算类型的集成运行时。

## <a name="error-code-df-cosmos-deletedatafailed"></a>错误代码：DF-Cosmos-DeleteDataFailed
- 消息：重试 3 次后，无法从 Cosmos 中删除数据。
- 原因：Cosmos 集合的吞吐量较小，导致发生限制，或者 Cosmo 中不存在行数据。
- 建议：请执行以下操作来解决此问题：
    - 如果错误为 404，请确保 Cosmos 集合中存在相关的行数据。 
    - 如果错误为限制，请增加 Cosmos 集合吞吐量或将其设置为自动缩放。
    - 如果错误是请求超时，请将 Cosmos 接收器中的“批大小”设置为较小值，例如 1000。

## <a name="error-code-df-sqldw-errorrowsfound"></a>错误代码：DF-SQLDW-ErrorRowsFound
- 原因：写入 Azure Synapse Analytics 接收器时发现了错误/无效行。
- 建议：请在已拒绝的数据存储位置（如果已配置）中查找错误行。

## <a name="error-code-df-sqldw-exporterrorrowfailed"></a>错误代码：DF-SQLDW-ExportErrorRowFailed
- 消息：将错误行写入存储时出现异常。
- 原因：将错误行写入存储时发生异常。
- 建议：请检查已拒绝的数据链接服务配置。

## <a name="error-code-df-executor-fieldnotexist"></a>错误代码：DF-Executor-FieldNotExist
- 消息：结构中的字段不存在。
- 原因：表达式中使用的字段名称无效或不可用。
- 建议：检查表达式中使用的字段名称。

## <a name="error-code-df-xml-invalidelement"></a>错误代码：DF-Xml-InvalidElement
- 消息：XML 元素具有无法转换的子元素或属性。
- 原因：XML 元素具有无法转换的子元素或属性。
- 建议：更新 XML 文件，以使 XML 元素具有正确的子元素或属性。

## <a name="error-code-df-gen2-invalidcloudtype"></a>错误代码：DF-GEN2-InvalidCloudType
- **消息**：云类型无效。
- 原因：提供的云类型无效。
- 建议：检查相关 ADLS Gen2 链接服务中的云类型。

## <a name="error-code-df-blob-invalidcloudtype"></a>错误代码：DF-Blob-InvalidCloudType
- **消息**：云类型无效。
- 原因：提供的云类型无效。
- 建议：请检查相关 Azure Blob 链接服务中的云类型。

## <a name="error-code-df-cosmos-failtoresetthroughput"></a>错误代码：DF-Cosmos-FailToResetThroughput
- 消息：由于正在进行另一个缩放操作，因此无法执行 Cosmos DB 吞吐量缩放操作，请稍后重试。
- 原因：由于正在进行另一个缩放操作，因此无法执行 Cosmos DB 吞吐量缩放操作。
- 建议：请登录 Cosmos 帐户，手动将其容器的吞吐量更改为自动缩放，或在数据流后添加自定义活动以重置吞吐量。

## <a name="error-code-df-executor-invalidpath"></a>错误代码：DF-Executor-InvalidPath
- 消息：路径未解析为任何文件。 请确保文件/文件夹存在且未隐藏。
- 原因：提供的文件/文件夹路径无效，无法找到或访问。
- 建议：请检查文件/文件夹路径，确保其存在且可以在存储中进行访问。

## <a name="error-code-df-executor-invalidpartitionfilenames"></a>错误代码：DF-Executor-InvalidPartitionFileNames
- 消息：文件名选项按分区设置时，文件名不能有空值。
- 原因：提供的分区文件名无效。
- 建议：请检查接收器设置以获得正确的文件名值。

## <a name="error-code-df-executor-invalidoutputcolumns"></a>错误代码：DF-Executor-InvalidOutputColumns
- 消息：结果包含 0 个输出列。 请确保至少映射一列。
- 原因：未映射列。
- 建议：请检查接收器架构以确保至少映射一列。

## <a name="error-code-df-executor-invalidinputcolumns"></a>错误代码：DF-Executor-InvalidInputColumns
- 消息：在源数据的架构中找不到源配置中的列。
- 原因：源中提供的列无效。
- 建议：检查源配置中的列并确保其为源数据架构的子集。

## <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>错误代码：DF-AdobeIntegration-InvalidMapToFilter
- **消息**：自定义资源只能有一个映射到筛选器的键/ID。
- 原因：提供的配置无效。
- 建议：在 AdobeIntegration 设置中，确保自定义资源只能有一个映射到筛选器的键/ID。

## <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>错误代码：DF-AdobeIntegration-InvalidPartitionConfiguration
- **消息**：仅支持单分区。 分区架构可以是 RoundRobin 或 Hash。
- 原因：提供的分区配置无效。
- 建议：在 AdobeIntegration 设置中，确认仅设置了单个分区，分区架构可以是 RoundRobin 或 Hash。

## <a name="error-code-df-adobeintegration-keycolumnmissed"></a>错误代码：DF-AdobeIntegration-KeyColumnMissed
- **消息**：对于不可插入的操作，必须指定键。
- 原因：缺少键列。
- 建议：更新 AdobeIntegration 设置以确保为不可插入操作指定键列。

## <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>错误代码：DF-AdobeIntegration-InvalidPartitionType
- **消息**：分区类型必须为 roundRobin。
- 原因：提供的分区类型无效。
- 建议：请更新 AdobeIntegration 设置以使分区类型为 RoundRobin。

## <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>错误代码：DF-AdobeIntegration-InvalidPrivacyRegulation
- 消息：目前唯一支持的隐私保护规范为“GDPR”。
- 原因：提供的隐私配置无效。
- 建议：在仅支持隐私“GDPR”时，请更新 AdobeIntegration 设置。

## <a name="error-code-df-executor-remoterpcclientdisassociated"></a>错误代码：DF-Executor-RemoteRPCClientDisassociated
- 消息：远程 RPC 客户端已解除关联。 可能是由于容器超过阈值或网络问题。
- 原因：由于暂时性的网络问题或 Spark 群集中的某个节点内存不足，数据流活动运行失败。
- 建议：使用以下选项解决此问题：
  - 选项 1：使用强大的群集（驱动器和执行器节点都有足够的内存，可以处理大数据）来运行数据流管道，并将“计算类型”设置为“内存优化”。 下图显示了设置。
        
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-compute-type.png" alt-text="显示“计算类型”配置的屏幕截图。":::   

  - 选项 2：使用更大的群集（例如 48 个核心）运行数据流管道。 可通过以下文档详细了解群集大小：[群集大小](./concepts-integration-runtime-performance.md#cluster-size)。
  
  - 选项 3：将输入数据重新分区。 对于在数据流 Spark 群集上运行的任务，一个分区就是一个任务，并在一个节点上运行。 如果一个分区中的数据太大，则节点上运行的相关任务需要消耗的内存就比节点本身消耗的内存还多，从而导致失败。 因此，可以使用重新分区来避免数据倾斜，确保每个分区的数据大小是均衡的，并且内存消耗量不会过大。
    
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-partition.png" alt-text="显示分区配置的屏幕截图。":::

    > [!NOTE]
    >  需要评估输入数据的数据大小或分区数，然后在“优化”下设置合理的分区数。 例如，在数据流管道执行中使用的群集为 8 个核心，每个核心的内存为 20GB，但输入数据为 1000GB 并分布在 10 个分区中。 如果你直接运行数据流，则会出现 OOM 问题，因为 1000GB/10 > 20GB，因此最好将重新分区数设置为 100 (1000GB/100 < 20GB)。
    
  - 选项 4：调整并优化源/接收器/转换设置。 例如，尝试将所有文件复制到一个容器，且不使用通配符模式。 有关详细信息，请参阅[映射数据流性能和优化指南](./concepts-data-flow-performance.md)。

## <a name="error-code-df-mssql-errorrowsfound"></a>错误代码：DF-MSSQL-ErrorRowsFound
- 原因：写入 Azure SQL 数据库接收器时发现了错误/无效行。
- 建议：请在拒绝的数据存储位置（如果已配置）查找错误行。

## <a name="error-code-df-mssql-exporterrorrowfailed"></a>错误代码：DF-MSSQL-ExportErrorRowFailed
- 消息：将错误行写入存储时出现异常。
- 原因：将错误行写入存储时发生异常。
- 建议：检查拒绝的数据链接服务配置。

## <a name="error-code-df-synapse-invaliddatabasetype"></a>错误代码：DF-Synapse-InvalidDatabaseType
- 消息：数据库类型不受支持。
- 原因：数据库类型不受支持。
- 建议：检查数据库类型，将其更改为正确的类型。

## <a name="error-code-df-synapse-invalidformat"></a>错误代码：DF-Synapse-InvalidFormat
- 消息：格式不受支持。
- 原因：格式不受支持。 
- 建议：检查格式，将其更改为正确的格式。

## <a name="error-code-df-synapse-invalidtabledbname"></a>错误代码：DF-Synapse-InvalidTableDBName
- 原因：表/数据库名称无效。
- 建议：更改为有效的表/数据库名称。 有效名称仅包含字母字符、数字和 `_`。

## <a name="error-code-df-synapse-invalidoperation"></a>错误代码：DF-Synapse-InvalidOperation
- 原因：该操作不受支持。
- 建议：更改无效操作。

## <a name="error-code-df-synapse-dbnotexist"></a>错误代码：DF-Synapse-DBNotExist
- 原因：数据库不存在。
- 建议：检查数据库是否存在。

## <a name="error-code-df-synapse-storedprocedurenotsupported"></a>错误代码：DF-Synapse-StoredProcedureNotSupported
- 消息：对于无服务器（按需）池，不支持使用“存储过程”作为源。
- 原因：无服务器池存在限制。
- 建议：重试使用“查询”作为源或将存储过程保存为视图，然后使用“表”作为源以直接从视图读取数据。

## <a name="error-code-df-executor-broadcastfailure"></a>错误代码：DF-Executor-BroadcastFailure
- 消息：在广播交换期间数据流执行失败。 可能的原因包括源中的连接配置不当，或发生了广播联接超时错误。 为确保正确配置源，请在数据流调试会话中测试连接或运行源数据预览。 若要避免广播联接超时，可以在 Join/Exists/Lookup 转换中选择“关闭”以关闭广播选项。 如果你想要使用广播选项来提高性能，请确保广播流可以在 60 秒（针对调试运行）或 300 秒（针对作业运行）内生成数据。 如果问题仍然存在，请与客户支持部门联系。

- **原因**：  
    1. 源连接/配置错误可能导致 join/exists/lookup 转换中发生广播失败。
    2. 广播在调试运行中的默认超时为 60 秒，在作业运行中的默认超时为 300 秒。 在广播联接中，为广播选择的流似乎太大，无法在此限制内生成数据。 如果不使用广播联接，则数据流执行的默认广播可以达到相同的限制。

- **建议**：
    - 在源中执行数据预览，以确认是否正确配置了源。 
    - 关闭广播选项，或避免广播其处理时间可能超过 60 秒的大型数据流。 相反，应选择更小的流进行广播。 
    - 大型 SQL/数据仓库表和源文件通常是不适当的候选项。 
    - 缺少广播联接时如果发生该错误，请使用较大的群集。 
    - 如果问题仍然存在，请与客户支持部门联系。

## <a name="error-code-df-cosmos-shorttypenotsupport"></a>错误代码：DF-Cosmos-ShortTypeNotSupport
- 消息：Cosmos DB 不支持短数据类型。
- 原因：Azure Cosmos DB 不支持短数据类型。
- 建议：添加派生转换，以先将相关列从短类型转换为整数，然后再在 Cosmos 接收器中使用这些列。

## <a name="error-code-df-blob-functionnotsupport"></a>错误代码：DF-Blob-FunctionNotSupport
- 消息：此终结点不支持 BlobStorageEvents、SoftDelete 或 AutomaticSnapshot。 若要使用此终结点，请禁用这些帐户功能。
- 原因：如果 Azure Blob 存储链接服务是使用服务主体或托管标识身份验证创建的，则数据流中不支持 Azure Blob 存储事件、软删除或自动快照。
- 建议：在 Azure Blob 帐户中禁用 Azure Blob 存储事件、软删除或自动快照功能，或者使用密钥身份验证创建链接服务。

## <a name="error-code-df-cosmos-invalidaccountkey"></a>错误代码：DF-Cosmos-InvalidAccountKey
- 消息：输入授权令牌无法为请求提供服务。 请检查是否根据协议生成了预期的有效负载，并检查所使用的密钥。
- 原因：权限不足，无法读取/写入 Azure Cosmos DB 数据。
- 建议：请使用读写密钥来访问 Azure Cosmos DB。

## <a name="next-steps"></a>后续步骤

在故障排除时如需更多帮助，请参阅以下资源：

- [映射数据流故障排除指南](data-flow-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)