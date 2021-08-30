---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：业务连续性和灾难恢复 (BCDR)
description: 与任何任务关键型系统一样，备份和还原功能以及灾难恢复策略 (BCDR) 是整体系统设计的重要组成部分。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 35ab4f952b2e8082f4923926f11698fef352c8f8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296224"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-business-continuity-and-disaster-recovery-bcdr"></a>将本地 MySQL 迁移到 Azure Database for MySQL：业务连续性和灾难恢复 (BCDR)

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[优化](11-optimization.md)

## <a name="back-up-and-restore"></a>备份和还原

与任何任务关键型系统一样，备份和还原功能以及灾难恢复策略 (BCDR) 是整体系统设计的重要组成部分。 如果发生意外事件，你必须能够在一段合理的时间内（恢复时间目标）将数据还原到某个时间点（恢复点目标）。

### <a name="backup"></a>备份

默认情况下，Azure Database for MySQL 支持自动备份 7 天。 可根据需要将其修改为当前的最大值 35 天。 请务必注意，如果将该值更改为 35 天，则超过所分配存储空间 1 倍的任何额外备份存储都会收费。

如 [Azure Database for MySQL 中的备份和还原](../../concepts-backup.md)文档文章中所述，数据库备份功能当前存在多个限制。 在决定应执行哪些附加策略时，必须了解这些限制。

需要注意的一些事项包括：

- 不能直接访问备份

- 容量高达 4TB 的层级每周进行一次完整备份，每天进行两次差异备份，每五分钟进行一次日志备份

- 容量高达 16TB 的层级可进行基于快照的备份

    > [!NOTE]
    > [某些区域](../../concepts-pricing-tiers.md#storage)尚不支持高达 16TB 的存储。

### <a name="restore"></a>还原

必须在服务器创建期间配置冗余（本地或异地）。 但可以执行异地还原，并允许在还原过程中修改这些选项。 执行还原操作将暂时停止连接，并且在还原过程中所有应用程序都将关闭。

在数据库还原期间，数据库之外的任何支持项也需要还原。 查看迁移过程。 请参阅[执行还原后任务](../../concepts-backup.md#perform-post-restore-tasks)，了解详细信息。

## <a name="read-replicas"></a>只读副本

[只读副本](../../concepts-read-replicas.md)可用于提高 MySQL 读取吞吐量、提高区域用户的性能并实施灾难恢复。 创建一个或多个只读副本时，请注意，与主服务器相同的计算和存储将收取额外费用。

## <a name="deleted-servers"></a>已删除的服务器

如果管理员或恶意参与者在 Azure 门户中或通过自动化方法删除了服务器，则所有备份和只读副本也将被删除。 请务必在 Azure Database for MySQL 资源组上创建[资源锁](../../../azure-resource-manager/management/lock-resources.md)，以便为实例添加一层额外的删除保护。

## <a name="regional-failure"></a>区域性故障

尽管很少见，但如果发生区域性故障，可以使用异地冗余备份或只读副本让数据工作负载再次运行。 最好同时使用异地复制和只读副本，以针对意外的区域性故障提供最佳保护。

> [!NOTE]
> 更改数据库服务器区域也意味着终结点将会更改，应用程序配置也需要相应地更新。

### <a name="load-balancers"></a>负载均衡器

如果应用程序由世界各地的许多不同实例组成，那么更新所有客户端可能并不切实可行。 利用 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)或[应用程序网关](../../../application-gateway/overview.md)可以实现无缝故障转移功能。 尽管这些工具有用且省时，但区域性故障转移功能并不需要这些工具。

## <a name="wwi-scenario"></a>WWI 方案

WWI 希望测试只读副本的故障转移功能，因此方案执行了以下概述步骤。

### <a name="creating-a-read-replica"></a>创建只读副本

- 打开 Azure 门户。

- 浏览到 Azure Database for MySQL 实例。

- 在“设置”下，选择“复制” 。

- 选择“添加副本”。

- 键入服务器名称。

- 选择区域。

- 选择“确定”，等待实例部署。 根据主实例的大小，复制可能需要一些时间。

    > [!NOTE]
    > 每个副本将产生与主实例相等的额外费用。

### <a name="fail-over-to-read-replica"></a>故障转移到只读副本

创建只读副本并完成复制过程后，即可将其用于故障转移。 复制将在故障转移期间停止，并将只读副本作为自己的主实例。

故障转移步骤：

- 打开 Azure 门户。

- 浏览到 Azure Database for MySQL 实例。

- 在“设置”下，选择“复制” 。

- 选择其中一个只读副本。

- 选择“停止复制”。 这将中断只读副本。

- 修改所有应用程序连接字符串，使其以指向新的主实例。

## <a name="bcdr-checklist"></a>BCDR 清单

- 修改备份频率以满足要求。

- 为读取密集型工作负载和区域故障转移设置只读副本。

- 在资源组上创建资源锁。

- 为应用程序实施负载平衡策略以实现快速故障转移。  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安全性](./13-security.md)