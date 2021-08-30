---
title: Azure Cosmos DB 专用网关
description: 专用网关是用作 Azure Cosmos DB 帐户前端的计算。 当你连接到专用网关时，它会路由请求并缓存数据。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: tisande
ms.openlocfilehash: 7cc6acc29cf8019bf65d3495cfd8a00a87da11bc
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112914240"
---
# <a name="azure-cosmos-db-dedicated-gateway---overview-preview"></a>Azure Cosmos DB 专用网关 - 概述（预览版）
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

专用网关是用作 Azure Cosmos DB 帐户前端的服务器端计算。 当你连接到专用网关时，它会路由请求并缓存数据。 和预配的吞吐量一样，专用网关按小时计费。

## <a name="overview"></a>概述

可以预配专用网关来大幅提高性能。 预配专用网关的最常见原因是为了能够缓存。 预配专用网关时，会自动在该专用网关中配置[集成式缓存](integrated-cache.md)。 命中集成式缓存的点读取和查询不会使用任何 RU。 预配具有集成式缓存的专用网关有助于降低 Azure Cosmos DB 上读取密集型工作负载的成本。

专用网关内置于 Azure Cosmos DB 中。 [预配专用网关](how-to-configure-integrated-cache.md)时，你将获得一个完全托管的节点，该节点可将请求路由到后端分区。 与通过标准网关连接到 Azure Cosmos DB 相比，通过专用网关连接到 Azure Cosmos DB 的延迟更低且更容易预测。 即使发生缓存未命中，专用网关的延迟也要优于标准网关。

只需对代码做出极少量的更改，就能让应用程序使用专用网关。 新的和现有的 Azure Cosmos DB 帐户都可以预配专用网关来提高读取性能。

> [!NOTE]
> 是否对专用网关有任何反馈？ 我们想听一听！ 欢迎直接与 Azure Cosmos DB 工程团队分享反馈：cosmoscachefeedback@microsoft.com

## <a name="connection-modes"></a>连接模式

可通过三种方式连接到 Azure Cosmos DB 帐户：

