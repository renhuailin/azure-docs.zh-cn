---
title: 排查 Azure Cosmos DB 连接器问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Azure Synapse Analytics 中的 Azure Cosmos DB 和 Azure Cosmos DB (SQL API) 连接器问题。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390381"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>排查 Azure 数据工厂和 Azure Synapse 中的 Azure Cosmos DB 连接器问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供有关排查 Azure 数据工厂和 Azure Synapse 中常见的 Azure Cosmos DB 和 Azure Cosmos DB (SQL API) 连接器问题的建议。

## <a name="error-message-request-size-is-too-large"></a>错误消息：请求过大

- **故障描述**：将数据复制到具有默认写入批大小的 Azure Cosmos DB 时，遇到以下错误：`Request size is too large.`

- **原因**：Azure Cosmos DB 限制单个请求的大小不超过 2 MB。 公式是“请求大小 = 单个文档大小 \* 写入批大小”。 如果文档大小太大，则默认行为将导致请求大小过大。 可以优化写入批大小。

- **解决方法**：在复制活动接收器中，减小“写入批大小”值（默认值为 10000）。

## <a name="error-message-unique-index-constraint-violation"></a>错误消息：唯一索引约束冲突

- **故障描述**：将数据复制到 Azure Cosmos DB 时，遇到以下错误：

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **原因：** 有两个可能的原因：

    - 原因 1：如果写入行为是“插入”，则此错误表示源数据包含 ID 相同的行或对象。
    - 原因 2：如果写入行为是“更新插入”，且你为容器另外设置了唯一键，则此错误表示源数据中有些行或对象对定义的唯一键来说具有不同的 ID 但相同的值。

- **解决方法**： 

    - 如果是原因 1，请将“更新插入”设置为写入行为。
    - 如果是原因 2，请确保每个文档对定义的唯一键具有不同的值。

## <a name="error-message-request-rate-is-large"></a>错误消息：请求速率太大

- **故障描述**：将数据复制到 Azure Cosmos DB 时，遇到以下错误：

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **原因**：使用的请求单位 (RU) 数量大于在 Azure Cosmos DB 中配置的可用 RU 数量。 若要了解 Azure Cosmos DB 如何计算 RU，请参阅 [Azure Cosmos DB 中的请求单位](../cosmos-db/request-units.md#request-unit-considerations)。

- **解决方法**：尝试以下两个解决方案之一：

    - 在 Azure Cosmos DB 中，增加容器 RU 的数量。 此解决方案将提高复制活动性能，但会在 Azure Cosmos DB 中产生更多成本。 
    - 将“writeBatchSize”设置为更小的值（例如 1000），将“parallelCopies”也设置为更小的值（例如 1） 。 此解决方案会降低复制运行性能，但不会在 Azure Cosmos DB 中产生更多成本。

## <a name="columns-missing-in-column-mapping"></a>列映射中缺少列

- **故障描述**：为 Azure Cosmos DB 导入架构进行列映射时，缺少一些列。 

- **原因**：Azure 数据工厂和 Synapse 管道从前 10 个 Azure Cosmos DB 文档中推断架构。 如果某些文档列或属性不包含值，则不会检测到架构，因此不会显示架构。

- **解决方法**：可按下面的代码所示优化查询，强制列值以空值显示在结果集中。 假设前 10 个文档中缺少“不可能”列。 或者，可以手动添加要映射的列。

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>错误消息：读取器的 GuidRepresentation 为 CSharpLegacy

- **故障描述**：使用通用唯一标识符 (UUID) 字段从 Azure Cosmos DB MongoAPI 或 MongoDB 复制数据时，遇到以下错误：

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **原因**：有两种方式来用二进制 JSON (BSON) 表示 UUID：UuidStardard 和 UuidLegacy。 默认使用 UuidLegacy 来读取数据。 如果 MongoDB 中的 UUID 数据是 UuidStandard，则会遇到错误。

- **解决方法**：在 MongoDB 连接字符串中，添加 uuidRepresentation=standard 选项。 有关详细信息，请参阅 [MongoDB 连接字符串](connector-mongodb.md#linked-service-properties)。

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>错误代码：CosmosDbSqlApiOperationFailed

- **消息**：`CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **原因**：CosmosDbSqlApi 操作存在问题。  这特别适用于 Cosmos DB (SQL API) 连接器。

- **建议**：若要查看错误详细信息，请参阅 [Azure Cosmos DB 帮助文档](../cosmos-db/troubleshoot-dot-net-sdk.md)。 如需更多帮助，请联系 Azure Cosmos DB 团队。

## <a name="next-steps"></a>后续步骤

尝试通过以下资源获得故障排除方面的更多帮助：

- [连接器故障排除指南](connector-troubleshoot-guide.md)
- [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [数据工厂功能请求](/answers/topics/azure-data-factory.html)
- [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
- [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
