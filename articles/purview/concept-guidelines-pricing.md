---
title: Purview 定价准则
description: 本文将提供有关了解 Purview 定价中各种组件的准则。
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: conceptual
ms.date: 10/03/2021
ms.openlocfilehash: 393414ad1d2c50f2a3bb76ce045614e19b50f928
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534065"
---
# <a name="azure-purview-pricing"></a>Azure Purview 定价   

Azure Purview 通过提供用于管理数据治理的单一虚拟管理平台实现统一的治理体验，从而实现大规模自动扫描和分类数据。


## <a name="intended-audience"></a>目标受众

- 数据治理和管理团队

## <a name="why-do-you-need-to-understand-the-components-of-the-azure-purview-pricing"></a>为什么需要了解 Azure Purview 定价的组件？ 

- 虽然 Azure Purview 定价基于订阅的“即用即付”模型，但提供涉及 Purview 的预算时可以考虑多种维度
- 本准则旨在通过提供影响预算的各种控制因素视图，帮助你规划用于 Purview 的预算


## <a name="factors-impacting-azure-pricing"></a>影响 Azure 定价的因素  
在规划 Purview 预算和成本管理时，需考虑直接和间接成本。

### <a name="direct-costs"></a>直接成本

直接成本会通过以下三个维度影响 Azure Purview 定价：
- **弹性数据映射**
- **自动化扫描与分类**
- **高级资源集**

#### <a name="elastic-data-map"></a>弹性数据映射

- 数据映射是 Purview 体系结构的基础，因此需要与任何给定点数据资产中的资产信息保持同步

- 根据容量单位 (CU) 对数据映射实施收费。 如果目录存储的元数据存储多达 2GB，且每秒最多可提供 25 个数据映射操作，则会在一个 CU 上预配数据映射

- 最初预配帐户时，系统始终会在一个 CU 上预配数据映射

- 但是，数据映射会自动在此弹性时段的下限和上限之间缩放，以满足数据映射中发生的更改，这些更改涉及两个关键因素：操作吞吐量和元数据存储

##### <a name="operation-throughput"></a>操作吞吐量

- 基于对数据映射执行创建、读取、更新、删除操作的事件驱动因素
- 部分数据映射操作的示例如下所示：- 在数据映射中创建资产；- 将关系添加到资产，例如所有者、专员、父级、世袭；- 编辑资产以添加业务元数据，例如说明、术语表术语；- 将结果返回到搜索结果页面的关键字搜索；- 使用 API 导入或导出信息
- 如果在数据映射上执行多个查询，则 I/O 操作的数量也会增加，进而增加数据映射
- 并发用户的数量也是治理数据映射容量单位的因素
- 其他需考虑的因素包括搜索查询的类型、API 交互、工作流、审批等
- 数据突发级别
    - 如果需要增加更多每秒操作的吞吐量，数据映射即可在弹性时段中自动缩放，以满足已更改的负载
    - 此级别构成了需要估计和计划的突发特征
    - 突发特征由突发级别和存在突发的突发持续时间组成
        - 突发级别是在稳定状态下预期弹性一致的乘法索引
        - 突发持续时间是由于元数据增长或数据映射上的操作数量增加而预期发生此类突发（弹性）的月份所占的比例


##### <a name="metadata-storage"></a>元数据存储

- 如果数据资产中的资产数量减少，然后在后续的增量扫描中遭删除，则存储组件会自动减少，数据映射亦会因此减少


#### <a name="automated-scanning--classification"></a>自动化扫描与分类

- 完全扫描会处理选定的数据源范围内的所有资产，而增量扫描则会检测并处理自上次成功扫描后创建、修改或删除的资产 

- 所有扫描（完全或增量扫描）都将选取已更新、修改或删除的资产

- 当属于不同部门的多个人员或组为同一数据源设置扫描，导致因重复扫描而支付额外费用时，考虑和避免出现这种情况便十分重要

- 建议安排“频繁的增量扫描”，使初始完全扫描与数据资产中的更改保持一致。 此操作将确保数据映射始终保持最新状态，且增量扫描会耗费比完全扫描更少的虚拟核心时间

- 数据源的“查看详细信息”链接可让用户运行完全扫描。 但是，建议考虑在完全扫描后执行增量扫描，以便获取优化的扫描结果（更改扫描规则集（例如分类或文件类型）除外）

- 建议在父集合中注册数据源，并通过不同的访问控制在子集合上确定范围扫描，以确保没有执行重复的扫描

- 建议使用集合授权的方式限制某些用户，使其无法再通过[精细的访问控制](./catalog-permissions.md)和数据源管理员角色注册用于扫描的数据源。 此操作将确保只允许注册有效数据源，并对扫描虚拟核心时间进行控制，从而降低扫描成本

