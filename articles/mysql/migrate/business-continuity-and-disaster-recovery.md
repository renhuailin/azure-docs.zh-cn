---
title: 从本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 业务连续性和灾难恢复 (BCDR)
description: 与任何关键任务系统一样，备份和恢复以及灾难恢复 (BCDR) 策略是整个系统设计的重要部分。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 6eb944d72f5d4add393079d9fff8e83d37a18e06
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111961533"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-business-continuity-and-disaster-recovery-bcdr"></a>从本地 MySQL 到 Azure Database for MySQL 的迁移指南 - 业务连续性和灾难恢复 (BCDR)

### <a name="backup-and-restore"></a>备份和还原

与任何关键任务系统一样，备份和恢复以及灾难恢复 (BCDR) 策略是整个系统设计的重要部分。 如果发生意外事件，你需要能够在合理的时间内（恢复时间目标）将数据恢复到某个时间点（恢复点目标）。

#### <a name="backup"></a>备份

默认情况下，Azure Database for MySQL 支持自动备份 7 天。 可根据需要将其修改为当前的最大值 35 天。 请务必注意，如果该值更改为 35 天，则超过所分配存储空间 1 倍的任何额外备份存储都会收费。

如 [Azure Database for MySQL 中的备份和还原](../concepts-backup.md)文档文章中所述，数据库备份功能当前存在多个限制。 在决定应该实施哪些额外策略时，必须了解这些限制。

需要注意的一些事项包括：

  - 不能直接访问备份

  - 允许容量高达 4TB 的层每周进行一次完整备份，每天进行两次差异备份，每五分钟进行一次日志记录

  - 容量可高达 16TB 的层的备份是基于快照的

    > [!NOTE] 
    > [某些区域](../concepts-pricing-tiers.md#storage)尚不支持容量高达 16TB 的存储空间。

#### <a name="restore"></a>还原

必须在服务器创建期间配置冗余（本地或异地）。 但可以执行异地还原，并允许在还原过程中修改这些选项。 执行还原操作将暂时停止连接，并且在还原过程中所有应用程序都将关闭。

在数据库还原期间，数据库之外的任何支持项目也需要还原。 查看迁移过程。 有关详细信息，请参阅[执行还原后任务](../concepts-backup.md#perform-post-restore-tasks)。

### <a name="read-replicas"></a>只读副本

[只读副本](../concepts-read-replicas.md)可用于增加 MySQL 读取吞吐量、提高区域用户的性能并实施灾难恢复。 创建一个或多个只读副本时，请注意，与主服务器相同的计算和存储将收取额外费用。

### <a name="deleted-servers"></a>删除的服务器

如果管理员或恶意参与者在 Azure 门户中或通过自动化方法删除服务器，则所有备份和只读副本也将被删除。 应务必在 Azure Database for MySQL 资源组上创建[资源锁](../../azure-resource-manager/management/lock-resources.md)，以便为实例添加额外的删除保护层。

### <a name="regional-failure"></a>区域故障

尽管很少见，但如果发生区域性故障，可以使用异地冗余备份或只读副本让数据工作负载再次运行。 最好同时使用异地复制和只读副本，以针对意外区域故障提供最佳保护。

> [!NOTE]
> 更改数据库服务器区域也意味着终结点将会更改，应用程序配置将需要相应地更新。

#### <a name="load-balancers"></a>负载均衡器

如果应用程序由世界各地的许多不同实例组成，那么可能无法更新所有客户端。 利用 [Azure 负载平衡器](../../load-balancer/load-balancer-overview.md)或[应用程序网关](../../application-gateway/overview.md)来实现无缝故障转移功能。 尽管这些工具有用且省时，但区域故障转移功能不需要这些工具。

### <a name="wwi-scenario"></a>WWI 场景

WWI 希望测试只读副本的故障转移功能，因此他们执行了下面概述的步骤。

#### <a name="creating-a-read-replica"></a>创建只读副本

  - 打开 Azure 门户。

  - 浏览到 Azure Database for MySQL 实例。

  - 在“设置”下，选择“复制” 。

  - 选择“添加副本”。

  - 键入服务器名称。

  - 选择区域。

  - 选择“确定”，等待实例部署。 根据主实例的大小，复制可能需要一些时间。

    > [!NOTE]
    > 每个副本将产生与主实例相等的额外费用。

#### <a name="failover-to-read-replica"></a>故障转移到只读副本

创建只读副本并完成复制过程后，即可将其用于故障转移。 复制将在故障转移期间停止，并使只读副本成为自己的主实例。

故障转移步骤：

  - 打开 Azure 门户。

  - 浏览到 Azure Database for MySQL 实例。

  - 在“设置”下，选择“复制” 。

  - 选择其中一个只读副本。

  - 选择“停止复制”。 这将中断只读副本。

  - 修改所有应用程序连接字符串以指向新的主实例。

### <a name="bcdr-checklist"></a>BCDR 清单

  - 修改备份频率以满足需求。

  - 为读取密集型工作负载和区域故障转移设置只读副本。

  - 在资源组上创建资源锁。

  - 为应用程序实施负载平衡策略以实现快速故障转移。  


> [!div class="nextstepaction"]
> [安全性](./security.md)