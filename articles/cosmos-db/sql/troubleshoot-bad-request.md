---
title: 排查 Azure Cosmos DB“错误的请求”异常
description: 了解如何诊断和修复“错误的请求”异常，例如输入内容或分区键无效、Azure Cosmos DB 中的分区键不匹配。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: ea7c05bc2361c266103908342e4ef9fb3a6160a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113539"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>诊断和排查 Azure Cosmos DB 中的“错误的请求”异常
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 状态代码 400 表示请求包含无效数据，或缺少必需的参数。

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>缺少 ID 属性
在此情况下，经常会出现以下错误：

“输入内容无效，因为缺少必需属性 - 'id;'”

响应中包含此错误意味着发送到服务的 JSON 文档缺少必需的 ID 属性。

### <a name="solution"></a>解决方案
根据 [REST 规范](/rest/api/cosmos-db/documents)指定一个使用字符串值的 `id` 属性作为文档的一部分，SDK 不会自动生成此属性的值。

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>无效的分区键类型
在此情况下，经常会出现类似于下面的错误：

“分区键 ... 无效。”

响应中包含此错误意味着分区键值的类型无效。

### <a name="solution"></a>解决方案
分区键的值应是字符串或数字，请确保值采用预期的类型。

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>错误的分区键值
在此情况下，经常会出现以下错误：

“从文档中提取的 PartitionKey 与头中指定的 PartitionKey 不匹配”

响应中包含此错误意味着你在执行某项操作时，传递的分区键值与文档中预期属性的正文值不匹配。 如果集合的分区键路径为 `/myPartitionKey`，则文档中会包含一个名为 `myPartitionKey` 的属性，其值与调用 SDK 方法时作为分区键值提供的值不匹配。

### <a name="solution"></a>解决方案
发送与文档属性值匹配的分区键值参数。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)在使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
* [诊断和排查](troubleshoot-java-sdk-v4-sql.md)使用 Azure Cosmos DB Java v4 SDK 时遇到的问题。
* 了解 [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) 的性能准则。