- 请考虑正在扫描的数据源类型和资产数量对扫描持续时间的影响

- 建议创建自定义扫描规则集，使其只包含数据区域中可用文件类型的子集，以及与业务要求相关的分类，以确保可以充分使用扫描仪

- 为数据源创建新扫描时，建议在实际运行扫描之前遵循建议的准备顺序。 其中包括收集适用于业务特定分类和文件类型（适用于存储帐户）的要求，以实现定义相应的扫描规则集，从而避免多次扫描并控制因缺少要求而进行多次扫描所致的不必要成本

- 建议将扫描计划数量与自承载集成运行时 (SHIR) 虚拟机 (VM) 数量保持一致，以避免与虚拟机关联时产生额外成本


#### <a name="advanced-resource-sets"></a>高级资源集

- Azure Purview 使用资源集解决将大量数据资产映射到单个逻辑资源的难题，具体方法为提供扫描数据湖中的所有文件并查找模式（GUID、本地化模式等）的能力，以将这些文件作为数据映射中的单个资产进行分组

- “高级资源集”是一项可选功能，可让客户计算丰富的资源集信息（如总大小、分区计数等），并通过模式规则启用自定义资源集分组。 如果未启用高级资源集功能，则数据目录仍将包含资源集资产，但不含聚合属性。 在这种情况下，不会对客户的“资源集”计量收费。

- 建议先使用基本资源集功能，然后再开启 Purview 中的“高级资源集”，验证是否已满足要求

- 出现以下情况时，请考虑打开高级资源集：
    - 数据湖架构不断变化，并且正在寻找除基本资源集功能之外的其他值，以使 Purview 能够以服务的方式计算参数（例如 #partitions、数据资产的大小等）
    - 需要自定义资源集资产分组的方法 

- 请务必注意，对高级资源集的计费基于脱机层用于聚合资源集信息的计算，且取决于目录中的资源集的大小/数量


### <a name="indirect-costs"></a>间接成本   

影响 Azure Purview 定价的间接成本如下所示：

- [托管的资源](https://azure.microsoft.com/pricing/details/azure-purview/)
    - 预配 Purview 帐户后，会在订阅中创建存储帐户和事件中心队列，以便符合安全扫描的需要，此操作可能会单独收费


- [Azure 专用终结点](./catalog-private-link.md)
    - Azure 专用终结点可用于 Purview 帐户，在此帐户中，虚拟网络 (VNet) 上的用户需要通过专用链接才能安全访问目录
    - 设置专用终结点的先决条件可能会产生额外成本

- [自承载集成运行时相关成本](./manage-integration-runtimes.md) 
    - 自承载集成运行时因需要基础结构而会产生额外成本
    - 必须在部署 Azure Purview 引入专用终结点的同一虚拟网络内部署和注册自承载集成运行时 (SHIR)
    - [扫描的其他内存要求](./register-scan-sapecc-source.md#creating-and-running-a-scan)
        - 某些数据源（例如 SAP）需要 SHIR 计算机上的额外内存来进行扫描


- [虚拟机大小调整](../virtual-machines/sizes.md)
    - 规划虚拟机大小，以便在虚拟机之间分发扫描工作负载，以优化运行扫描时所使用的 V 核心

- [Microsoft 365 许可证](./create-sensitivity-label.md) 
    - Microsoft 信息保护 (MIP) 敏感度标签可自动应用于 Azure Purview 中的 Azure 资产。
    - MIP 敏感度标签在 Microsoft 365 安全与合规中心中创建和管理。
    - 要创建可在 Azure Purview 中使用的敏感度标签，必须拥有具有自动应用敏感度标签功能的有效 Microsoft 365 许可证。 有关许可证的完整列表，请参阅 Azure Purview 中的敏感度标签常见问题解答。 

- [Azure 警报](../azure-monitor/alerts/alerts-overview.md)
    - Azure 警报可使用 Azure Monitor 中的监视数据通知客户基础结构或应用程序发现的问题
    - [此处](https://azure.microsoft.com/pricing/details/monitor/)提供 Azure 警报的其他定价

- [成本管理预算和警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md)
    - 在 Azure 中，根据何时使用 Azure 资源，借助自动生成的成本警报监视 Azure 使用和支出情况
    - Azure 允许创建和管理 Azure 预算， 详情请参阅[教程](../cost-management-billing/costs/tutorial-acm-create-budgets.md)

- 多云流出量费用
    - 请考虑与扫描运行原生服务（S3 和 RDS 源除外）的多云（例如 AWS、Google）数据源关联的流出量费用（作为多云订阅的一部分添加的最低费用）


## <a name="next-steps"></a>后续步骤
- [Azure Purview 定价页面](https://azure.microsoft.com/pricing/details/azure-purview/)
