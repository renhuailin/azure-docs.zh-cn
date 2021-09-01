---
title: 性能建议 - Azure Database for MySQL
description: 本文介绍 Azure Database for MySQL 中的“性能建议”功能
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 7cd24a6ca179b2cfc744dae3d5401d4762241960
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651835"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL 中的性能建议

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

**适用于：** Azure Database for MySQL 5.7、8.0

性能建议功能会通过分析数据库来创建自定义的建议，以提高性能。 为了生成建议，分析将查看各种数据库特征（包括架构）。 启用服务器上的[查询存储](concepts-query-store.md)即可充分利用性能建议功能。 如果性能架构为 OFF，则打开查询存储会启用 performance_schema 以及此功能所需的一部分性能架构检测。 实施任何性能建议后，应测试性能以评估这些更改的影响。

## <a name="permissions"></a>权限

使用性能建议功能运行分析所需的“所有者”或“参与者”权限。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能跨服务器分析工作负载以标识可能会提高性能的索引。

从 MySQL 服务器的 Azure 门户页菜单栏的“智能性能”部分打开 **性能建议**。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="性能建议登陆页面":::

选择“分析”并选择数据库就会开始分析。 分析可能需要几分钟的时间才能完成，具体取决于工作负载。 分析完成后，门户中将出现通知。 分析会执行数据库的深层检查。 建议在非高峰期执行分析。

“建议”窗口会显示找到的建议的列表，以及生成该建议的相关查询 ID。 有了查询 ID，就可以使用 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 视图来详细了解查询。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="性能建议新页":::

不会自动应用建议。 若要应用建议，请复制查询文本并从所选的客户端中运行。 记住通过测试和监视来评估建议。

## <a name="recommendation-types"></a>建议类型

### <a name="index-recommendations"></a>索引建议

“创建索引”建议建议使用新索引来加快工作负载中最常运行或最耗时的查询。 此建议类型需要启用[查询存储](concepts-query-store.md)。 查询存储收集查询信息并提供详细的查询运行时和频率统计信息，供分析用来提出建议。

### <a name="query-recommendations"></a>查询建议

查询建议建议对工作负载中的查询进行优化和重写。 通过识别 MySQL 查询反模式并按句法修复它们，可以改进耗时查询的性能。 此建议类型需要启用“查询存储”。 查询存储收集查询信息并提供详细的查询运行时和频率统计信息，供分析用来提出建议。

## <a name="next-steps"></a>后续步骤
- 详细了解如何在 Azure Database for MySQL 中进行[监视和优化](concepts-monitoring.md)。