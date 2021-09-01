---
title: 什么是已启用 Azure Arc 的超大规模 PostgreSQL？
description: 什么是已启用 Azure Arc 的超大规模 PostgreSQL？
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d43753084f79184b338b16223ed45cb00a2dfb33
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732451"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>什么是已启用 Azure Arc 的超大规模 PostgreSQL？

已启用 Azure Arc 的超大规模 PostgreSQL 是已启用 Azure Arc 的数据服务中提供的一项数据库服务。 借助 Azure Arc，可以使用 Kubernetes 和你选择的基础结构在本地、边缘和公有云环境中运行 Azure 数据服务。 已启用 Azure Arc 的数据服务的价值主张围绕以下内容阐述：
- 始终最新
- 弹性缩放
- 自助式预配
- 统一管理
- 断开连接的方案支持

有关详细信息，请访问：
- [什么是已启用 Azure Arc 的数据服务](overview.md)
- [连接模式和要求](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

若要详细了解这些功能，还可以观看此“公开的数据”剧集。
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]

## <a name="compare-solutions"></a>比较解决方案

本部分介绍了已启用 Azure Arc 的超大规模 PostgreSQL 与 Azure Database for PostgreSQL 超大规模 (Citus) 有哪些不同之处？

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Azure Database for PostgreSQL 超大规模 (Citus)

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure SQL Database for PostgreSQL 超大规模 (Citus)":::

这是 Azure (PaaS) 中以数据库即服务形式提供的 Postgres 数据库引擎的超大规模外形规格。 它由实现超大规模体验的 Citus 扩展提供支持。 在此外形规格中，服务在 Microsoft 数据中心运行，由 Microsoft 操作。

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="已启用 Azure Arc 的超大规模 PostgreSQL":::

这是已启用 Azure Arc 的数据服务提供的 Postgres 数据库引擎的超大规模外形规格。 它也由实现超大规模体验的 Citus 扩展提供支持。 在此外形规格中，我们的客户提供了托管和运行系统的基础结构。

## <a name="next-steps"></a>后续步骤
- 请试用。在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始。 

- “自行创建。” 请按以下步骤在自己的 Kubernetes 群集上创建： 
   1. [安装客户端工具](install-client-tools.md)
   2. [创建 Azure Arc 数据控制器](create-data-controller.md)
   3. [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [阅读了解有关已启用 Azure Arc 的数据服务的详细信息](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [了解 Azure Arc](https://aka.ms/azurearc)
