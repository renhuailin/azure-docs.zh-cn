---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：优化
description: 使用 Azure 指标，不仅可以监视审核与活动日志，还可以监视服务器性能。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 4a1f279ad8eb81891a184700b1076bcec89b92e8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296208"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-optimization"></a>将本地 MySQL 迁移到 Azure Database for MySQL：优化

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[迁移后管理](10-post-migration-management.md)

## <a name="monitoring-hardware-and-query-performance"></a>监视硬件和查询性能

使用 [Azure 指标](../../../azure-monitor/essentials/data-platform-metrics.md)，不仅可以监视审核和活动日志，还可以监视服务器性能。 每分钟提供一次 Azure 指标，可以通过 Azure 指标来配置警报。 更多详细信息，请参阅[在 Azure Database for MySQL 中进行监视](../../concepts-monitoring.md)以具体了解可以监视的指标类型。

如上所述，在决定是否升级数据库层时，监视 CPU \_百分比或内存\_百分比等指标可能非常重要。 如果值始终很高，则可能表示需要进行层升级。

此外，如果 CPU 和内存似乎不是问题，管理员可以浏览基于数据库的选项（例如索引编制和查询修改），以找出性能不佳的查询。

要查找性能不佳的查询，请运行以下命令：

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s| top 5 by query\_time\_d desc
```

## <a name="query-performance-insight"></a>Query Performance Insight

除了基本服务器监视方面以外，Azure 还提供用于监视应用程序查询性能的工具。 更正或改进查询可能使查询吞吐量大幅增加。 使用 [Query Performance Insight 工具](../../concepts-query-performance-insight.md)分析运行时间最长的查询，并确定如果这些项在设定期间内具有确定性，是否可缓存这些项，或修改查询以提高其性能。

可以将 `slow\_query\_log` 设置为在 MySQL 日志文件中显示慢速查询（默认为“OFF”）。 服务器参数 `long\_query\_time` 可以提醒用户注意长时间运行的查询（默认为 10 秒）。

## <a name="upgrading-the-tier"></a>升级层

可以使用 Azure 门户在 `General Purpose` 与 `Memory Optimized` 之间缩放。 如果选择 `Basic` 层，则以后不会出现将层升级到 `General Purpose` 或 `Memory Optimized` 的选项。 但是，可以利用其他技术来迁移/升级到新的 Azure Database for MySQL 实例。

有关从基本层迁移到另一个服务器层的脚本示例，请参考[在 Azure Database for MySQL 中从“基本”层升级到“通用”层或“内存优化”层](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)。

## <a name="scale-the-server"></a>缩放服务器

在层中，可以将内核数和内存大小缩放至该层中允许的最小和最大限制。 如果监视显示 CPU 或内存持续接近最大值，则遵循[纵向扩展的步骤以满足您的需求。](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)

## <a name="moving-regions"></a>移动区域

将数据库移到不同的 Azure 区域取决于方法和体系结构。 采用特定方法时，这可能导致系统停机。

建议的过程与利用只读副本进行维护故障转移的过程相同。 但是，与上述计划内维护方法相比，在应用程序中实现故障转移层后，故障转移速度要快得多。 在只读副本故障转移过程中，应用程序只需停机几分钟。 “业务连续性和灾难恢复”一节中介绍了更多详细信息。

## <a name="wwi-scenario"></a>WWI 方案

WWI 业务和应用程序用户对于按需扩展数据库的功能感到非常激动。 他们还希望使用 Query Performance Insight 来确定是否需要解决长时间运行的查询的性能。

他们选择利用一个只读副本服务器来解决任何潜在的故障转移或需要只读的情况。

迁移团队与 Azure 工程师合作，设置 KQL 查询来监视 MySQL 服务器性能的任何潜在问题。 已设置 KQY 查询，其中包含警报，以通过电子邮件向数据库和会议团队发送事件问题。

他们选择立即监视任何潜在问题，并在以后根据需要实现 Azure 自动化，以提高运营效率。

## <a name="optimization-checklist"></a>优化核对清单

  - 监视速度缓慢的查询。

  - 定期查看性能见解仪表板。

  - 利用监视来推动层升级和规模决定。

  - 考虑移动用户或应用程序需要更改的区域。  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [业务连续性和灾难恢复 (BCDR)](./12-business-continuity-and-disaster-recovery.md)