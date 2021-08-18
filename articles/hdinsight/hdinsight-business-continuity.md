---
title: Azure HDInsight 业务连续性
description: 本文概述了 Azure HDInsight 业务连续性规划的最佳做法、单一区域可用性和优化选项。
keywords: hadoop 高可用性
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: e3cbae5df6e9a4e5ff17bf1245fcaf190efbbf84
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112371511"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight 业务连续性

Azure HDInsight 群集依赖于许多 Azure 服务，例如存储、数据库、Active Directory、Active Directory 域服务、网络和 Key Vault。 设计分析应用程序时，若要确保其设计良好、高度可用且可容错，则应让其有足够的冗余，以应对其中一项或多项服务出现区域中断或本地中断的情况。 本文概述了业务连续性规划的最佳做法、单一区域可用性和优化选项。

## <a name="general-best-practices"></a>一般最佳实践

本部分讨论了在业务连续性规划期间要考虑的一些最佳做法。

* 确定发生灾难时所需的最低业务功能以及原因。 例如，评估你是需要数据转换层（显示为黄色）和数据服务层（显示为蓝色）的故障转移功能，还是只需要数据服务层的故障转移功能。

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="数据转换层和数据服务层":::

* 根据工作负荷、开发生命周期和部门对你的群集进行划分。 具有更多群集可降低发生影响多个不同业务流程的单个大故障的几率。

* 使你的辅助区域成为只读的区域。 对同时启用了读取和写入功能的区域进行故障转移可能会导致复杂的体系结构。

* 发生灾难时，暂时性群集更易于管理。 设计你的工作负荷时使群集可以循环利用，并且不在群集中维护任何状态。

* 如果发生灾难，工作负荷通常会保持未完成状态，需要在新区域中重启。 将你的工作负荷设计为在本质上是幂等的。

* 在群集部署过程中使用自动化，并确保尽可能将群集配置设置脚本化，以确保在发生灾难时快速进行完全自动化的部署。

* 使用 HDInsight 上的 Azure 监视工具检测群集中的异常行为并设置相应的警报通知。 你可以部署预配置的、特定于 HDInsight 群集的管理解决方案，这些解决方案收集特定群集类型的重要性能指标。 有关详细信息，请参阅 [Azure HDInsight 监视](./hdinsight-hadoop-oms-log-analytics-tutorial.md)。  

* 订阅 Azure 运行状况警报，以获得有关订阅、服务或区域的服务问题、计划内维护、运行状况和安全建议的通知。 包含问题原因和解决方法 ETA 的运行状况通知可帮助你更好地执行故障转移和故障回复。 有关详细信息，请参阅 [Azure 服务运行状况文档](../service-health/index.yml)。

## <a name="single-region-availability"></a>单一区域可用性

基本 HDInsight 系统具有以下组件。 所有组件都有其自己的单一区域容错机制。

* 计算（虚拟机）：Azure HDInsight 群集
* 元存储：Azure SQL 数据库
* 存储：Azure Data Lake Gen2 或 Blob 存储
* 身份验证：Azure Active Directory、Azure Active Directory 域服务、企业安全性套餐
* 域名解析：Azure DNS

还可以使用其他可选服务，例如 Azure Key Vault 和 Azure 数据工厂。

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight 组件":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight 群集（计算）

HDInsight 提供 99.9% 的可用性 SLA。 为了在单个部署中提供高可用性，默认情况下，HDInsight 附带了许多处于高可用性模式的服务。 HDInsight 中的容错机制由 Microsoft 和 Apache OSS 生态系统高可用性服务提供。

以下服务设计为具有高可用性：

#### <a name="infrastructure"></a>基础结构

* 主动和备用头节点
* 多个网关节点
* 三个 Zookeeper 仲裁节点
* 按容错域和更新域分布的工作器节点

#### <a name="service"></a>服务

* Apache Ambari 服务器
* YARN 的应用程序时间线服务器
* 适用于 Hadoop MapReduce 的作业历史记录服务器
* Apache Livy
* HDFS
* YARN 资源管理器
* HBase Master

若要了解详细信息，请参阅 [Azure HDInsight 支持的高可用性服务](./hdinsight-high-availability-components.md)相关文档。

