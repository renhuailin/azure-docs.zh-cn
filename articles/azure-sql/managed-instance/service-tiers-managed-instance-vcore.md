---
title: vCore 购买模型
description: 使用 vCore 购买模型，可以单独为 Azure SQL 托管实例缩放计算和存储资源、匹配本地性能和优化价格
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.openlocfilehash: 16782538918c0477b969f95b7730b38221f97f82
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414755"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL 托管实例 - vCore 服务层级中的计算硬件
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文介绍了 [Azure SQL 托管实例](sql-managed-instance-paas-overview.md)的 vCore 购买模型。 若要详细了解如何在 vCore 和 DTU 购买模型之间进行选择，请参阅[在 vCore 和 DTU 购买模型之间进行选择](../database/purchasing-models.md)。

Azure SQL 托管实例使用的虚拟核心 (vCore) 购买模型具有以下特征：

- 控制硬件代系，以便更好地符合工作负荷的计算和内存要求。
- [Azure 混合权益 (AHB)](../azure-hybrid-benefit.md) 和[预留实例 (RI)](../database/reserved-capacity-overview.md) 的定价折扣。
- 为驱动计算的硬件细节提供更高的透明度，这有助于规划从本地部署进行的迁移。
- [预留实例定价](../database/reserved-capacity-overview.md)只适用于 vCore 购买模型。 

## <a name="service-tiers"></a><a id="compute-tiers"></a>服务层

vCore 购买模型中的服务层级选项包括“常规用途”和“业务关键”。 服务层级通常定义存储体系结构、空间和 I/O 限制，以及与可用性和灾难恢复相关的业务连续性选项。

|**用例**|**常规用途**|**业务关键**|
|---|---|---|
|最适用于|大多数业务工作负荷。 提供预算导向的、均衡且可缩放的计算和存储选项。 |它使用多个独立副本为商业应用程序提供最高级别的故障恢复能力，并提供最高的 I/O 性能。|
|存储|使用远程存储。 32 GB - 8 TB |使用本地 SSD 存储。 32 GB - 4 TB |
|IOPS 和吞吐量（近似值）|请参阅 [Azure SQL 托管实例资源限制概述](../managed-instance/resource-limits.md#service-tier-characteristics)。|请参阅 [Azure SQL 托管实例资源限制概述](../managed-instance/resource-limits.md#service-tier-characteristics)。|
|可用性|1 个副本，无读取缩放副本|3 个副本，1 个[读取缩放副本](../database/read-scale-out.md)，<br/>区域冗余高可用性 (HA)|
|备份|[读取访问异地冗余存储 (RA-GRS)](../../storage/common/geo-redundant-design.md)，1-35 天（默认为 7 天）|[RA-GRS](../../storage/common/geo-redundant-design.md)，1-35 天（默认为 7 天）|
|内存中|不支持|支持|
||||

### <a name="choosing-a-service-tier"></a>选择服务层级

有关为特定工作负荷选择服务层级的信息，请参阅以下文章：

- [何时选择“常规用途”服务层级](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何时选择“业务关键”服务层级](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>计算

SQL 托管实例计算提供特定数量的计算资源，这些资源是独立于工作负荷活动持续预配的，针对预配的计算资源量按固定的小时价格计费。

## <a name="hardware-generations"></a>硬件代系

vCore 模型中的硬件代系选项包括第 5 代硬件系列。 硬件代系通常定义计算和内存限制，以及影响工作负荷性能的其他特征。

### <a name="compute-and-memory-specifications"></a>计算和内存规格

|硬件代次  |计算  |内存  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3 (Haswell) 2.4-GHz 处理器<br>- 最多预配 24 个 vCore（1 个 vCore = 1 个物理核心）  |- 每个 vCore 7 GB<br>- 最多预配 168 GB|
|Gen5     |- Intel&reg; E5-2673 v4 (Broadwell) 2.3-GH、Intel&reg; SP-8160 (Skylake)\* 和 Intel&reg; 8272CL (Cascade Lake) 2.5 GHz\* 处理器<br>- 最多预配 80 个 vCore（1 个 vCore = 1 个超线程）|每个 vCore 5.1 GB<br>- 最多预配 408 GB|

\* 在 [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 动态管理视图中，使用 Intel&reg; SP-8160 (Skylake) 处理器的实例的硬件代系会显示为 Gen6，而使用 Intel&reg; 8272CL (Cascade Lake) 的实例的硬件代系会显示为 Gen7。 不管什么处理器类型（Broadwell、Skylake 或 Cascade Lake），所有 Gen5 实例的资源限制都相同。

### <a name="selecting-a-hardware-generation"></a>选择硬件代系

在 Azure 门户中，可以在创建时选择硬件代系，也可以更改现有 SQL 托管实例的硬件代系。

**创建 SQL 托管实例时选择硬件代系**

有关详细信息，请参阅[创建 SQL 托管实例](../managed-instance/instance-create-quickstart.md)。

在“基本信息”选项卡上，选择“计算 + 存储”部分中的“配置数据库”链接，然后选择所需的硬件代系：  

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="配置 SQL 托管实例"  loc-scope="azure-portal":::
  
**更改现有 SQL 托管实例的硬件代系**

#### <a name="the-azure-portal"></a>[Azure 门户](#tab/azure-portal)

在“SQL 托管实例”页面上，选择“设置”部分下的“定价层”链接

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="更改 SQL 托管实例硬件"  loc-scope="azure-portal":::

在“定价层”页上，可以按前面步骤中所述更改硬件代系。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell 脚本：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

有关更多详细信息，请查看 [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) 命令。

#### <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

有关更多详细信息，请查看 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) 命令。

---

### <a name="hardware-availability"></a>硬件可用性

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> 第 4 代/第 5 代

“第 4 代”硬件[正在逐步被淘汰](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，不能再用于新部署。 所有新实例都必须部署在第 5 代硬件上。

“第 5 代”在世界范围内所有公共区域中都可用。

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅[使用 Azure 门户创建 SQL 托管实例](instance-create-quickstart.md)
- 有关定价详细信息，请参阅： 
    - [Azure SQL 托管实例单实例定价页](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL 托管实例池定价页](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- 若要详细了解“常规用途”和“业务关键”服务层级中提供的特定计算大小和存储大小，请参阅 [Azure SQL 托管实例基于 vCore 的资源限制](resource-limits.md)。
