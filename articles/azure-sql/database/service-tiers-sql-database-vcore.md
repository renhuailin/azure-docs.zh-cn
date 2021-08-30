---
title: vCore 购买模型
description: 借助 vCore 购买模型，可单独缩放计算和存储资源、匹配本地性能，并优化 Azure SQL 数据库的价格
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 07/14/2021
ms.custom: references_regions
ms.openlocfilehash: 3e80c1153737514575017685310b6e5306a47167
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730815"
---
# <a name="vcore-purchase-model-overview---azure-sql-database"></a>vCore 购买模型概述 - Azure SQL 数据库 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍了适用于 [Azure SQL 数据库](sql-database-paas-overview.md)的 vCore 购买模型。 有关在 vCore 和 DTU 购买模型之间进行选择的详细信息，请参阅[在 vCore 和 DTU 购买模型之间选择](purchasing-models.md)。

Azure SQL 数据库使用的虚拟核心 (vCore) 购买模型相比于 DTU 购买模型而言具备一些优势：

- 更高的计算、内存、I/O 和存储限制。
- 控制硬件代系，以便更好地符合工作负荷的计算和内存要求。
- [Azure 混合权益 (AHB)](../azure-hybrid-benefit.md) 的定价折扣。
- 为驱动计算的硬件细节提供更高的透明度，这有助于规划从本地部署进行的迁移。
- [预留实例定价](reserved-capacity-overview.md)只适用于 vCore 购买模型。 

## <a name="service-tiers"></a>服务层

vCore 购买模型中的服务层级选项包括“常规用途”、“业务关键”和“超大规模”。 服务层级通常定义存储体系结构、空间和 I/O 限制，以及与可用性和灾难恢复相关的业务连续性选项。

