---
title: 什么是 Azure 数据库迁移服务？
description: Azure 数据库迁移服务概述，该服务提供从多个数据库源到 Azure 数据平台的无缝迁移。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 09/01/2021
ms.openlocfilehash: 68d462a93d891c25602bb305417ddeb64f5f02a1
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123317266"
---
# <a name="what-is-azure-database-migration-service"></a>什么是 Azure 数据库迁移服务？

Azure 数据库迁移服务是一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并且最大限度地减少停机时间（联机迁移）。

[!INCLUDE [database-migration-services-sql-mi-sql-vm](../../includes/database-migration-services-sql-mi-sql-vm.md)]

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>使用熟悉的工具将数据库迁移到 Azure

Azure 数据库迁移服务集成了一些现有工具和服务的功能。 它为客户提供高度可用的综合解决方案。 该服务使用[数据迁移助手](/sql/dma/dma-overview)生成评估报告，这些报告提供建议以指导你在执行迁移之前完成所需的更改。 你可以自己选择执行所需的修正。 当你准备好开始该迁移过程时，Azure 数据库迁移服务会执行所有必需步骤。 该过程利用了 Microsoft 确定的最佳做法，因此你便可以在启动迁移项目后高枕无忧。 

> [!NOTE]
> 使用 Azure 数据库迁移服务执行联机迁移需要基于“高级”定价层创建实例。

## <a name="regional-availability"></a>区域可用性

有关 Azure 数据库迁移服务区域可用性的最新信息，请参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)。

## <a name="pricing"></a>定价

有关 Azure 数据库迁移服务定价的最新信息，请参阅 [Azure 数据库迁移服务定价](https://azure.microsoft.com/pricing/details/database-migration/)。

## <a name="next-steps"></a>后续步骤

* [Azure 数据库迁移服务支持的迁移方案的状态](resource-scenario-status.md)。
* [使用 Azure 门户创建 Azure 数据库迁移服务实例](quickstart-create-data-migration-service-portal.md)。
* [将 SQL Server 迁移到 Azure SQL 数据库](tutorial-sql-server-to-azure-sql.md)。
* [使用 Azure 数据库迁移服务的先决条件概述](pre-reqs.md)。
* [有关使用 Azure 数据库迁移服务的常见问题解答](faq.yml)。
* [可用于数据迁移方案的服务和工具](dms-tools-matrix.md)。