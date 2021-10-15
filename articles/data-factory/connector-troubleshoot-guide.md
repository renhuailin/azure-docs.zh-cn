---
title: 连接器疑难解答
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d09621589899693195f9195cdb0e76da5762b03e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388976"
---
# <a name="troubleshoot-azure-data-factory-and-azure-synapse-analytics-connectors"></a>排查 Azure 数据工厂 Azure Synapse Analytics 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的连接器问题。  

## <a name="connector-specific-problems"></a>特定于连接器的问题

可以参考每个连接器的故障排除页来查看特定于该连接器的问题、这些问题的原因解释及其解决方法建议。

- [Azure Blob 存储](connector-troubleshoot-azure-blob-storage.md)
- [Azure Cosmos DB（包括 SQL API 连接器）](connector-troubleshoot-azure-cosmos-db.md)
- [Azure Data Lake（Gen1 和 Gen2）](connector-troubleshoot-azure-data-lake.md)
- [Azure database for PostgreSQL](connector-troubleshoot-postgresql.md)
- [Azure 文件存储](connector-troubleshoot-azure-files.md)
- [Azure Synapse Analytics、Azure SQL 数据库和 SQL Server](connector-troubleshoot-synapse-sql.md)
- [DB2](connector-troubleshoot-db2.md)
- [带分隔符的文本格式](connector-troubleshoot-delimited-text.md)
- [Dynamics 365、Dataverse (Common Data Service) 和 Dynamics CRM](connector-troubleshoot-dynamics-dataverse.md)
- [FTP、SFTP 和 HTTP](connector-troubleshoot-ftp-sftp-http.md)
- [Oracle](connector-troubleshoot-oracle.md)
- [ORC 格式](connector-troubleshoot-orc.md)
- [Parquet 格式](connector-troubleshoot-parquet.md)
- [REST](connector-troubleshoot-rest.md)
- [SharePoint Online 列表](connector-troubleshoot-sharepoint-online-list.md)
- [XML 格式](connector-troubleshoot-xml.md)

## <a name="general-copy-activity-errors"></a>常规复制活动错误

使用复制活动时经常会出现以下错误，任何连接器都可能发生这些错误。

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

        :::image type="content" source="./media/connector-troubleshoot-guide/disable-fips-policy.png" alt-text="显示已禁用 FIPS 的 diawp.exe.config 文件部分的屏幕截图。":::

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

- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)