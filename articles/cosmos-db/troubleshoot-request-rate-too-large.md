---
title: 对 Azure Cosmos DB 请求速率过大异常进行故障排除
description: 了解如何诊断和修复请求速率过大异常。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411331"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>诊断 Azure Cosmos DB 请求速率过大异常并对其进行故障排除
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

“请求速率过大”消息或错误代码 429 表明请求受到限制。

## <a name="troubleshooting-steps"></a>疑难解答步骤
以下部分包含请求过多的已知原因和解决方案。

### <a name="check-the-metrics"></a>检查指标
检查 [Azure Cosmos DB 监视](monitor-cosmos-db.md)，查看 429 异常的数量。

#### <a name="cause"></a>原因：
消耗的吞吐量（请求单位/秒）已超过[预配的吞吐量](set-throughput.md)。 SDK 会根据指定的重试策略自动重试请求。 如果经常遇到这种失败，请考虑增大集合的吞吐量。 检查门户的指标，确定是否收到了 429 错误。 查看分区键，以确保[均匀分配存储和请求量](partitioning-overview.md)。

#### <a name="solution"></a>解决方案：
1. 使用[门户或 SDK](set-throughput.md) 增加预配的吞吐量。
1. 将数据库或容器切换为[自动缩放](provision-throughput-autoscale.md)。

## <a name="next-steps"></a>后续步骤
* [诊断和排查](troubleshoot-dot-net-sdk.md)使用 Azure Cosmos DB .NET SDK 时遇到的问题。
* 了解 [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.NET v2](performance-tips.md) 的性能准则。
* [诊断并解决](troubleshoot-java-sdk-v4-sql.md) 使用 Azure Cosmos DB JAVA v4 SDK 时遇到的问题。
* 了解 [Java V4 SDK](performance-tips-java-sdk-v4-sql.md)的性能准则。