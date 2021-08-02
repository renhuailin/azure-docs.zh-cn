---
title: 备份和还原 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 保护数据免于意外损坏或删除
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 7681e9c28bbbbcec06bcc1cf2bf469f1b4189d79
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520167"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure Database for PostgreSQL - 超大规模 (Citus) 中进行备份和还原

Azure Database for PostgreSQL - 超大规模 (Citus) 自动创建每个节点的备份，并将其存储在本地冗余存储中。 备份可用于将超大规模 (Citus) 服务器组还原到指定时间。
备份和还原是任何业务连续性策略的基本组成部分，因为它们可以保护数据免遭意外损坏或删除。

## <a name="backups"></a>备份

Azure Database for PostgreSQL 拍摄数据文件和数据库事务日志的快照备份，至少一天一次。 可以通过这些备份将服务器还原到保留期中的任意时间点。 （所有服务器组目前的保持期均为 35 天。）所有备份都使用 AES 256 位加密进行加密。

在支持可用性区域的 Azure 区域中，备份快照存储在三个可用性区域中。 只要至少有一个可用性区域处于联机状态，超大规模 (Citus) 服务器组就是可还原的。

无法导出备份文件。 这些文件只能用于 Azure Database for PostgreSQL 中的还原操作。

### <a name="backup-storage-cost"></a>备份存储成本

若要了解当前的备份存储定价，请参阅 Azure Database for PostgreSQL - 超大规模 (Citus) [定价页](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/)。

## <a name="restore"></a>还原

可以将超大规模 (Citus) 服务器组还原到过去 35 天内的任何时间点。  多种情况下可以使用时间点还原。 例如，当用户意外删除数据、重要的表或数据库时，或者应用程序意外使用错误数据覆盖了正确的数据。

> [!IMPORTANT]
> 无法还原已删除的超大规模 (Citus) 服务器组。 如果删除服务器组，则属于该服务器组的所有节点都会被删除且无法恢复。 为了防止服务器组资源在部署后遭意外删除或意外更改，管理员可以利用[管理锁](../azure-resource-manager/management/lock-resources.md)。

还原过程会在原始服务器组所在的 Azure 区域、订阅和资源组中创建新服务器组。 该服务器组具有原始服务器组的配置：相同的节点数、vCore 数、存储大小、用户角色、PostgreSQL 版本和 Citus 扩展的版本。

不会保留原始服务器组中的防火墙设置和 PostgreSQL 服务器参数，它们会重置为默认值。 防火墙将阻止所有连接。 还原后需要手动调整这些设置。 通常请参阅建议的[还原后任务](howto-hyperscale-restore-portal.md#post-restore-tasks)的列表。

## <a name="next-steps"></a>后续步骤

* 请查看在 Azure 门户中[还原服务器组](howto-hyperscale-restore-portal.md)的步骤。
* 了解  [Azure 可用性区域](../availability-zones/az-overview.md)。