- [直接模式](#connect-to-azure-cosmos-db-using-direct-mode)
- [使用标准网关的网关模式](#connect-to-azure-cosmos-db-using-gateway-mode)
- [使用专用网关的网关模式](#connect-to-azure-cosmos-db-using-the-dedicated-gateway)（仅适用于 SQL API 帐户）

### <a name="connect-to-azure-cosmos-db-using-direct-mode"></a>使用直接模式连接到 Azure Cosmos DB

当你使用直接模式连接到 Azure Cosmos DB 时，应用程序将直接连接到 Azure Cosmos DB 后端。 即使你有许多物理分区，请求路由也完全在客户端进行处理。 直接模式的延迟较低，因为应用程序可以直接与 Azure Cosmos DB 后端通信，而无需中间网络跃点。

直接模式连接的图形表示形式：

:::image type="content" source="./media/dedicated-gateway/direct-mode.png" alt-text="显示 Cosmos DB 直接模式工作原理的插图" border="false":::

### <a name="connect-to-azure-cosmos-db-using-gateway-mode"></a>使用网关模式连接到 Azure Cosmos DB

如果你使用网关模式连接到 Azure Cosmos DB，则应用程序将先连接到前端节点，该节点会处理将请求路由到相应后端节点的过程。 由于网关模式涉及到一个额外的网络跃点，因此与直接模式相比，其延迟可能略高。 

通过网关模式连接到 Azure Cosmos DB 时，可使用以下任一选项进行连接：

* 标准网关 - 虽然具有预配吞吐量和存储的后端为每个容器提供专用容量，但标准网关是在许多 Azure Cosmos 帐户之间共享的。 对于许多客户而言，共享标准网关是切实可行的，因为每个客户消耗的计算资源很小。
* 专用网关 - 在此网关中，后端和网关都有专用容量。 集成式缓存需要专用网关，因为它需要大量特定于 Azure Cosmos 帐户的 CPU 和内存。

### <a name="connect-to-azure-cosmos-db-using-the-dedicated-gateway"></a>使用专用网关连接到 Azure Cosmos DB

必须通过专用网关连接到 Azure Cosmos DB 才能使用集成式缓存。 专用网关有一个与 Azure Cosmos DB 帐户中提供的标准终结点不同的终结点。 当你连接到专用网关终结点时，应用程序会将请求发送到专用网关，然后该网关会将请求路由到不同的后端节点。 如果可能，集成式缓存将提供结果。

使用专用网关进行网关模式连接的示意图：

:::image type="content" source="./media/dedicated-gateway/dedicated-gateway-mode.png" alt-text="显示 Cosmos DB 专用网关工作原理的插图" border="false":::
 
## <a name="provisioning-the-dedicated-gateway"></a>预配专用网关

可以在 Core (SQL) API 帐户中预配专用网关群集。 一个专用网关群集最多可以有五个节点，你可以随时添加或删除节点。 帐户中的所有专用网关节点[共享同一个连接字符串](how-to-configure-integrated-cache.md#configuring-the-integrated-cache)。

专用网关节点彼此独立。 预配多个专用网关节点时，任一节点都可以路由任一给定请求。 此外，每个节点的缓存都独立于其他节点的缓存。 每个节点中缓存的数据取决于最近通过该特定节点[写入或读取](integrated-cache.md#item-cache)的数据。 换句话说，如果在一个节点上缓存了某个项或查询，不一定会在其他节点上缓存该项或查询。

对于开发，我们建议从一个节点开始，但对于生产，应预配三个或更多个节点以实现高可用性。 [了解如何预配具有集成式缓存的专用网关群集](how-to-configure-integrated-cache.md)。 预配多个专用网关节点可使专用网关群集持续路由请求并提供缓存的数据，即使某个专用网关节点不可用，也是如此。

由于专用网关是以公共预览版提供的，因此不附带可用性 SLA。 但一般情况下，它的可用性应与 Azure Cosmos DB 帐户的其他功能相当。

专用网关按以下大小提供：

| SKU 名称 | **vCPU** | **内存**  |
| ------------ | -------- | ----------- |
| D4s      | **4**    | **16 GB** |
| D8s      | **8**    | **32 GB** |
| D16s     | **16**   | **64 GB** |

> [!NOTE]
> 创建专用网关节点后无法修改其大小。 但可以添加或删除节点。

## <a name="dedicated-gateway-in-multi-region-accounts"></a>多区域帐户中的专用网关

在多区域帐户中预配专用网关群集时，将在每个区域中预配相同的专用网关群集。 例如，假设在美国东部和北欧有一个 Azure Cosmos DB 帐户。 如果在此帐户中预配包含两个 D8 节点的专用网关群集，则总共会有四个 D8 节点 - 两个位于美国东部，两个位于北欧。 无需在每个区域中显式配置专用网关，并且连接字符串将保持不变。 有关执行故障转移的最佳做法也没有任何变化。

> [!NOTE]
> 无法在启用了可用性区域的帐户中预配专用网关群集

与群集中的节点一样，跨区域的专用网关节点也是独立的。 每个区域中缓存的数据有可能不同，具体取决于最近在该区域中的读取或写入。

## <a name="limitations"></a>限制

公共预览版的专用网关存在以下限制：

- 专用网关仅在 SQL API 帐户中受支持。
- 无法在配置了 [IP 防火墙](how-to-configure-firewall.md)或[专用链接](how-to-configure-private-endpoints.md)的 Azure Cosmos DB 帐户中预配专用网关。
- 无法在启用了[可用性区域](high-availability.md#availability-zone-support)的 Azure Cosmos DB 帐户中预配专用网关。

## <a name="next-steps"></a>后续步骤

请参阅以下文章来详细了解专用网关的用法：

- [集成式缓存](integrated-cache.md)
- [配置集成式缓存](how-to-configure-integrated-cache.md)
- [集成式缓存常见问题解答](integrated-cache-faq.md)