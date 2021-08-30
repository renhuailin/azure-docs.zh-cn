---
title: 索引器对受保护资源的访问
titleSuffix: Azure Cognitive Search
description: 由 Azure 认知搜索中的索引器用来访问 Azure 数据的网络级安全选项的概念性概述。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: dc89bfcd3d89e6987c2c8b742f5fe2453c273fc7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746281"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features"></a>索引器访问受 Azure 网络安全功能保护的内容

在执行期间，Azure 认知搜索索引器可对各种 Azure 资源发出出站调用。 本文将解释索引器访问受 IP 防火墙、专用终结点或其他 Azure 网络级安全机制保护的内容的相关概念。 

如果出现以下两种情况，索引器会进行出站调用：

- 在索引期间连接到外部数据源
- 通过技能集连接到外部封装的代码

下表列出了索引器在典型的运行中可能访问的所有可能资源类型的列表。

| 资源 | 在索引器运行中的用途 |
| --- | --- |
| Azure 存储（Blob、表、ADLS Gen 2） | 数据源 |
| Azure 存储（Blob、表） | 技能组（缓存已扩充的文档，存储知识存储投影） |
| Azure Cosmos DB（各种 API） | 数据源 |
| Azure SQL 数据库 | 数据源 |
| Azure 虚拟机上的 SQL Server | 数据源 |
| SQL 托管实例 | 数据源 |
| Azure Functions | 自定义 Web API 技能的主机 |
| 认知服务 | 附加到用于对超过 20 个免费文档限制的扩充操作计费的技能组 |

> [!NOTE]
> 附加到技能组的认知服务资源用于根据执行的扩充操作以及写入到搜索索引的扩充结果计费。 它不用于访问认知服务 API。 从索引器的扩充管道访问认知服务 API 是通过内部安全信道进行的，其中的数据在传输中经过强加密，且永远不会静态存储。

客户可以通过 Azure 提供的多个网络隔离机制来保护这些资源。 索引器对除认知服务资源以外的其他资源的访问能力有限，即使这些资源已经过网络隔离，具体如下表中所述。

| 资源 | IP 限制 | 专用终结点 |
| --- | --- | ---- |
| Azure 存储（Blob、表、ADLS Gen 2） | 仅当存储帐户和搜索服务位于不同的区域时才受支持 | 支持 |
| Azure Cosmos DB - SQL API | 支持 | 支持 |
| Azure Cosmos DB - MongoDB 和 Gremlin API | 支持 | 不支持 |
| Azure SQL 数据库 | 支持 | 支持 |
| Azure 虚拟机上的 SQL Server | 支持 | 不适用 |
| SQL 托管实例 | 支持 | 空值 |
| Azure Functions | 支持 | 支持，但仅适用于特定的 Azure Functions 层 |

