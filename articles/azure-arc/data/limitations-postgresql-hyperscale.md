---
title: 启用 Azure Arc 的限制 PostgreSQL 超大规模
description: 启用 Azure Arc 的限制 PostgreSQL 超大规模
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 01/21/2021
ms.topic: how-to
ms.openlocfilehash: ecf3c1f0d553c6521178fa5e1f67fc1cf95de73e
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987043"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>启用 Azure Arc 的限制 PostgreSQL 超大规模

本文介绍启用了 Azure Arc PostgreSQL 超大规模的限制。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>备份和还原

- 不支持还原到同一服务器组的特定日期和时间)  (时间点还原。 执行时间点还原时，必须还原到还原之前已部署的其他服务器组。 还原到新的服务器组后，您可以删除该服务器的源组。
- PostgreSQL 版本12支持还原备份 (的整个内容，而不是还原到特定的时间点) 到同一服务器组。 由于使用时间线的 PostgreSQL 引擎的限制，PostgreSQL 版本11不支持此方法。 若要为版本11的 PostgreSQL 服务器组还原整个备份内容，必须将其还原到另一个服务器组。


## <a name="databases"></a>数据库

不支持在一个服务器组中承载多个数据库。


## <a name="security"></a>安全性

不支持管理用户和角色。 现在，请继续使用 postgres 标准用户。

## <a name="roles-and-responsibilities"></a>角色和职责

Microsoft 及其客户之间的角色和职责在 Azure PaaS 服务 (平台即服务) 和 Azure 混合 (，例如启用了 Azure Arc 的 PostgreSQL 超大规模) 。 

### <a name="frequently-asked-questions"></a>常见问题解答

下表总结了有关支持角色和责任的常见问题的解答。

| 问题                      |  (PaaS) 的 Azure 平台即服务 | Azure Arc 混合服务 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 谁提供基础结构？  | Microsoft                          | 客户                  |
| 谁提供该软件？ *       | Microsoft                          | Microsoft                 |
| 谁执行操作？ | Microsoft                          | 客户                  |
| Microsoft 是否提供 Sla？      | 是                                | 否                        |
| 谁负责 Sla？ | Microsoft                          | 客户                  |

\* Azure 服务

__为什么 Microsoft 不提供 Azure Arc 混合服务的 Sla？__ 由于 Microsoft 不拥有基础结构，因此不会运行它。 客户进行操作。

## <a name="next-steps"></a>后续步骤

- **试试看。** 在 Azure Kubernetes Service 上快速开始使用 [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) (AKS) 、AWS 弹性 Kubernetes SERVICE (EKS) 、Google Cloud Kubernetes ENGINE (GKE) 或 Azure VM。 

- **创建自己的。** 按照以下步骤在自己的 Kubernetes 群集上创建： 
   1. [安装客户端工具](install-client-tools.md)
   2. [创建 Azure Arc 数据控制器](create-data-controller.md)
   3. [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [阅读有关启用了 Azure Arc 的数据服务的详细信息](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [了解 Azure Arc](https://aka.ms/azurearc)
