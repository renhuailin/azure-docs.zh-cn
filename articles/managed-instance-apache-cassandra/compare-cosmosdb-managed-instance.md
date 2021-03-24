---
title: Azure Managed Instance for Apache Cassandra 与 Azure Cosmos DB Cassandra API 之间的差别
description: 了解 Azure Managed Instance for Apache Cassandra 与 Azure Cosmos DB Cassandra API 之间的差别。 你还将了解每种服务的优势及其适用场景。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 3050dda4b3c0e1a05d751a4f5969bba69ad0506d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747472"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-preview-and-azure-cosmos-db-cassandra-api"></a>Azure Managed Instance for Apache Cassandra（预览版）与 Azure Cosmos DB Cassandra API 之间的差别 

在本文，你将了解 Azure Managed Instance for Apache Cassandra 与 Azure Cosmos DB Cassandra API 之间的差别。 本文提供了相关建议，指导如何在这两种服务之间进行选择或何时托管自己的 Apache Cassandra 环境。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="key-differences"></a>主要区别

Azure Managed Instance for Apache Cassandra 提供了自动部署、缩放和操作来维护 Azure 中开源 Apache Cassandra 实例的节点运行状况。 通过它，还可横向扩展现有本地或云自承载 Apache Cassandra 群集的容量。 它通过将托管的 Cassandra 数据中心添加到现有群集通道来进行横向扩展。

Azure Cosmos DB 中的 [Cassandra API](../cosmos-db/cassandra-introduction.md) 是 Microsoft 全球分布式云原生数据库服务 [Azure Cosmos DB](../cosmos-db/index.yml) 上的兼容性层。 Azure 中的这些服务相辅相成，为复杂混合云环境中的 Apache Cassandra 用户提供了一系列选择。

## <a name="how-to-choose"></a>如何选择？

下表显示了与每种部署方法匹配的常见方案、工作负载要求和期望：

| |本地或 Azure 中自承载的 Apache Cassandra | Azure Managed Instance for Apache Cassandra | Azure Cosmos DB Cassandra API |
|---------|---------|---------|---------|
|**部署类型**| 你具有高度自定义的 Apache Cassandra 部署，其中包含自定义的修补程序或报告。 | 你具有标准的开源 Apache Cassandra 部署，其中不包含任何自定义代码。 | 你满意的平台不是底层的 Apache Cassandra，而是与 [有线协议](../cosmos-db/cassandra-support.md)级别上的所有开源客户端驱动程序兼容的平台。 |
| **操作开销**| 当前有 Cassandra 专家可帮助你部署、配置和维护群集。  | 你想要降低 Apache Cassandra 节点运行状况的操作开销，但仍保持对复制和一致性等平台级别配置的控制。 | 你想要通过在云中使用完全托管的平台即服务数据库来消除操作开销。 |
| **操作系统要求**| 你需要维护虚拟机操作系统的自定义映像或黄金映像。 | 你可使用 vanilla 映像，但想要控制 SKU、内存、磁盘和 IOPS。 | 你想要简化容量预配并将其表示为单一规范化指标，并建立与吞吐量的一对一关系，例如 Azure Cosmos DB 中的[请求单位](../cosmos-db/request-units.md)。 |
| **定价模型**| 你想要使用 Datastax 工具等管理软件，并且你对许可成本感到满意。 | 你更喜欢纯开源许可和基于 VM 实例的定价。 | 你想要使用云原生定价，其包含[自动缩放](../cosmos-db/manage-scale-cassandra.md#use-autoscale)和[无服务器](../cosmos-db/serverless.md)产品/服务。 |
| **分析**| 你想要完全控制分析管道的预配，而不考虑生成和维护这些管道的开销。 | 你想要使用基于云的分析服务，例如 Azure Databricks。 | 你想要使用具有 [Azure Synapse Link for Cosmos DB](../cosmos-db/synapse-link.md) 的平台中内置的几乎实时的混合事务分析。 |
| **工作负载模式**| 你的工作负载处于相对稳定状态，你无需频繁缩放群集中的节点。 | 你的工作负载不稳定，你需要能够轻松纵向扩展或纵向缩减数据中心内的节点，或者能够轻松添加/删除数据中心。 | 你工作负载经常不稳定，你需要能够快速大规模地进行纵向扩展或纵向缩减。 |
| **SLA**| 你对 SLA 维护过程在一致性、吞吐量、可用性和灾难恢复上的表现感到满意。 | 你对 SLA 维护过程在一致性、吞吐量和可用性上的表现感到满意，但在备份方面需要帮助。 | 你在一致性、吞吐量、可用性和灾难恢复方面需要完整综合 SLA。 |

## <a name="next-steps"></a>后续步骤

通过以下快速入门之一来完成入门：

* [通过 Azure 门户创建托管实例群集](create-cluster-portal.md)