|**用例**|**常规用途**|**业务关键**|**超大规模**|
|---|---|---|---|
|最适用于|大多数业务工作负荷。 提供预算导向的、均衡且可缩放的计算和存储选项。 |它使用多个独立副本为商业应用程序提供最高级别的故障恢复能力，为每个数据库副本提供最高的 I/O 性能。|具有很高的可缩放存储和读取缩放要求的大多数业务工作负荷。  允许配置多个独立的数据库副本，提供更高的故障恢复能力。 |
|存储|使用远程存储。<br/>**SQL 数据库预配计算**：<br/>5 GB – 4 TB<br/>**无服务器计算**<br/>5 GB - 3 TB|使用本地 SSD 存储。<br/>**SQL 数据库预配计算**：<br/>5 GB – 4 TB|可以根据需要灵活地自动扩展存储。 最多支持 100 TB 存储空间。 使用本地 SSD 存储作为本地缓冲池缓存和本地数据存储。 使用 Azure 远程存储作为最终的长期数据存储。 |
|IOPS 和吞吐量（近似值）|**SQL 数据库**：请查看 [单一数据库](resource-limits-vcore-single-databases.md)和 [弹性池](resource-limits-vcore-elastic-pools.md)的资源限制。|请查看[单一数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)的资源限制。|超大规模是具有多个级别缓存的多层体系结构。 有效的 IOPS 和吞吐量将取决于工作负载。|
|可用性|1 个副本，无读取缩放副本|3 个副本，1 个[读取缩放副本](read-scale-out.md)，<br/>区域冗余高可用性 (HA)|1 个读写副本加 0-4 个[读取缩放副本](read-scale-out.md)|
|备份|[读取访问异地冗余存储 (RA-GRS)](../../storage/common/geo-redundant-design.md)，1-35 天（默认为 7 天）|[RA-GRS](../..//storage/common/geo-redundant-design.md)，1-35 天（默认为 7 天）|Azure 远程存储中基于快照的备份。 还原使用这些快照进行快速恢复。 备份瞬间完成，不会影响计算 I/O 性能。 还原速度很快，不基于数据操作的大小（需要几分钟，而不是几小时或几天）。|
|内存中|不支持|支持|部分支持。 支持内存优化表类型、表变量和原生编译模块。|
|||


### <a name="choosing-a-service-tier"></a>选择服务层级

有关为特定工作负荷选择服务层级的信息，请参阅以下文章：

- [何时选择“常规用途”服务层级](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何时选择“业务关键”服务层级](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [何时选择“超大规模”服务层级](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>计算层级

vCore 模型中的计算层级选项包括预配计算层级和无服务器计算层级。


### <a name="provisioned-compute"></a>预配计算

预配计算层级提供特定数量的计算资源，这些资源是独立于工作负荷活动持续预配的，针对预配的计算资源量按固定的小时价格计费。


### <a name="serverless-compute"></a>无服务器计算

[无服务器计算层级](serverless-tier-overview.md)根据工作负荷活动自动缩放计算资源，针对使用的计算资源量按秒计费。



## <a name="hardware-generations"></a>硬件代系

vCore 模型中的硬件代系选项包括“第 4 代”和“第 5 代”、M 系列、Fsv2 系列和 DC 系列。 硬件代系通常定义计算和内存限制，以及影响工作负荷性能的其他特征。

### <a name="gen4gen5"></a>第 4 代/第 5 代

- 第 4 代/第 5 代硬件提供均衡的计算和内存资源，适合用于内存和 vCore 要求不太高，或者不需要较快的单一 vCore 的大部分数据库工作负荷，而 Fsv2 系列或 M 系列可以满足这些要求。

有关第 4 代/第 5 代的可用区域，请参阅[第 4 代/第 5 代可用性](#gen4gen5-1)。

### <a name="fsv2-series"></a>Fsv2 系列

- Fsv2 系列是计算优化的硬件选项，可为大多数对 CPU 要求很高的工作负载提供低 CPU 延迟和高时钟速度。
- 具体取决于工作负载，Fsv2 系列相较于第 5 代可以为每个 vCore 提供更高的 CPU 性能，并且其 72 个 vCore 相较于第 5 代中的 80 个 vCore 可以提供更高的 CPU 性能，且成本更低。 
- 相较于其他硬件，Fsv2 为每个 vCore 提供的内存和 tempdb 更少，因此对这些限制敏感的工作负载可能会考虑使用第 5 代或 M 系列。  

仅常规用途层支持 Fsv2 系列。 对于 Fsv2 系列可用的区域，请参阅 [Fsv2 系列的可用性](#fsv2-series-1)。

### <a name="m-series"></a>M 系列

- M 系列是内存优化的硬件选项，适用于所需内存和计算限制超过第 5 代所提供内存和计算限制的工作负载。
- M 系列为每个 vCore 提供 29 GB，最多支持 128 个 vCore。相较于第 5 代，内存限制增加了 8 倍，达到近 4 TB。

仅业务关键层支持 M 系列，且 M 系列不支持区域冗余。  对于 M 系列可用的区域，请参阅 [M 系列的可用性](#m-series-1)。

#### <a name="azure-offer-types-supported-by-m-series"></a>M 系列支持的 Azure 产品/服务类型

若要访问 M 系列，订阅必须是付费产品/服务类型，包括即用即付或企业协议 (EA)。  有关 M 系列支持的 Azure 产品/服务类型的完整列表，请参阅[当前产品/服务，无需支出限制](https://azure.microsoft.com/support/legal/offer-details)。

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC 系列

- DC 系列硬件使用具有软件防护扩展 (Intel SGX) 技术的 Intel 处理器。
- [具有安全 enclave 的 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) 需要 DC 系列，其他硬件配置不支持此系列。
- DC 系列专为处理敏感数据且需要机密查询处理功能的工作负载设计，这些功能由具有安全 enclave 的 Always Encrypted 提供。
- DC 系列硬件提供均衡的计算和内存资源。

仅预配计算支持 DC 系列（无服务器不支持），并且此系列不支持区域冗余。 对于 DC 系列可用的区域，请参阅 [DC 系列的可用性](#dc-series-1)。

#### <a name="azure-offer-types-supported-by-dc-series"></a>DC 系列支持的 Azure 产品/服务类型

若要访问 DC 系列，订阅必须是付费产品/服务类型，包括即用即付或企业协议 (EA)。  有关 DC 系列支持的 Azure 产品/服务类型的完整列表，请参阅[当前产品/服务，无需支出限制](https://azure.microsoft.com/support/legal/offer-details)。

### <a name="compute-and-memory-specifications"></a>计算和内存规格


|硬件代次  |计算  |内存  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3 (Haswell) 2.4-GHz 处理器<br>- 最多预配 24 个 vCore（1 个 vCore = 1 个物理核心）  |- 每个 vCore 7 GB<br>- 最多预配 168 GB|
|Gen5     |**预配计算**<br>- Intel&reg; E5-2673 v4 (Broadwell) 2.3-GHz、Intel&reg; SP-8160 (Skylake)\* 和 Intel&reg; 8272CL (Cascade Lake) 2.5 GHz\* 处理器<br>- 最多预配 80 个 vCore（1 个 vCore = 1 个超线程）<br><br>**无服务器计算**<br>- Intel&reg; E5-2673 v4 (Broadwell) 2.3-GHz 和 Intel&reg; SP-8160 (Skylake)* 处理器<br>- 自动扩展为 40 个 vCore（1 个 vCore = 1 个超线程）|**预配计算**<br>- 每个 vCore 5.1 GB<br>- 最多预配 408 GB<br><br>**无服务器计算**<br>- 自动扩展为每个vCore 24 GB<br>- 自动纵向扩展为最大 120 GB|
|Fsv2 系列     |- Intel&reg; 8168 (Skylake) 处理器<br>- 具有 3.4 GHz 的持续全核 Turbo 时钟速度和 3.7 GHz 的最大单核 Turbo 时钟速度。<br>- 最多预配 72 个 vCore（1 个 vCore = 1 个超线程）|- 每个 vCore 1.9 GB<br>- 最多预配 136 GB|
|M 系列     |- Intel&reg; E7-8890 v3 2.5 GHz 和 Intel&reg; 8280M 2.7 GHz (Cascade Lake) 处理器<br>- 最多预配 128 个 vCore（1 个 vCore = 1 个超线程）|- 每个 vCore 29 GB<br>- 最多预配 3.7 TB|
|DC 系列     | - Intel XEON E-2288G 处理器<br>- 具有 Intel 软件防护扩展 (Intel SGX)<br>- 最多预配 8 个 vCore（1 个 vCore = 1 个物理核心） | 每个 vCore 4.5 GB |

\* 在 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 动态管理视图中，使用 Intel&reg; SP-8160 (Skylake) 处理器的数据库的硬件代系会显示为 Gen6，而使用 Intel&reg; 8272CL (Cascade Lake) 的数据库的硬件代系会显示为 Gen7。 不管处理器类型如何（Broadwell、Skylake 或 Cascade Lake），所有 Gen5 数据库的资源限制都相同。

有关资源限制的详细信息，请参阅[单一数据库的资源限制 (vCore)](resource-limits-vcore-single-databases.md) 或[弹性池的资源限制 (vCore)](resource-limits-vcore-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>选择硬件代系

在 Azure 门户中，可在 SQL 数据库中创建数据库或池时为其选择硬件代系，或者更改现有 SQL 数据库或池的硬件代系。

**创建 SQL 数据库或池时选择硬件代系**

有关详细信息，请参阅[创建 SQL 数据库](single-database-create-quickstart.md)。

在“基本信息”选项卡上，选择“计算 + 存储”部分中的“配置数据库”链接，然后选择“更改配置”链接：   

:::image type="content" source="./media/service-tiers-vcore/configure-sql-database.png" alt-text="配置 SQL 数据库" loc-scope="azure-portal":::

选择所需的硬件代系：

:::image type="content" source="./media/service-tiers-vcore/select-hardware.png" alt-text="选择 SQL 数据库的硬件" loc-scope="azure-portal":::

**更改现有 SQL 数据库或池的硬件代系**

对于数据库，请在“概述”页上选择“定价层”链接：

:::image type="content" source="./media/service-tiers-vcore/change-hardware.png" alt-text="更改 SQL 数据库的硬件" loc-scope="azure-portal":::

对于池，请在“概述”页上选择“配置”。

遵循相应的步骤更改配置，然后根据前面的步骤所述选择硬件代系。

### <a name="hardware-availability"></a>硬件可用性

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> 第 4 代/第 5 代

“第 4 代”硬件[正在逐步被淘汰](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，不能再用于新部署。 所有新的数据库都必须部署在第 5 代硬件上。

“第 5 代”在世界范围内所有公共区域中都可用。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列在以下区域中可用：澳大利亚中部、澳大利亚中部 2、澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、东亚、美国东部、法国中部、印度中部、韩国中部、韩国南部、北欧、南非北部、东南亚、英国南部、英国西部、西欧、美国西部 2。

#### <a name="m-series"></a>M 系列

M 系列在以下区域可用：美国东部、北欧、西欧、美国西部 2。
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC 系列

DC 系列在以下区域中可用：加拿大中部、加拿大东部、美国东部、北欧、英国南部、西欧、美国西部。

如果在当前不受支持的区域需要 DC 系列，请[提交支持工单](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 在“基本信息”页上，提供以下信息：

1. 对于“问题类型”，选择“技术” 。
1. 对于“服务类型”，选择“SQL 数据库” 。
1. 对于“问题类型”，选择“安全性、隐私和合规性” 。
1. 对于“问题子类型”，选择“Always Encrypted” 。

:::image type="content" source="./media/service-tiers-vcore/request-dc-series.png" alt-text="请求新区域中的 DC 系列" loc-scope="azure-portal":::

## <a name="next-steps"></a>后续步骤

- 若要开始使用，请参阅[使用 Azure 门户创建 SQL 数据库](single-database-create-quickstart.md)
- 有关定价的详细信息，请参阅 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)
- 有关可用的特定计算和存储大小的详细信息，请参阅：
    - [Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)
    - [共用 Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-elastic-pools.md)
