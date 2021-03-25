---
title: 适用于 Apache Cassandra 的 Azure 托管实例简介
description: 了解适用于 Apache Cassandra 的 Azure 托管实例。 此服务管理 Azure 中 Apache Cassandra 的原生开源实例的部署和缩放。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 03/02/2021
ms.openlocfilehash: d99c62e7d88510c351f87d85b4f8c5c271767cb3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101747420"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra-preview"></a>什么是适用于 Apache Cassandra 的 Azure 托管实例？ （预览版）

> [!IMPORTANT]
> 适用于 Apache Cassandra 的 Azure 托管实例当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

适用于 Apache Cassandra 的 Azure 托管实例服务为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作。 此服务有助于加速混合方案的部署，并减少日常维护。 在正式发布时，它将通过 [Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) 实现深度集成和数据移动功能。

:::image type="content" source="./media/introduction/icon.gif" alt-text="适用于 Apache Cassandra 的 Azure 托管实例是一种适用于 Apache Cassandra 的托管服务。" border="false":::

## <a name="key-benefits"></a>主要优点

### <a name="hybrid-deployments"></a>混合部署

可以使用此服务轻松地将 Apache Cassandra 数据中心托管实例（自动部署为虚拟机规模集）置于新的或现有的 Azure 虚拟网络中。 可以通过 Azure 或其他云环境中的 [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) 将这些数据中心添加到在本地运行的现有 Apache Cassandra 环。

- **简化的部署：** 混合连接建立后，可以通过 gossip 协议轻松完成部署。
- **托管指标：** 这些指标托管在 [Prometheus](https://prometheus.io/docs/introduction/overview/) 中，用于监视群集中的活动。

### <a name="simplified-scaling"></a>简化的缩放

在托管实例中，在数据中心内纵向扩展和纵向缩减节点的操作是完全托管的。 你输入所需节点的数目，而缩放业务流程协调程序则负责在 Cassandra 环中建立这些节点的操作。

### <a name="managed-and-cost-effective"></a>托管且经济高效

该服务为以下常见的 Apache Cassandra 任务提供管理操作：

- 设置群集
- 预配数据中心
- 缩放数据中心
- 删除数据中心
- 在密钥空间中启动修复操作
- 更改数据中心的配置
- 设置备份

定价模型是灵活、按需且基于实例的，没有许可费。 此定价模型允许你根据特定的工作负荷需求进行调整。 你可以选择所需的核心数、VM SKU、内存大小和磁盘空间大小。

## <a name="next-steps"></a>后续步骤

通过以下快速入门之一来完成入门：

* [通过 Azure 门户创建托管实例群集](create-cluster-portal.md)
* [使用 Azure Databricks 部署托管的 Apache Spark 群集](deploy-cluster-databricks.md)
* [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)
