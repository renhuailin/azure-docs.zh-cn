---
title: '迁移你的应用程序以使用 Azure Cosmos DB .NET SDK 2.0 (Cosmos) '
description: 了解如何将现有 .NET 应用程序从 v1 SDK 升级到 .NET SDK v2 for Core (SQL) API。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550052"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>迁移你的应用程序以使用 Azure Cosmos DB .NET SDK v2

> [!IMPORTANT]
> 请务必注意，.NET SDK 的 v3 当前可用，并在 [此处](migrate-dotnet-v3.md)提供了 v2 到 v3 的迁移计划。 要了解 Azure Cosmos DB .NET SDK v2，请参阅 [发行说明](sql-api-sdk-dotnet.md)， [.net GitHub 存储库](https://github.com/Azure/azure-cosmos-dotnet-v2)，.Net SDK V2 [性能提示](performance-tips.md)和 [故障排除指南](troubleshoot-dot-net-sdk.md)。
>

本文重点介绍将现有 v1 .NET 应用程序升级到 (SQL) API Azure Cosmos DB .NET SDK v2 的一些注意事项。 Azure Cosmos DB .NET SDK v2 对应于 `Microsoft.Azure.DocumentDB` 命名空间。 如果要将应用程序从以下任一 Azure Cosmos DB .NET 平台迁移到使用 v2 SDK，则可以使用本文档中提供的信息 `Microsoft.Azure.Cosmos` ：

* 适用于 SQL API 的 .NET Framework v1 SDK Azure Cosmos DB
* 适用于 SQL API 的 .NET Core SDK v1 Azure Cosmos DB

## <a name="whats-available-in-the-net-v2-sdk"></a>.NET v2 SDK 中提供了哪些功能

V2 SDK 包含许多可用性和性能改进，包括：

* 支持非 Windows 客户端的 TCP 直接模式
* 多区域写入支持
* 查询性能改进
* 支持地理空间/几何集合和索引编制
* 增强了直接/TCP 传输诊断的改进
* 直接 TCP 传输堆栈上用于减少已建立连接数的更新
* RequestTimeout 中减少延迟的改进

大多数重试逻辑和更低的 SDK 级别在很大程度上保持不变。

## <a name="why-migrate-to-the-net-v2-sdk"></a>迁移到 .NET v2 SDK 的原因

除了众多性能改进之外，最新 SDK 中的新功能投资也不会退回到较旧版本。

此外，较旧的 Sdk 将替换为较新的版本，v1 SDK 将进入 [维护模式](sql-api-sdk-dotnet.md)。 为了获得最佳的开发体验，我们建议将应用程序迁移到更高版本的 SDK。

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>从 v1 SDK 到 v2 SDK 的重大更改

### <a name="direct-mode--tcp"></a>直接模式 + TCP

.NET v2 SDK 现在支持直接模式和网关模式。 直接模式支持通过 TCP 协议连接并提供更好的性能，因为它直接连接到具有较少网络跃点的后端副本。

有关更多详细信息，请参阅 [AZURE COSMOS DB SQL SDK 连接模式指南](sql-sdk-connection-modes.md)。

### <a name="session-token-formatting"></a>会话令牌格式

V2 SDK 不再使用 v1 中使用的 *简单* 会话令牌格式，而是 sdk 使用 *矢量* 格式。 在将格式传递到具有不同版本的客户端应用程序时，应转换格式，因为格式不能互换。

有关详细信息，请参阅 [在 .NET SDK 中转换会话令牌格式](how-to-convert-session-token.md)。

### <a name="using-the-net-change-feed-processor-sdk"></a>使用 .NET 更改源处理器 SDK

.NET 更改源处理器库 2.1. x 需要 `Microsoft.Azure.DocumentDB` 2.0 或更高版本。

2.1. x 库具有以下关键更改：

* 稳定性和诊断改进
* 改进的错误和异常处理
* 对分区租约集合的其他支持
* 用于实现 `ChangeFeedDocument` 接口和类以进行其他错误处理和跟踪的高级扩展插件
* 添加了对使用自定义存储保留每个分区的继续标记的支持

有关详细信息，请参阅更改源处理器库的 [发行说明](sql-api-sdk-dotnet-changefeed.md)。

### <a name="using-the-bulk-executor-library"></a>使用大容量执行器库

V2 大容量执行器库当前依赖于 Azure Cosmos DB .NET SDK 2.5.1 或更高版本。

有关详细信息，请参阅 [Azure Cosmos DB 批量执行器库概述](bulk-executor-overview.md) 和 .net 批量执行程序库 [发行说明](sql-api-sdk-bulk-executor-dot-net.md)。

## <a name="next-steps"></a>后续步骤

* 阅读有关使用 SQL API v2 Azure Cosmos DB 的 [其他性能技巧](sql-api-get-started.md) ，以便优化应用程序以实现最大性能
* 了解有关如何[通过 V2 SDK 执行的操作的](sql-api-dotnet-samples.md)详细信息