---
title: Azure 门户中关于 Azure Managed Instance for Apache Cassandra 的常见问题解答
description: 关于 Azure Managed Instance for Apache Cassandra 的常见问题解答。 本文解答有关托管实例适用场景、优势、吞吐量限制、支持的区域，以及其他配置详细信息的问题。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747435"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>关于 Azure Managed Instance for Apache Cassandra（预览版）的常见问题解答

本文解答有关 Azure Managed Instance for Apache Cassandra 的常见问题。 你将了解托管实例适用场景，以及其优势、吞吐量限制、支持的区域和配置详细信息。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="general-faq"></a>常见问题解答

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra 有哪些优势？

如果你需要可伸缩性和高可用性，而又不想影响性能，那么 Apache Cassandra 数据库是正确选择。 此平台在商用硬件或云基础结构上具备线性可伸缩性和经过验证的容错能力，非常适合任务关键型数据。 Azure Managed Instance for Apache Cassandra 是一项服务，可管理 Azure 中部署的开源 Apache Cassandra 数据中心的实例。

可单独在云中使用它，也可将其作为混合云和本地群集的一部分使用。 如果你希望在开源 Apache Cassandra 中进行细粒度的配置和控制，而又不产生任何维护开销，那么此服务是不错的选择。

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>为何该使用此服务，而不是 Azure Cosmos DB Cassandra API？

Azure Managed Instance for Apache Cassandra 由 Azure Cosmos DB 团队提供。 它是一项独立的托管服务，用于部署、维护和缩放开源 Apache Cassandra 数据中心和群集。 而另一方面，[Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md) 是一项平台即服务，为 Apache Cassandra 有线协议提供了互操作性层。 如果你希望平台的行为与所有 Apache Cassandra 群集的行为完全相同，那么应选择托管实例服务。 若要了解详细信息，请参阅 [Azure Managed Instance for Apache Cassandra 与 Azure Cosmos DB Cassandra API](compare-cosmosdb-managed-instance.md) 一文。

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Azure Managed Instance for Apache Cassandra 是否依赖于 Azure Cosmos DB？

否，Azure Managed Instance for Apache Cassandra 与 Azure Cosmos DB 后端之间不存在体系结构依赖关系。 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>是否可在任何区域部署 Azure Managed Instance for Apache Cassandra？

预览期间，托管实例将仅在部分区域中可用。

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra 在存储和吞吐量方面有哪些限制？

这些限制取决于你选择的虚拟机 SKU。

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra 的成本是多少？

托管实例根据基础 VM 成本收费，存在少量加价。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/)。

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>是否可使用 YAML 文件设置来配置行为？

是，可在部署 Azure 资源管理器模板期间嵌入 YAML 文件配置。

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>如何监视基础结构以及吞吐量？

[Prometheus](https://prometheus.io/docs/introduction/overview/) 服务器经过托管来监视整个群集中的活动，而且它会公开终结点。 这样会维护 10 分钟或 10 GB 数据（以先达到的阈值为准）。 若要使用此监视，需要设置[联合](https://prometheus.io/docs/prometheus/latest/federation/)和适当的仪表板工具，例如 Grafana。

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Azure Managed Instance for Apache Cassandra 是否提供完整备份？

是，它提供 Azure 存储的完整备份并还原到新群集

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>如何将数据从现有的 Apache Cassandra 群集迁移到 Azure Managed Instance for Apache Cassandra？

Azure Managed Instance for Apache Cassandra 支持 Apache Cassandra 中的所有功能，可在数据中心之间复制和流式传输数据。

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>是否可将本地 Apache Cassandra 群集与 Azure Managed Instance for Apache Cassandra 配对？

是，可在服务部署的 Azure 虚拟网络注入的数据中心内配置混合群集。 托管实例数据中心可与同一群集通道中的本地数据中心进行通信。

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>可在哪里就 Azure Managed Instance for Apache Cassandra 提供反馈？

请使用类别“托管 Apache Cassandra”通过[UserVoice 反馈](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548)提供反馈。

若要修复帐户问题，请在 Azure 门户中提交[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="deployment-specific-faq"></a>部署特定的常见问题解答

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>托管实例是否支持节点添加、群集状态和节点状态命令？

可通过 Azure CLI 使用所有只读 Nodetool 命令（例如 `status`）。 但节点添加等操作不可用，因为我们在托管实例中管理节点的运行状况。 在混合模式下，可使用 Nodetool 连接到群集。 但建议不要使用 Nodetool，因为它可能会破坏群集的稳定性。 它还可能会使与群集中托管实例数据中心的运行状况有关的任何生产支持 SLA 失效。

### <a name="what-happens-with-various-settings-for-table-metadata"></a>表元数据的各种设置会产生什么效果？

与任何自承载的 Apache Cassandra 环境一样，完全支持表元数据的设置，例如布隆筛选器、缓存、读修复概率、gc_grace 和压缩 memtable_flush_period。

## <a name="next-steps"></a>后续步骤

若要了解其他 API 中的常见问题，请参阅：

* [通过 Azure 门户创建托管实例群集](create-cluster-portal.md)
* [使用 Azure Databricks 部署托管的 Apache Spark 群集](deploy-cluster-databricks.md)
* [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)