并非总是会发生影响业务功能的灾难性事件。 单个区域中一个或多个以下服务出现服务事件也可能会导致预期的业务功能丢失。

### <a name="hdinsight-metastore"></a>HDInsight 元存储

HDInsight 使用 [Azure SQL 数据库](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_4/)作为元存储，该元存储提供 99.99% 的 SLA。 数据的三个副本通过同步复制持久保存在数据中心内。 如果副本丢失，则可以无缝地提供备用副本。 现成支持[活动异地复制](../azure-sql/database/active-geo-replication-overview.md)，最多可使用四个数据中心。 如果通过手动方式或通过数据中心进行故障转移，则层次结构中的第一个副本将自动变为可读写的副本。 有关详细信息，请参阅 [Azure SQL 数据库业务连续性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)。

### <a name="hdinsight-storage"></a>HDInsight 存储

HDInsight 建议使用 Azure Data Lake Storage Gen2 作为基础存储层。 [Azure 存储](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)（包括 Azure Data Lake Storage Gen2）提供 99.9% 的 SLA。 HDInsight 使用 LRS 服务，其中有三个数据副本持久保存在一个数据中心内，复制是同步的。 发生副本丢失时，可以无缝地提供副本。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) 提供 99.9% 的 SLA。 Active Directory 是一项全局服务，具有多层内部冗余和自动可恢复性。 有关详细信息，请参阅 [Microsoft 如何持续提高 Azure Active Directory 的可靠性](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)。

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory 域服务 (AD DS)

[Azure Active Directory 域服务](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)提供 99.9% 的 SLA。 Azure AD DS 是在全球分布的数据中心内承载的高度可用的服务。 副本集是 Azure AD DS 中的一项预览版功能，当 Azure 区域脱机时，它可以实现地理灾难恢复。 有关详细信息，请参阅 [Azure Active Directory 域服务的副本集概念和功能](../active-directory-domain-services/concepts-replica-sets.md)。  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) 提供 100% 的 SLA。 HDInsight 使用不同位置的 Azure DNS 进行域名解析。

## <a name="multi-region-cost-and-complexity-optimizations"></a>多区域成本和复杂性优化

如果使用跨区域高可用性灾难恢复来提高业务连续性，则所需的体系结构设计复杂性更高且成本更高。 下表详细说明了一些可能会增加总拥有成本的技术领域。

### <a name="cost-optimizations"></a>成本优化

|区域|成本增加的原因|优化策略|
|----|------------------------|-----------------------|
|数据存储|在辅助区域中复制主数据/表|仅复制特选数据|
|数据流出|出站跨区域数据传输需要支出一定的成本。 查看带宽定价准则|请仅复制特选数据以减少区域数据流出量|
|群集计算|辅助区域中的其他 HDInsight 群集|在主计算失败后使用自动化脚本部署辅助计算。 使用自动缩放将辅助群集大小保持在最小值。 使用更便宜的 VM SKU。 在 VM SKU 可能会打折的区域中创建辅助群集。|
|身份验证 |辅助区域中的多用户方案将产生其他 Azure AD DS 设置|请避免在辅助区域中使用多用户设置。|

### <a name="complexity-optimizations"></a>复杂性优化

|区域|复杂性增加的原因|优化策略|
|----|------------------------|-----------------------|
|读取写入模式 |需要同时为主区域和辅助区域启用读取和写入 |将辅助区域设计为只读区域|
|零 RPO 和 RTO |要求零数据丢失 (RPO=0) 和零停机时间 (RTO=0) |以减少需要故障转移的组件数量的方式设计 RPO 和 RTO。|
|业务功能 |要求辅助区域具备主区域的完整业务功能 |评估是否可以在辅助区域中使用业务功能的最低限度的关键子集来运行。|
|连接 |要求主区域中的所有上游和下游系统也连接到辅助区域|将辅助连接限制为最低限度的关键子集。|

## <a name="next-steps"></a>后续步骤

若要详细了解本文中所述的项，请参阅：

* [Azure HDInsight 业务连续性体系结构](./hdinsight-business-continuity-architecture.md)
* [Azure HDInsight 高度可用的解决方案体系结构案例研究](./hdinsight-high-availability-case-study.md)
* [什么是 Azure HDInsight 中的 Apache Hive 和 HiveQL？](./hadoop/hdinsight-use-hive.md)
