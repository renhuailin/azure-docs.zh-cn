---
title: Azure CLI 示例 - Azure Database for MySQL 灵活服务器
description: 本文列出了可用于与 Azure Database for MySQL 灵活服务器交互的 Azure CLI 代码示例。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 7f68da581378d9050ad0026930196f09b744371f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604621"
---
# <a name="azure-cli-samples-for-azure-database-for-mysql---flexible-server-preview"></a>适用于 Azure Database for MySQL 灵活服务器的 Azure CLI 示例（预览版） 

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

下表包含一些链接，指向适用于 Azure Database for MySQL 灵活服务器的示例 Azure CLI 脚本。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。


| 示例链接 | 说明  |
|---|---|
|**创建并连接到服务器**||
| [创建服务器并启用公共访问连接](scripts/sample-cli-create-connect-public-access.md) | 创建 Azure Database for MySQL 灵活服务器，配置服务器级防火墙规则（公共访问连接方法）并连接到服务器。 |
| [创建服务器并启用专用访问连接（VNet 集成）](scripts/sample-cli-create-connect-private-access.md) | 在 VNet（专用访问连接方法）中创建 Azure Database for MySQL 灵活服务器，并通过 VNet 中的 VM 连接到服务器。 |
|**监视和缩放**||
| [监视指标并缩放服务器](scripts/sample-cli-monitor-and-scale.md) | 监视并纵向扩展或缩减单个 Azure Database for MySQL 灵活服务器，以允许更改性能需求。 |
|**备份和还原**||
| [还原服务器](scripts/sample-cli-restore-server.md) | 将单个 Azure Database for MySQL 灵活服务器还原到以前的时间点。 |
|**高可用性**||
| [配置区域冗余高可用性](scripts/sample-cli-zone-redundant-ha.md) | 在创建 Azure Database for MySQL 灵活服务器时启用区域冗余高可用性。|
| [配置相同区域高可用性](scripts/sample-cli-same-zone-ha.md) | 在创建 Azure Database for MySQL 灵活服务器时启用相同区域高可用性。|
|**管理服务器**||
| [重启、停止、启动服务器](scripts/sample-cli-restart-stop-start.md)| 在单个 Azure Database for MySQL 灵活服务器上执行重启、停止和启动操作。 |
| [更改服务器参数](scripts/sample-cli-change-server-parameters.md) | 更改单个 Azure Database for MySQL 灵活服务器的服务器参数。 |
|**复制**||
| [创建只读副本](scripts/sample-cli-read-replicas.md) | 在单个 Azure Database for MySQL 灵活服务器中创建和管理只读副本。 |
|**配置日志**||
| [配置审核日志](scripts/sample-cli-audit-logs.md) | 在单个 Azure Database for MySQL 灵活服务器上配置审核日志。 |
| [配置慢查询日志](scripts/sample-cli-slow-query-logs.md) | 在单个 Azure Database for MySQL 灵活服务器上配置慢查询日志。 |

