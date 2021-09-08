---
title: Azure Cosmos DB SQL SDK 连接模式
description: 了解 Azure Cosmos DB SQL SDK 上提供的不同的连接模式。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/26/2021
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: 645093cc63f351cc196e7d90812e6bc430259eed
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030913"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB SQL SDK 连接模式
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

客户端连接到 Azure Cosmos DB 的方式对性能有重大影响，尤其是在观察到的客户端延迟方面。 Azure Cosmos DB 提供基于 HTTPS 的简单开放式 RESTful 编程模型（称为网关模式）。 此外，它还提供了高效的 TCP 协议，该协议在其通信模型中也是 RESTful，并使用 TLS 进行初始身份验证和加密流量（称为直接模式）。

## <a name="available-connectivity-modes"></a>可用的连接模式

两种可用的连接模式为：

  * 网关模式
      
    可在任意 SDK 平台上使用网关模式。 如果应用程序在有严格防火墙限制的企业网络中运行，则网关模式是最佳选择，因为它使用标准 HTTPS 端口与单个 DNS 终结点。 但是，对于性能的影响是：每次在 Azure Cosmos DB 中读取或写入数据时，网关模式都涉及到额外的网络跃点。 在套接字连接数量有限的环境中运行应用程序时，我们也建议使用网关连接模式。

    在 Azure Functions 中使用 SDK 时，尤其是在[消耗计划](../azure-functions/consumption-plan.md)中使用时，请注意当前的[连接限制](../azure-functions/manage-connections.md)。

  * 直接模式

    直接模式支持通过 TCP 协议进行连接，由于网络跃点更少，因此性能更好。 应用程序直接连接到后端副本。 直接模式目前仅在 .NET 和 Java SDK 平台上受支持。
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos SDK 连接模式" border="false":::

这些连接模式实质上限制了数据平面请求（文档读取和写入）从客户端计算机到 Azure Cosmos DB 后端中的分区的路由方式。 直接模式是获得最佳性能的首选选项，它允许客户端直接与 Azure Cosmos DB 后端中的分区建立 TCP 连接，并且在不通过中介的情况下直接发送请求。 与之相反，在网关模式下，客户端发出的请求会路由到 Azure Cosmos DB 前端中所谓的“网关”服务器，该服务器接下来会将你的请求扇出到 Azure Cosmos DB 后端的相应分区。

## <a name="service-port-ranges"></a>服务端口范围

使用直接模式时，除了网关端口外，还需确保 10000 到 20000 这个范围的端口处于打开状态，因为 Azure Cosmos DB 使用动态 TCP 端口。 当在[专用终结点](./how-to-configure-private-endpoints.md)上使用直接模式时，应打开从 0 到 65535 的整个 TCP 端口范围。 如果这些端口未处于打开状态，你可能会在尝试使用 TCP 协议时收到“503 服务不可用”错误。

下表显示了可用于各种 API 的连接模式以及用于每个 API 的服务端口的摘要：

|连接模式  |支持的协议  |支持的 SDK  |API/服务端口  |
|---------|---------|---------|---------|
|网关  |   HTTPS    |  所有 SDK    |   SQL (443)、MongoDB（10250、10255、10256）、表 (443)、Cassandra (10350)、Graph (443) <br> 端口 10250 映射到没有异地复制功能的默认 Azure Cosmos DB API for MongoDB 实例。 而端口 10255 和 10256 映射到具有异地复制功能的实例。   |
|直接    |     TCP    |  .NET SDK Java SDK    | 使用公共/服务终结点时：端口介于 10000 到 20000 之间<br>使用专用终结点时：端口介于 0 到 65535 之间 |

## <a name="next-steps"></a>后续步骤

对于特定 SDK 平台性能优化：

* [.NET V2 SDK 性能提示](performance-tips.md)

* [.NET V3 SDK 性能提示](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Java V4 SDK 性能提示](performance-tips-java-sdk-v4-sql.md)

* 尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量规划。
    * 如果你只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
    * 如果知道当前数据库工作负荷的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)