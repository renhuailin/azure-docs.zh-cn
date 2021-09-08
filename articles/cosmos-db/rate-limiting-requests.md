---
title: 使用速率限制优化 Azure Cosmos DB 应用程序
description: 本文为开发人员提供一种限制对 Azure Cosmos DB 发送请求的速率的方法。 实现此模式可以减少错误，并提高超出了目标数据库或容器预配吞吐量的工作负载的总体性能。
author: plasne
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/26/2021
ms.author: pelasne
ms.openlocfilehash: 0677a3f4180ab429dc51a4e40c969e32e6e8f655
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098134"
---
# <a name="optimize-your-azure-cosmos-db-application-using-rate-limiting"></a>使用速率限制优化 Azure Cosmos DB 应用程序
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

本文为开发人员提供一种限制对 Azure Cosmos DB 发送请求的速率的方法。 实现此模式可以减少错误，并提高超出了目标数据库或容器预配吞吐量的工作负载的总体性能。

超出 Azure Cosmos DB 中预配吞吐量的请求可能会导致暂时性故障，例如 [TooManyRequests](troubleshoot-request-rate-too-large.md)、[Timeout](troubleshoot-request-timeout.md) 和 [ServiceUnavailable](troubleshoot-service-unavailable.md)。 通常，在有可用容量时你会重试这些请求，而且重试将会成功。 但是，此方法可能导致大量的请求在代码中的错误路径上缓冲，并且通常会导致吞吐量下降。

将客户端工作负载流量与服务器端预配吞吐量进行匹配，可以实现最佳系统性能（按照成本和时间来度量）。

假设出现了下面这种情景：

* 你在 Azure Cosmos DB 中预配了 20K RU/秒的吞吐量。
* 应用程序需要处理包含 10K 条记录的引入作业，处理其中的每条记录需要消耗 10 RU。 完成此作业所需的总容量为 100K RU。
* 如果将整个作业发送到 Azure Cosmos DB，预期会发生大量的暂时性故障，并且必须重试大量缓冲的请求。 之所以出现这种状况，是因为作业所需的 RU 总数 (100K) 远远超过了预配的上限 (20K)。 大约 2K 条记录将在数据库中接受，但大约 8K 条记录将被拒绝。 在重试时，你会将大约 8K 条记录发送到 Azure Cosmos DB，其中大约 2K 条记录将被接受，依此循环往复。 预期此模式会发送大约 30K 条记录，而不是 10K 条记录。
* 如果每隔 5 秒均匀发送这些请求，则预期不会出现任何错误，并且吞吐量在总体上更快，因为每一批消耗的 RU 不超过预配的 20K。

可以通过在代码中引入速率限制机制，在一段时间内分散发送请求。

为容器预配的 RU 将在一定数量的物理分区之间均匀共享。 在上述示例中，如果 Azure Cosmos DB 预配了两个物理分区，则其中每个分区将有 10K RU。

