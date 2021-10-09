---
title: 已启用 Azure Arc 的 SQL 托管实例的限制
description: 已启用 Azure Arc 的 SQL 托管实例的限制
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 09/07/2021
ms.topic: conceptual
ms.openlocfilehash: 8709504d93f22ee5f2704b7b1a8d71a5e66df796
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837708"
---
# <a name="limitations-of-azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例的限制

本文介绍已启用 Azure Arc 的 SQL 托管实例的限制。 

当前，业务关键服务层级为预览状态版。 常规用途服务层级已正式发布。

## <a name="backup-and-restore"></a>备份和还原

### <a name="automated-backups"></a>自动备份 

-  不会备份具有简单恢复模式的用户数据库。
-  不会备份系统数据库 `model` 以防干扰数据库的创建/删除。 执行管理操作时，数据库被锁定。

### <a name="point-in-time-restore-pitr"></a>时间点还原 (PITR)

-  不支持从一个已启用 Azure Arc 的 SQL 托管实例还原到另一个启用 Azure Arc 的 SQL 托管实例。  只能将数据库还原到创建备份时所在的同一个启用 Arc 的 SQL 托管实例。
-  为了执行时间点还原，目前不支持重命名数据库。
-  当前不支持还原已启用 TDE 的数据库。
-  当前无法还原已删除的数据库。

## <a name="other-limitations"></a>其他限制 

-  当前不支持事务复制。
-  当前会阻止日志传送。
-  仅支持 SQL Server 身份验证。

## <a name="roles-and-responsibilities"></a>角色和职责

Microsoft 及其客户在 Azure PaaS 服务（平台即服务）和 Azure 混合服务（例如已启用 Azure Arc 的 SQL 托管实例）之间的角色和职责是不同的。 

### <a name="frequently-asked-questions"></a>常见问题

下表汇总了有关支持角色和职责的常见问题的解答。

| 问题                          | Azure 平台即服务 (PaaS) | Azure Arc 混合服务 |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| 谁提供基础结构？  | Microsoft                          | 客户                  |
| 谁提供该软件？*       | Microsoft                          | Microsoft                 |
| 谁执行操作？          | Microsoft                          | 客户                  |
| Microsoft 是否提供 SLA？      | 是                                | 否                        |
| 谁负责 SLA？          | Microsoft                          | 客户                  |

\* Azure 服务

为什么 Microsoft 没有对 Azure Arc 混合服务提供 SLA？ 因为 Microsoft 没有基础结构，也不负责运营它。 它是由客户运营的。

## <a name="next-steps"></a>后续步骤

- 请试用。在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始。 

- “自行创建。” 请按以下步骤在自己的 Kubernetes 群集上创建： 
   1. [安装客户端工具](install-client-tools.md)
   2. [创建 Azure Arc 数据控制器](create-data-controller.md)
   3. [创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md) 

- **Learn**
   - [阅读了解有关已启用 Azure Arc 的数据服务的详细信息](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [了解 Azure Arc](https://aka.ms/azurearc)
