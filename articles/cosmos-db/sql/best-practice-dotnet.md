---
title: 适用于 .NET SDK v3 的 Azure Cosmos DB 最佳做法
description: 了解使用 Azure Cosmos DB .NET SDK v3 的最佳做法
author: StefArroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: esarroyo
ms.openlocfilehash: b07162b95419a73be4bdecc5ff3160ded1c2a0e5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219621"
---
# <a name="best-practices-for-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK 的最佳做法
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文演示使用 Azure Cosmos DB .NET SDK 的最佳做法。 遵循这些做法将有助于改善延迟、提高可用性并提升整体性能。 

观看下面的视频，听 Cosmos DB 工程师详细讲解如何使用 .NET SDK！


> [!VIDEO https://www.youtube.com/embed/McZIQhZpvew?start=118]
>

## <a name="checklist"></a>清单
|已选中  | 主题  |详细信息/链接  |
|---------|---------|---------|
|<input type="checkbox"/> |    SDK 版本    |   始终使用提供的[最新版本](sql-api-sdk-dotnet-standard.md) Cosmos DB SDK，以获得最佳性能。     |
| <input type="checkbox"/>   |    单一实例客户端     |       在应用程序的生存期内使用 `CosmosClient` 的[单个实例](/dotnet/api/microsoft.azure.cosmos.cosmosclient?view=azure-dotnet&preserve-view=true)，以获得[更好的性能](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)。     |
| <input type="checkbox"/>  |     区域     |   确保在与 Azure Cosmos DB 帐户相同的 [Azure 区域](../distribute-data-globally.md)中运行应用程序，以尽可能减少延迟。 启用 2-4 个区域并在多个区域中复制帐户以获得[最佳可用性](../distribute-data-globally.md)。 对于生产工作负载，请启用[自动故障转移](../how-to-manage-database-account.md#configure-multiple-write-regions)。 如果不存在此配置，则帐户在写入区域中断的整个持续时间内会丢失写入可用性，因为手动故障转移会由于缺少区域连接而无法成功。 若要了解如何使用 .NET SDK 来添加多个区域，请访问[此处](tutorial-global-distribution-sql-api.md)   |
| <input type="checkbox"/>   |   可用性和故障转移     |  使用[首选区域列表](./tutorial-global-distribution-sql-api.md?tabs=dotnetv3%2capi-async#preferred-locations)在 v3 SDK 中设置 [ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions?view=azure-dotnet&preserve-view=true) 或 [ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion?view=azure-dotnet&preserve-view=true)，在 v2 SDK 中设置 [PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet&preserve-view=true)。 在故障转移期间，系统会将写入操作发送到当前写入区域，并将所有读取操作发送到首选区域列表中的第一个区域。 有关区域故障转移机制的详细信息，请参阅[可用性问题排查指南](troubleshoot-sdk-availability.md)。 |
|  <input type="checkbox"/> |    CPU     |  由于客户端计算机上的资源不足，你可能会遇到连接/可用性问题。 监视运行 Azure Cosmos DB 客户端的节点上的 CPU 利用率，并在使用率非常高的情况下纵向/横向扩展。      |
| <input type="checkbox"/>   |    Hosting      |   尽可能使用 [Windows 64 位主机](performance-tips.md#hosting)处理以获得最佳性能。       |
| <input type="checkbox"/> |    连接模式    |    使用[直接模式](sql-sdk-connection-modes.md)以获得最佳性能。  有关如何执行此操作的说明，请参阅 [V3 SDK 文档](performance-tips-dotnet-sdk-v3-sql.md#networking)或 [V2 SDK 文档](performance-tips.md#networking)。|
|<input type="checkbox"/>  |    网络   | 如果使用虚拟机来运行应用程序，请在 VM 上启用[加速网络](../../virtual-network/create-vm-accelerated-networking-powershell.md)，以帮助解决流量较大所致的瓶颈问题，并减少延迟或 CPU 抖动。 你可能还需要考虑使用最大 CPU 使用率低于 70% 的更高端虚拟机。    |
|<input type="checkbox"/> |  临时端口耗尽      | 对于稀疏或偶发性连接，将 [`IdleConnectionTimeout`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.idletcpconnectiontimeout?view=azure-dotnet&preserve-view=true) 和 [`PortReuseMode`](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.portreusemode?view=azure-dotnet&preserve-view=true) 设置为 `PrivatePortPool`。 `IdleConnectionTimeout` 属性可帮助控制未使用连接的关闭时间。 这将减少未使用的连接数。 默认情况下，空闲连接会无限期地保持为打开状态。 设置的值必须大于或等于 10 分钟。 建议值介于 20 到 24 小时之间。  `PortReuseMode` 属性使 SDK 可以针对各种 Azure Cosmos DB 目标终结点使用一小部分临时端口。    |
|<input type="checkbox"/> |  使用 Async/Await     |  避免阻止调用：`Task.Result`、`Task.Wait` 和 `Task.GetAwaiter().GetResult()`。 为了获益于 [async/await](/dotnet/csharp/programming-guide/concepts/async/) 模式，整个调用堆栈都是异步的。 许多同步阻止调用都会导致[线程池饥饿](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall)和响应时间降低。 |
|<input type="checkbox"/>  |   端到端超时 | 若要获取端到端超时，需要同时使用 `RequestTimeout` 和 `CancellationToken` 参数。 有关 Cosmos DB 超时的更多详细信息，请[访问](troubleshoot-dot-net-sdk-request-timeout.md) |
|<input type="checkbox"/>  |   重试逻辑      |   暂时性错误是指根本原因很快就能自行解决的错误。 连接到你的数据库的应用程序应当构建为能预见这些暂时性错误。 为了处理这些错误，可在代码中实现重试逻辑，而不是将它们以应用程序错误的形式呈现给用户。 SDK 提供内置逻辑来处理可重试请求（如读取或查询操作）的这些暂时性故障。 由于写入操作不是幂等的，因此 SDK 不会针对暂时性故障重试写入。 SDK 允许用户为限制配置重试逻辑。 有关重试哪些错误的详细信息，请[访问](troubleshoot-dot-net-sdk.md#retry-logics) |
|<input type="checkbox"/>  |     缓存数据库/集合名称    |    从配置中检索数据库和容器的名称，或者一开始就将其缓存。 `ReadDatabaseAsync` 或 `ReadDocumentCollectionAsync` 以及 `CreateDatabaseQuery` 或 `CreateDocumentCollectionQuery` 之类的调用将导致对服务的元数据调用，这些调用使用系统预留的 RU 限制。 还应该只使用一次 `CreateIfNotExist` 来设置数据库。 总体而言，不应频繁执行这些操作。       |
|<input type="checkbox"/> |     批量支持      |     在可能不需要针对延迟进行优化的情况下，建议为转储大量数据启用[批量支持](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/)。    |
| <input type="checkbox"/>  |     并行查询     |    Cosmos DB SDK 支持[并行运行查询](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)，以在查询时改善延迟和提高吞吐量。  建议将 `QueryRequestsOptions` 中的 `MaxConcurrency` 属性设置为你拥有的分区数。 如果不知道分区数，请开始使用 `int.MaxValue`，这将为你提供最佳延迟。 然后减少此数值，直至符合环境的资源限制，以避免高 CPU 问题。 另外，将 `MaxBufferedItemCount` 设置为预期返回的结果数，以限制预提取结果的数目。 |
| <input type="checkbox"/> |     性能测试回退      |    对应用程序执行测试时，应该按 [`RetryAfter`](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage) 间隔实现回退。 允许退让有助于确保最大程度地减少等待重试的时间。   |
|  <input type="checkbox"/>   |   索引     |   Azure Cosmos DB 的索引策略还允许使用索引路径（IndexingPolicy.IncludedPaths 和 IndexingPolicy.ExcludedPaths）指定要在索引中包括或排除的文档路径。  确保从索引中排除未使用的路径以加快写入速度。  有关如何使用 SDK 来创建索引的示例，请[访问](performance-tips-dotnet-sdk-v3-sql.md#indexing-policy)   |
|  <input type="checkbox"/>   |    文档大小  |    指定操作的请求费用与文档大小直接相关。 建议减小文档的大小，因为对大型文档执行操作比对小型文档执行操作成本更高。      |
| <input type="checkbox"/>   |     增加线程/任务数目    |    由于对 Azure Cosmos DB 的调用是通过网络执行的，可能需要改变请求的并发度，以便最大程度地减少客户端应用程序等待请求的时间。 例如，如果使用 [.NET 任务并行库](/dotnet/standard/parallel-programming/task-parallel-library-tpl)，请创建数百个读取或写入 Azure Cosmos DB 的任务。     |
|  <input type="checkbox"/> |    启用查询指标     |  对于后端查询执行的其他日志记录，可以使用 .NET SDK 启用 SQL 查询指标。 有关如何收集 SQL 查询指标的说明，请[访问](profile-sql-api-query.md)    |
|  <input type="checkbox"/>    | SDK 日志记录   | 使用 SDK 日志记录来捕获其他诊断信息并排查延迟问题。  记录 v2 SDK 中的[诊断字符串](/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet&preserve-view=true)或 v3 SDK 中的 [`Diagnostics`](/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet&preserve-view=true)，以获取对服务的当前请求的更详细 cosmos 诊断信息。 对于示例用例，如果 `Diagnostics.ElapsedTime` 大于指定的阈值，则捕获任何异常和已完成操作的诊断（即，如果 SLA 为 10 秒，则在 `ElapsedTime` > 10 秒时捕获诊断）。 建议仅在性能测试期间使用这些诊断。     |

## <a name="best-practices-when-using-gateway-mode"></a>使用网关模式时的最佳做法
使用网关模式时，增加每台主机的 `System.Net MaxConnections`。 使用网关模式时，Azure Cosmos DB 请求是通过 HTTPS/REST 发出的。 这些请求受制于每个主机名或 IP 地址的默认连接限制。 可能需要将 `MaxConnections` 设置为较大的值（从 100 到 1,000），以便客户端库能够同时使用多个连接来访问 Azure Cosmos DB。 在 .NET SDK 1.8.0 及更高版本中，`ServicePointManager.DefaultConnectionLimit` 的默认值为 50。 若要更改该值，可以将 `Documents.Client.ConnectionPolicy.MaxConnectionLimit` 设置为较高的值。

## <a name="best-practices-for-write-heavy-workloads"></a>具有大量写入操作的工作负载的最佳做法
对于具有大量创建有效负载的工作负荷，请将 `EnableContentResponseOnWrite` 请求选项设置为 `false`。 该服务将不再将创建或更新的资源返回给 SDK。 通常，因为应用程序具有正在创建的对象，因此不需要服务即可将其返回。 标头值仍可访问，例如请求费用。 禁用内容响应有助于提高性能，因为 SDK 不再需要分配内存或序列化响应正文。 此外还会降低网络带宽的使用率，从而进一步提高性能。

## <a name="next-steps"></a>后续步骤
如果需要使用一个示例应用程序来评估 Azure Cosmos DB，以便在数个客户端计算机上实现高性能方案，请参阅[使用 Azure Cosmos DB 进行性能和缩放测试](performance-testing.md)。

若要深入了解如何设计应用程序以实现缩放和高性能，请参阅 [Azure Cosmos DB 中的分区和缩放](../partitioning-overview.md)。

正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
* 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
