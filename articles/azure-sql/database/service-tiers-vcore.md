---
title: vCore 购买模型
titleSuffix: Azure SQL Database & SQL Managed Instance
description: 借助 vCore 购买模型，可单独缩放计算和存储资源、匹配本地性能，并优化 Azure SQL 数据库和 Azure SQL 托管实例的价格。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma
ms.date: 05/18/2021
ROBOTS: NOINDEX
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6c16e508aa2002aff07df5fc30e0af4c74bdd025
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413692"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore 模型概述 - Azure SQL 数据库和 Azure SQL 托管实例 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL 数据库和 Azure SQL 托管实例使用的虚拟核心 (vCore) 购买模型具有下面几个优势：

- 控制硬件代系，以便更好地符合工作负荷的计算和内存要求。
- [Azure 混合权益 (AHB)](../azure-hybrid-benefit.md) 和[预留实例 (RI)](reserved-capacity-overview.md) 的定价折扣。
- 为驱动计算的硬件细节提供更高的透明度，这有助于规划从本地部署进行的迁移。
- 对于 Azure SQL 数据库，vCore 购买模型提供的计算、内存、I/O 和存储限制比 DTU 模型高。

有关在 vCore 和 DTU 购买模型之间进行选择的详细信息，请参阅[在 vCore 和 DTU 购买模型之间选择](purchasing-models.md)。

## <a name="service-tiers"></a>服务层

以下文章提供了有关每种产品中 vCore 购买模型的具体信息。

- 有关 vCore 模型的 Azure SQL 数据库服务层级的信息，请参阅 [vCore 模型概述 - Azure SQL 数据库](service-tiers-sql-database-vcore.md)。
- 有关 vCore 模型的 Azure SQL 托管实例服务层级的信息，请参阅 [vCore 模型概述 - Azure SQL 托管实例](../managed-instance/service-tiers-managed-instance-vcore.md)。

## <a name="next-steps"></a>后续步骤

如要入门，请参阅： 
- [使用 Azure 门户创建 SQL 数据库](single-database-create-quickstart.md)
- [使用 Azure 门户创建 SQL 托管实例](../managed-instance/instance-create-quickstart.md)

- 有关定价详细信息，请参阅 
    - [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)
    - [Azure SQL 托管实例单实例定价页](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Azure SQL 托管实例池定价页](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
    
若要详细了解“常规用途”和“业务关键”服务层级中提供的特定计算大小和存储大小，请参阅：

- [Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)。
- [共用 Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-elastic-pools.md)。
- [Azure SQL 托管实例基于 vCore 的资源限制](../managed-instance/resource-limits.md)。
