---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：代表性用例
description: 以下用例基于将 MySQL 工作负载迁移到 Azure Database for MySQL 的企业的真实客户场景。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 508e27006c96003bd4c2825c9987761f83358f0d
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085067"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-representative-use-case"></a>将本地 MySQL 迁移到 Azure Database for MySQL：代表性用例

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[介绍](01-mysql-migration-guide-intro.md)
## <a name="overview"></a>概述

以下用例基于将 MySQL 工作负载迁移到 Azure Database for MySQL 的企业的真实客户场景。

World-Wide Importers (WWI) 公司是一家总部位于加利福尼亚州旧金山的新奇商品制造商和批发商。 该公司于 2002 年开始运营，并开发了一种有效的企业对企业 (B2B) 模式，将其生产的产品直接销售给美国各地的零售客户。 其客户包括专卖店、超市、电脑商店、旅游景点商店和一些个人买家。 这种 B2B 模式简化了产品的分销系统，使他们能够降低成本并为其制造的产品提供更具竞争力的价格。 该公司还通过代理网络向其他批发商销售产品，这些代理代表 WWI 推销他们的产品。

在进入新领域之前，WWI 希望确保其 IT 基础结构能够应对预期的增长。 WWI 目前在其公司总部本地托管其所有 IT 基础结构，并相信将这些资源转移到云中可以促进未来的增长。 因此，他们让 CIO 负责监督客户门户和相关数据工作负载向云的迁移。

WWI 希望继续利用云中提供的许多高级功能，并且希望将其数据库和相关工作负载迁移到 Azure。 他们希望在不对其应用程序或数据库进行任何更改的情况下快速完成此操作。 最初，他们计划将基于 Java 的客户门户 Web 应用程序以及相关的 MySQL 数据库和工作负载迁移到云中。

### <a name="migration-goals"></a>迁移目标

将数据库和相关 SQL 工作负载迁移到云的主要目标包括：

  - 改善静态数据和传输中的数据的整体安全状况。

  - 增强高可用性和灾难恢复 (HA/DR) 功能。

  - 使组织能够使用时间点还原等云原生功能和技术。

  - 利用 Azure Database for MySQL 的管理和性能优化功能。

  - 创建一个可扩展的平台，他们可以使用该平台将业务扩展到更多地理区域。

  - 更好地遵循存储个人信息的各种法律要求。

WWI 使用[云采用框架 (CAF)](/azure/cloud-adoption-framework/) 来指导他们的团队遵循云迁移的最佳做法指南。 然后，使用 CAF 作为更高级别的迁移指南，WWI 将其迁移分为三个主要阶段。 最后，他们定义了需要在每个阶段处理的活动，以确保直接云迁移取得成功。

这些阶段包括：

| 阶段 | 名称 | 活动 |
|-------|------|------------|
| 1 | 迁移前 | 评估、计划、迁移方法评估、应用程序影响、Test Plans、性能基线 |
| 2 | 迁移     | 执行迁移、执行 Test Plans                                                                          |
| 3 | 迁移后| 业务连续性、灾难恢复、管理、安全性、性能优化、平台现代化 |

WWI 有多个 MySQL 实例运行，版本从 5.5 到 5.7 不等。 他们希望尽快将他们的实例转换为最新版本，但希望确保他们的应用程序在转换为新版本后仍能正常工作。 他们可以接受迁移到云中的相同版本并在之后升级，但如果可以一次性完成两项任务，他们更愿意采用这种方式。

他们还希望确保在出现故障时，数据工作负载在多个地理区域是安全且可用的，并查看可用的配置选项。

WWI 希望从一个简单的应用程序开始进行第一次迁移，然后在后续阶段迁移更关键的业务应用程序。 这将为团队提供准备和规划那些未来迁移所需的知识和经验。  

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [评估](./03-assessment.md)
