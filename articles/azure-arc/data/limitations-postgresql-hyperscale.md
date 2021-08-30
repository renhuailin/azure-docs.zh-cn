---
title: 已启用 Azure Arc 的超大规模 PostgreSQL 的限制
description: 已启用 Azure Arc 的超大规模 PostgreSQL 的限制
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 4fdcf88200b3c2a6b5371633c10d9246cfbf4c31
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730237"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL 的限制

本文介绍已启用 Azure Arc 的超大规模 PostgreSQL 的限制。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>备份和还原

- 不支持同一服务器组的时间点还原（例如还原到特定日期和时间）。 执行时间点还原时，必须在还原之前部署的另一个服务器组上进行还原。 还原到新的服务器组后，可以删除原始的服务器组。
- PostgreSQL 版本 12 支持将备份的完整内容还原到同一服务器组（而不是还原到特定的时间点）。 PostgreSQL 版本 11 由于 PostgreSQL 引擎存在时间线限制，不支持此方法。 若要还原版本 11 的 PostgreSQL 服务器组备份的完整内容，必须将其还原到另一个服务器组。


## <a name="databases"></a>数据库

不支持在一个服务器组中承载多个数据库。


## <a name="security"></a>安全

不支持管理用户和角色。 目前，请继续使用 postgres 标准用户。

## <a name="roles-and-responsibilities"></a>角色和职责

Microsoft 及其客户在 Azure PaaS 服务（平台即服务）和 Azure 混合服务（例如已启用 Azure Arc 的超大规模 PostgreSQL）之间的角色和职责是不同的。 

### <a name="frequently-asked-questions"></a>常见问题

下表汇总了有关支持角色和职责的常见问题的解答。

| 问题                      | Azure 平台即服务 (PaaS) | Azure Arc 混合服务 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 谁提供基础结构？  | Microsoft                          | 客户                  |
| 谁提供该软件？*       | Microsoft                          | Microsoft                 |
| 谁执行操作？ | Microsoft                          | 客户                  |
| Microsoft 是否提供 SLA？      | 是                                | 否                        |
| 谁负责 SLA？ | Microsoft                          | 客户                  |

\* Azure 服务

为什么 Microsoft 没有对 Azure Arc 混合服务提供 SLA？ 因为 Microsoft 没有基础结构，也不负责运营它。 它是由客户运营的。

## <a name="next-steps"></a>后续步骤

- 请试用。在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始。 

- “自行创建。” 请按以下步骤在自己的 Kubernetes 群集上创建： 
   1. [安装客户端工具](install-client-tools.md)
   2. [创建 Azure Arc 数据控制器](create-data-controller.md)
   3. [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [阅读了解有关已启用 Azure Arc 的数据服务的详细信息](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [了解 Azure Arc](https://aka.ms/azurearc)
