---
title: 业务连续性 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍使用 Azure Database for PostgreSQL 时的业务连续性（时间点还原、数据中心服务中断、异地还原、副本）。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: f8d8547c2d4900e6258f7360c50d8dfc17157832
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517238"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>有关使用 Azure Database for PostgreSQL - 单一服务器确保业务连续性的概述

本概述介绍了 Azure Database for PostgreSQL 针对业务连续性和灾难恢复所提供的功能。 了解在发生破坏性事件后用于进行恢复的选项，破坏性事件可能导致数据丢失或者数据库和应用程序无法使用。 了解对一些情况的处理方式，包括用户或应用程序错误影响数据完整性、Azure 区域发生服务中断，或者应用程序需要维护。

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>可用来提供业务连续性的功能

制定业务连续性计划时，需了解应用程序在破坏性事件发生后完全恢复前的最大可接受时间，即恢复时间目标 (RTO)。 此外，还需要了解从破坏性事件恢复时，应用程序可忍受丢失的最近数据更新（时间间隔）最大数量，即恢复点目标 (RPO)。

Azure Database for PostgreSQL 提供了业务连续性功能，这些功能包括能够启动异地还原的异地冗余备份，以及将只读副本部署到不同区域中的功能。 每种功能在恢复时间和可能丢失数据方面都有不同的特性。 启用[异地还原](concepts-backup.md)功能时，可以使用从另一个区域复制的备份数据创建新的服务器。 还原和恢复所需的总时间取决于数据库的大小和要恢复的日志数量。 建立服务器的总时间从几分钟到几小时不等。 使用[只读副本](concepts-read-replicas.md)，来自主数据库的事务日志会以异步方式流式传输到副本。 如果由于地区级或区域级故障导致主数据库中断，则故障转移到副本可提供较短的 RTO，并会减少数据丢失的情况。

> [!NOTE]
> 主数据库和副本之间的延迟取决于站点之间的延迟以及要传输的数据量，最重要的是取决于主服务器的写入工作负载。 大量写入工作负载可能会产生明显的延迟。 
>
> 由于用于只读副本的复制技术具有异步特性，因此不应将它们视为高可用性 (HA) 解决方案，更高的延迟可能意味着 RTO 和 RPO 更高。 仅针对在工作负载高峰和非高峰时间延迟仍然较小的工作负载，只读副本才可以用作 HA 的替代方案。 否则，只读副本将用于真正的读取缩放，以应对频繁读取的工作负载和（灾难恢复）DR 场景。

下表比较了典型工作负载中的 RTO 和 RPO：

| **功能** | **基本** | **常规用途** | **内存优化** |
| :------------: | :-------: | :-----------------: | :------------------: |
| 从备份执行时间点还原 | 保留期内的任何还原点 <br/> RTO - 可变 <br/>RPO < 15 分钟| 保留期内的任何还原点 <br/> RTO - 可变 <br/>RPO < 15 分钟 | 保留期内的任何还原点 <br/> RTO - 可变 <br/>RPO < 15 分钟 |
| 从异地复制的备份执行异地还原 | 不支持 | RTO - 可变 <br/>RPO < 1 小时 | RTO - 可变 <br/>RPO < 1 小时 |
| 只读副本 | RTO - 几分钟* <br/>RPO < 5 分钟* | RTO - 几分钟* <br/>RPO < 5 分钟*| RTO - 几分钟* <br/>RPO < 5 分钟*|

 \* 在某些情况下，RTO 和 RPO 可能会更高，具体取决于各种因素（包括站点间的延迟，以及重要的主数据库写入工作负载）。 

## <a name="recover-a-server-after-a-user-or-application-error"></a>在发生用户或应用程序错误之后恢复服务器

可以使用服务的备份在发生各种破坏性事件后对服务器进行恢复。 用户可能会不小心删除某些数据、无意中删除重要的表，甚至删除整个数据库。 应用程序可能因为自身缺陷，意外以错误数据覆盖正确数据，等等。

可以执行 **时间点还原** 来创建服务器在已知良好的时间点的副本。 此时间点必须在为服务器配置的备份保留期内。 在将数据还原到新服务器后，可以将原始服务器替换为新还原的服务器，或者将所需的数据从还原的服务器复制到原始服务器。

> [!IMPORTANT]
> 已删除的服务器 **无法** 还原。 如果删除服务器，则属于该服务器的所有数据库也会被删除且不可恢复。 使用 [Azure 资源锁](../azure-resource-manager/management/lock-resources.md)帮助防止意外删除服务器。

## <a name="recover-from-an-azure-data-center-outage"></a>从 Azure 数据中心服务中断进行恢复

Azure 数据中心会罕见地发生中断。 发生中断时，可能仅导致业务中断持续几分钟，也可能持续数小时。

一个选项是等待数据中心中断结束时，服务器重新联机。 这适用于可以承受服务器脱机一段时间的应用程序，例如开发环境。 当数据中心发生服务中断时，你不知道中断可能会持续多长时间，因此该选项仅在一段时间不需要服务器时才有效。

## <a name="geo-restore"></a>异地还原

异地还原功能使用异地冗余备份来还原服务器。 备份托管在服务器的[配对区域](../best-practices-availability-paired-regions.md)中。 可以使用这些备份还原到任何其他区域。 异地还原使用备份中的数据创建新的服务器。 从[备份和还原概念文章](concepts-backup.md)详细了解异地还原。

> [!IMPORTANT]
> 只有当为服务器预配了异地冗余备份存储时，异地还原才是可行的。 如果要从现有服务器的本地冗余切换到异地冗余备份，必须使用现有服务器的 pg_dump 进行转储，然后将其还原到配置了异地冗余的新建服务器中。

## <a name="cross-region-read-replicas"></a>跨区域只读副本
可以使用跨区域只读副本来增强业务连续性和灾难恢复规划。 只读副本使用 PostgreSQL 的物理复制技术进行异步更新，可能与主数据库之间存在延迟。 从[只读副本概念文章](concepts-read-replicas.md)详细了解有关只读副本、可用区域以及如何进行故障转移的信息。 

## <a name="faq"></a>常见问题解答
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Azure Database for PostgreSQL 将客户数据存储在何处？
默认情况下，Azure Database for PostgreSQL 不会将客户数据移出部署的区域。 但是，客户可以选择启用[地域冗余备份](concepts-backup.md#backup-redundancy-options)或创建[跨区域读取副本](concepts-read-replicas.md#cross-region-replication)，以便在另一个区域存储数据。


## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for PostgreSQL 中的自动备份](concepts-backup.md)。 
- 了解如何使用 [Azure 门户](howto-restore-server-portal.md)或 [Azure CLI](howto-restore-server-cli.md) 进行还原。
- 了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
