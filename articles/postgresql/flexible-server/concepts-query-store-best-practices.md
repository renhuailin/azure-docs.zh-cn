---
title: Azure Database for PostgreSQL 灵活服务器中的查询存储最佳做法
description: 本文介绍了 Azure Database for PostgreSQL 灵活服务器中查询存储的最佳做法。
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 9ee2dc4b3e8ea6a9f892adbc378e8e13b8bd8160
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558569"
---
# <a name="best-practices-for-query-store---flexible-server"></a>查询存储灵活服务器的最佳做法

**适用范围**：Azure Database for PostgreSQL 灵活服务器版本 11、12

本文概述了在 Azure Database for PostgreSQL 中使用查询存储的最佳做法。

## <a name="set-the-optimal-query-capture-mode"></a>设置最佳查询捕获模式
让查询存储捕获重要的数据。 

|**pg_qs.query_capture_mode** | **方案**|
|---|---|
|_全部_  |根据所有查询及其执行频率和其他统计信息彻底分析工作负荷。 识别工作负荷中的新查询。 检测是否使用即席查询来识别用户或自动参数化的机会。 “全部”会增加资源消耗成本。 |
|顶部  |将注意力集中在热门查询 - 客户发布的查询上。
|_无_ |如果设置为“无”，查询存储将不会捕捉任何新查询。 已捕获了要调查的查询集和时间窗口，并且希望消除其他查询可能引入的干扰。 “无”适用于测试和基准测试环境。 由于可能错过了跟踪和优化重要新查询的机会，因此应谨慎使用“无”。 |


> [!NOTE] 
> 将 pgms_wait_sampling.query_capture_mode 替代为 pg_qs.query_capture_mode 。 若 pg_qs.query_capture_mode 设置为“无”，则 pgms_wait_sampling.query_capture_mode 设置无效。 


## <a name="keep-the-data-you-need"></a>保留所需的数据
该 pg_qs.retention_period_in_days 参数指定查询存储的数据保留期（以天为单位）。 删除较旧的查询和统计信息数据。 默认情况下，查询存储配置为将数据保留 7 天。 避免保留不打算使用的历史数据。 若需要将数据保留更长时间，请增大保留期的值。


## <a name="next-steps"></a>后续步骤
- 了解如何使用 [Azure 门户](howto-configure-server-parameters-using-portal.md)或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 获取或设置参数。
