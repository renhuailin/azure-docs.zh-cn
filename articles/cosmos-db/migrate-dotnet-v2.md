---
title: 迁移应用程序以使用 Azure Cosmos DB .NET SDK 2.0 (Microsoft.Azure.Cosmos)
description: 了解如何将现有 .NET 应用程序从 v1 SDK 升级到适用于 Core (SQL) API 的 .NET SDK v2。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 0d6fcdc1fde00a24b4317b035e043e3b30ba5b6e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037121"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>迁移应用程序以使用 Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> 请务必注意，.NET SDK 的 v3 当前可用，[此处](migrate-dotnet-v3.md)提供了从 v2 到 v3 的迁移计划。 若要了解 Azure Cosmos DB .NET SDK v2，请参阅[发行说明](sql-api-sdk-dotnet.md)、[.NET GitHub 存储库](https://github.com/Azure/azure-cosmos-dotnet-v2)、.NET SDK v2 [性能提示](performance-tips.md)和[故障排除指南](troubleshoot-dot-net-sdk.md)。
>

本文重点介绍将现有 v1 .NET 应用程序升级到适用于 Core (SQL) API 的 Azure Cosmos DB .NET SDK v2 时的一些注意事项。 Azure Cosmos DB .NET SDK v2 对应于 `Microsoft.Azure.DocumentDB` 命名空间。 如果要从以下任何 Azure Cosmos DB .NET 平台迁移应用程序以使用 v2 SDK `Microsoft.Azure.Cosmos`，则可以使用本文档中提供的信息：

* 适用于 SQL API 的 Azure Cosmos DB .NET Framework v1 SDK
* 适用于 SQL API 的 Azure Cosmos DB .NET Core SDK v1

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK 中提供了哪些功能

v2 SDK 包含许多可用性和性能改进，包括：

* 为非 Windows 客户端提供对 TCP 直接模式的支持
* 多区域写入支持
* 查询性能改进
* 支持地理空间/几何集合和索引编制
* 增加了对直接/TCP 传输诊断的改进
* 直接 TCP 传输堆栈更新，以减少建立的连接数
* 关于 RequestTimeout 中延迟减少的改进

大多数重试逻辑和较低级别的 SDK 基本保持不变。

## <a name="why-migrate-to-the-net-v2-sdk"></a>为何要迁移到 .NET v2 SDK

除了大量的性能改进之外，最新 SDK 中的新功能投资将不会反向移植到较旧版本。

此外，较旧的 SDK 将被较新的版本取代，而 v1 SDK 将进入[维护模式](sql-api-sdk-dotnet.md)。 为了获得最佳的开发体验，建议将应用程序迁移到更高版本的 SDK。

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>从 v1 SDK 到 v2 SDK 的重大更改

### <a name="direct-mode--tcp"></a>直接模式 + TCP

.NET v2 SDK 现在支持直接模式和网关模式。 直接模式支持通过 TCP 协议进行连接并提供更佳的性能，因为它直接连接到后端副本，且网络跃点较少。

有关更多详细信息，请仔细阅读 [Azure Cosmos DB SQL SDK 连接模式指南](sql-sdk-connection-modes.md)。

### <a name="session-token-formatting"></a>会话令牌格式

v2 SDK 不再使用 v1 中所使用的简单会话令牌格式，改为使用向量格式 。 由于格式不可互换，在传递给具有不同版本的客户端应用程序时应转换格式。

有关详细信息，请参阅 [.NET SDK 中的转换会话令牌格式](how-to-convert-session-token.md)。

### <a name="using-the-net-change-feed-processor-sdk"></a>使用 .NET 更改源处理器 SDK

.NET 更改源处理器库 2.1.x 需要 `Microsoft.Azure.DocumentDB` 2.0 或更高版本。

2\.1.x 库具有以下主要更改：

* 稳定性和诊断改进
* 改进了对错误和异常的处理
* 对分区租用集合的其他支持
* 用于实现 `ChangeFeedDocument` 接口和类的高级扩展，适用于其他错误处理和跟踪
* 添加了对使用自定义存储来持久存储每个分区的延续令牌的支持

有关详细信息，请参阅更改源处理器库[发行说明](sql-api-sdk-dotnet-changefeed.md)。

### <a name="using-the-bulk-executor-library"></a>使用批量执行工具库

v2 批量执行工具库当前对 Azure Cosmos DB .NET SDK 2.5.1 或更高版本具有依赖项。

有关详细信息，请参阅 [Azure Cosmos DB 批量执行工具库概述](bulk-executor-overview.md)和 .NET 批量执行工具库[发行说明](sql-api-sdk-bulk-executor-dot-net.md)。

## <a name="next-steps"></a>后续步骤

* 阅读有关使用适用于 SQL API v2 的 Azure Cosmos DB 来优化应用程序以实现最大性能的[其他性能提示](sql-api-get-started.md)
* 详细了解 [v2 SDK 的用途](sql-api-dotnet-samples.md)
* 正在尝试为迁移到 Azure Cosmos DB 进行容量计划？
    * 如果只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)