> [!NOTE]
> 除了上面列出的选项外，对于受网络保护的 Azure 存储帐户，客户还可以利用 Azure 认知搜索是[受信任的 Microsoft 服务](../storage/common/storage-network-security.md#trusted-microsoft-services)这一事实。 这意味着，如果对存储帐户启用了相应的基于角色的访问控制，则特定的搜索服务可以绕过针对存储帐户的虚拟网络或 IP 限制，并可以访问存储帐户中的数据。 有关详细信息，请参阅[使用受信任服务例外建立索引器连接](search-indexer-howto-access-trusted-service-exception.md)。 如果存储帐户或搜索服务无法迁移到不同的区域，可以利用此选项来替代 IP 限制路由。

选择安全访问机制时，请考虑以下约束：

- 索引器无法连接到[虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 仅支持通过具有凭据、专用终结点、受信任服务和 IP 寻址的公共终结点建立索引器连接。
- 搜索服务始终在云中运行，无法预配到在虚拟机上本机运行的特定虚拟网络。 Azure 认知搜索不提供此功能。
- 当索引器利用（出站）专用终结点访问资源时，可能会产生额外的[专用链接费](https://azure.microsoft.com/pricing/details/search/)。

## <a name="indexer-execution-environment"></a>索引器执行环境

Azure 认知搜索索引器能够有效地从数据源提取内容，将扩充内容添加到提取的内容，并在将结果写入搜索索引之前有选择性地生成投影。 根据分配给索引器的责任数量，索引器可在以下两个环境之一中运行：

- 供特定搜索服务专用的环境。 在此类环境中运行的索引器将与其他工作负载（例如客户启动的其他索引编制或查询工作负载）共享资源。 通常，只有执行基于文本的索引编制的索引器（例如，不使用技能组的索引器）才会在此环境中运行。

- 托管资源密集型索引器（例如，使用技能组的索引器）的多租户环境。 此环境用于卸载计算密集型处理，使服务特定的资源可用于日常操作。 此多租户环境由 Microsoft 管理和保护，不会向客户收取额外费用。

对于任何给定的索引器运行，Azure 认知搜索将确定用于运行索引器的最佳环境。 如果你正在使用 IP 防火墙控制对 Azure 资源的访问，了解执行环境将有助于设置涵盖这两个环境的 IP 范围。

## <a name="granting-access-to-indexer-ip-ranges"></a>向索引器 IP 范围授予访问权限

如果索引器尝试访问的资源仅限特定的 IP 范围集访问，则你需要扩展该集，以包括可从中发起索引器请求的可能性 IP 范围。 如前所述，可以在两个可能的环境中运行索引器和发起访问请求。 需要添加这两个环境的 IP 地址，索引器访问才能正常进行。

- 若要获取搜索服务特定的专用环境的 IP 地址，请对搜索服务的完全限定域名 (FQDN) 运行 `nslookup`（或 `ping`）。 例如，公有云中搜索服务的 FQDN 为 `<service-name>.search.windows.net`。 可以在 Azure 门户中获取此信息。
- 可通过 `AzureCognitiveSearch` 服务标记获取多租户环境的 IP 地址。 [Azure 服务标记](../virtual-network/service-tags-overview.md)提供每个服务的已发布 IP 地址范围 - 可以通过[发现 API（预览版）](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview)或[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)获取此范围。 无论使用哪种方法，IP 范围都是按区域划分的 - 只能选取分配给预配了你的搜索服务的区域的 IP 范围。

对于某些数据源，服务标记本身可直接使用，而无需枚举 IP 范围列表（仍需显式使用搜索服务的 IP 地址）。 这些数据源通过设置一个[网络安全组规则](../virtual-network/network-security-groups-overview.md)来限制访问。与 Azure 存储、Cosmos DB、Azure SQL 等提供的 IP 规则不同，该规则原生支持添加服务标记。 除了支持搜索服务 IP 地址外，还支持直接利用 `AzureCognitiveSearch` 服务标记的数据源包括：

- [Azure 虚拟机上的 SQL Server](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [SQL 托管实例](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

有关此连接选项的详细信息，请参阅[通过 IP 防火墙建立索引器连接](search-indexer-howto-access-ip-restricted.md)。

## <a name="granting-access-via-private-endpoints"></a>通过专用终结点授予访问权限

索引器可以利用[专用终结点](../private-link/private-endpoint-overview.md)来访问资源，对这些资源的访问被锁定，目的是选择虚拟网络或不启用任何公共访问。

此功能仅在可计费的搜索服务中可用，并且可创建的专用终结点数有限制。 有关详细信息，请参阅[服务限制](search-limits-quotas-capacity.md#shared-private-link-resource-limits)。

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>步骤 1：创建用于访问安全资源的专用终结点

客户应该对共享的专用链接资源调用搜索管理操作 [CreateOrUpdate API](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources/create-or-update)，以便与他们的安全资源（例如存储帐户）建立专用终结点连接。 通过此（出站）专用终结点连接传送的流量只会源自搜索服务特定的“专用”索引器执行环境中的虚拟网络。

Azure 认知搜索将验证此 API 的调用方是否拥有 Azure RBAC 权限，可以批准对安全资源的专用终结点连接请求。 例如，如果你使用只读权限请求与存储帐户建立专用终结点连接，此调用将被拒绝。

### <a name="step-2-approve-the-private-endpoint-connection"></a>步骤 2：批准专用终结点连接

当创建共享专用链接资源的（异步）操作完成时，将创建一个处于“挂起”状态的专用终结点连接。 暂时还没有任何流量流经该连接。

然后，客户需要查找并“批准”针对其安全资源的这种请求。 通常，可以通过 Azure 门户或 [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection) 执行此操作。

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>步骤 3：强制索引器在“专用”环境中运行

使用已批准的专用终结点，可以成功地从搜索服务对实施了某种形式的网络级访问限制的资源（例如，配置为只能从特定虚拟网络访问的存储帐户数据源）进行传出调用。

这意味着，任何能够通过专用终结点访问此类数据源的索引器都将成功。
如果专用终结点未经批准，或者索引器不利用专用终结点连接，则索引器运行最终会出现 `transientFailure`。

若要使索引器能够通过专用终结点连接访问资源，必须将索引器的 `executionEnvironment` 设置为 `"Private"`，确保所有索引器运行能够利用专用终结点。 这是因为，专用终结点是在搜索服务特定的专用环境中预配的。

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

[通过专用终结点建立索引器连接](search-indexer-howto-access-private.md)中更详细地介绍了这些步骤。
在你获得一个用于访问资源的已批准专用终结点后，设置为 private 的索引器将尝试通过专用终结点连接获取访问权限。

### <a name="limits"></a>限制

为确保搜索服务的最佳性能和稳定性，（搜索服务层级）已在以下维度实施了限制：

- 可设置为 private 的索引器类型。
- 可创建的共享专用链接资源数。
- 可为其创建共享专用链接资源的相异资源类型数。

[服务限制](search-limits-quotas-capacity.md)中阐述了这些限制。

## <a name="next-steps"></a>后续步骤

- [通过 IP 防火墙建立索引器连接](search-indexer-howto-access-ip-restricted.md)
- [使用受信任服务例外建立索引器连接](search-indexer-howto-access-trusted-service-exception.md)
- [索引器与专用终结点的连接](search-indexer-howto-access-private.md)