有关请求单位的详细信息，请参阅 [Azure Cosmos DB 中的请求单位](request-units.md)。
有关如何估算工作负载消耗的 RU 数的详细信息，请参阅[请求单位注意事项](request-units.md#request-unit-considerations)。
有关 Azure Cosmos DB 分区的详细信息，请参阅 [ Azure Cosmos DB 中的分区和横向缩放](partitioning-overview.md)。

## <a name="methodology"></a>方法

实现速率限制的方法类似于：

1. 分析应用程序，以获取有关所用的写入和读取请求的数据。
1. 定义所有索引。
1. 在集合中填充合理数量的数据（可以是样本数据）。 如果预期应用程序在一般情况下会包含数百万条记录，请在其中填充数百万条记录。
1. 编写有代表性的文档并记录 RU 成本。
1. 运行有代表性的查询并记录 RU 成本。
1. 在应用程序中实现一个函数，以根据结果确定任意给定请求的成本。
1. 在代码中实现速率限制机制，以确保在一秒内发送到 Azure Cosmos DB 的所有操作的总和不超过预配的吞吐量。
1. 对应用程序进行负载测试，并验证是否不超过预配的吞吐量。
1. 根据需要定期重新测试 RU 成本并更新成本函数。

## <a name="indexing"></a>索引

与你可能熟悉的其他 SQL 和 NoSQL 数据库不同，Azure Cosmos DB 针对新建容器的默认索引策略是为每个属性编制索引。 为每个属性编制索引会增大写入操作的 RU 成本。

默认索引策略可以降低读取密集型系统中的延迟，因为其中的查询筛选条件将合理分布在所有已存储的字段之间。 例如，如果系统中的 Azure Cosmos DB 花费大部分时间为最终用户创建的临时搜索提供服务，则此类系统可以受益于此策略。

你可能不希望为永不搜索的属性编制索引。 从索引中删除属性可以提高写入密集型系统的总体性能（成本和时间），并更严格地约束记录检索模式。

在度量任何成本之前，应该有意地根据用例配置相应的索引策略。 此外，如果以后更改索引，则需要重新运行所有成本计算。 

在可能的情况下，在正常和峰值需求条件下使用一个可以反映典型查询的负载来测试开发中的系统，将有助于确定要使用的索引策略。

有关索引的详细信息，请参阅 [Azure Cosmos DB 中的索引策略](index-policy.md)。

## <a name="measuring-cost"></a>度量成本

度量成本时需要理解一些重要概念：

* 根据[请求单位注意事项](request-units.md#request-unit-considerations)中所述，考虑影响 RU 使用量的所有因素。
* 数据库或容器中的所有读取和写入操作将共享相同的预配吞吐量。
* 无论使用哪个 Azure Cosmos DB API，都会消耗 RU。
* 集合的分区策略可能会对系统成本产生显著影响。 有关详细信息，请参阅 [Azure Cosmos DB 中的分区和水平缩放](partitioning-overview.md#choose-partitionkey)。
* 使用有代表性的文档和有代表性的查询。
  * 它们被视为与正常运行的系统所要处理的文档和查询最接近。
  * 获取这些有代表性的文档和查询的最佳方法就是检测应用程序的使用情况。 更好的做法始终是做出数据驱动的决策。
* 定期度量成本。
  * 索引更改和索引大小可能会影响成本。 
  * 针对有代表性的文档和查询创建某种可重复的（甚至自动化的）测试会很有帮助。
  * 确保有代表性的文档和查询仍具代表性。

用于确定请求成本的方法根据所用的每个 API 而异：

* [核心 API](find-request-unit-charge.md)
* [Cassandra API](cassandra/find-request-unit-charge-cassandra.md)
* [Gremlin API](find-request-unit-charge-gremlin.md)
* [Mongo DB API](mongodb/find-request-unit-charge-mongodb.md)
* [表 API](table/find-request-unit-charge.md)

## <a name="write-requests"></a>写入请求

写入操作的成本往往很容易预测。 插入记录并记下 Azure Cosmos 报告的成本即可。

如果你的文档具有不同的大小和/或使用不同的索引，则必须衡量所有这些因素。
你可能发现，有代表性的文档在成本上足够接近，以致可以为所有写入分配一个值。
例如，如果你发现成本分别为 13.14 RU、16.01 RU 和 12.63 RU，则可以取这些成本的平均值，即 14 RU。

## <a name="read-requests"></a>读取请求

查询操作的成本可能出于以下原因而更难预测：

* 如果系统支持用户定义的查询，则你需要将传入的查询映射到有代表性的查询，以帮助确定成本。 此过程可能采用多种形式：
  * 也许可以对查询进行精确匹配。 如果没有直接匹配项，则可能需要找出与它最接近的有代表性查询。
  * 你可能发现，可以根据查询的特征来计算成本。 例如，你可能发现，查询的每个子句产生了特定的成本，或者某个已编制索引的属性的成本为“x”，而某个未编制索引的属性的成本为“y”，等等。
* 结果数可能不同，除非你有统计信息，否则无法预测返回有效负载的 RU 影响。

查询操作有可能不会产生一种成本，而评估查询和计算成本的某个函数则有可能会产生一种成本。
如果使用的是 Core API，则可以评估操作的实际成本，并确定估算准确度（甚至可以在代码中自动优化这种估算）。

## <a name="transient-fault-handling"></a>暂时性故障处理

即使实现了速率限制机制，应用程序也仍会出于以下原因而需要进行暂时性故障处理：

* 请求的实际成本可能不同于预计成本。
* 暂时性故障可能由于 TooManyRequests 以外的原因发生。

但是，在应用程序中正确实现速率限制机制会大幅减少暂时性故障数量。

## <a name="alternate-and-related-techniques"></a>备用和相关技术

尽管本文介绍的是工作负载的客户端协调和批处理，但也可以采用其他技术来管理总体系统吞吐量。

### <a name="autoscaling"></a>自动缩放

通过自动缩放 Azure Cosmos DB 中预配的吞吐量，可以立即自动缩放数据库或容器的吞吐量 (RU/s)。 吞吐量会根据使用情况进行缩放，而不会影响工作负荷的可用性、延迟、吞吐量或性能。

自动缩放预配吞吐量非常适合于具有可变或不可预测流量模式，并且在高性能和规模方面需要 SLA 的任务关键型工作负载。

有关自动缩放的详细信息，请参阅[创建具有自动缩放吞吐量的 Azure Cosmos 容器和数据库](provision-throughput-autoscale.md)。

### <a name="queue-based-load-leveling-pattern"></a>基于队列的负载调节模式

可以采用一个队列在客户端与 Azure Cosmos DB 之间充当缓冲，从而缓解间歇性负载过大现象，否则会导致服务故障或任务超时。

此模式适用于其使用的服务可能发生过载的任何应用程序。 但是，如果应用程序需要尽量降低从服务收到响应时的延迟，则此模式不适用。

此模式通常很适合用于引入操作。

有关此模式的详细信息，请参阅[基于队列的负载调控模式](/azure/architecture/patterns/queue-based-load-leveling)。

### <a name="cache-aside-pattern"></a>缓存端模式

可以考虑按需将数据加载到缓存中，而不是每次都查询 Azure Cosmos DB。 使用缓存可以提高性能，并且有助于在缓存中保存的数据与基础数据存储中的数据之间保持一致性。

有关详细信息，请参阅：[缓存端模式](/azure/architecture/patterns/cache-aside)。

### <a name="materialized-view-pattern"></a>具体化视图模式

当未针对所需的查询操作完美设置数据的格式时，可以在 Azure Cosmos DB 中存储数据后，将视图预先填充到其他集合中。 此模式有助于支持高效查询和数据提取，并可提高应用程序性能。

有关详细信息，请参阅[具体化视图模式](/azure/architecture/patterns/materialized-view)。

## <a name="next-steps"></a>后续步骤

* 了解如何在 Azure Cosmos DB 中[排查 TooManyRequests 错误](troubleshoot-request-rate-too-large.md)。
* 了解如何在 Azure Cosmos DB 中[排查 Timeout 错误](troubleshoot-request-timeout.md)。
* 了解如何在 Azure Cosmos DB 中[排查 ServiceUnavailable 错误](troubleshoot-service-unavailable.md)。
* 详细了解 Azure Cosmos DB 中的[请求单位](request-units.md)。
* 详细了解 Azure Cosmos DB 中的[分区和横向缩放](partitioning-overview.md)。
* 了解 Azure Cosmos DB 中的[索引策略](index-policy.md)。
* 了解 Azure Cosmos DB 中的[自动缩放](provision-throughput-autoscale.md)。
* 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
