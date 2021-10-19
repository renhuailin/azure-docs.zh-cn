---
title: 使用 Azure Monitor 工作簿监视 Azure Database for MySQL 灵活服务器
description: 介绍如何使用 Azure Monitor 工作簿监视 Azure Database for MySQL 灵活服务器。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: a773048b2d1ddf8ad7b7993ef7975517506bbd07
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620855"
---
# <a name="monitoring-azure-database-for-mysql---flexible-server-with-azure-monitor-workbooks"></a>使用 Azure Monitor 工作簿监视 Azure Database for MySQL 灵活服务器

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Azure Database for MySQL 灵活服务器现已与 Azure Monitor 工作簿集成。 工作簿提供了一块灵活的画布，以用于分析数据以及在 Azure 门户中创建丰富的视觉报告。 使用工作簿可以在整个 Azure 中接入多个数据源，并将其组合成统一的交互式体验。 工作簿模板充当特选的报告，可供多个用户和团队灵活重复使用。 打开某个模板会创建一个临时工作簿，其中填充了该模板的内容。 通过这种集成，服务器可以链接到工作簿和少量示例模板，这有助于大规模监视服务。可对这些模板进行编辑、根据客户要求进行自定义并将其固定到仪表板，以创建具有针对性并井然有序的 Azure 资源视图。
 
本文介绍可用于灵活服务器的各种工作簿模板

有三个默认模板可用于 Azure Database for MySQL 灵活服务器
 
- 概述：提供实例摘要和顶级指标，以帮助了解服务器上的资源利用率。 你可以查看 Azure Database for MySQL 灵活服务器的以下详细信息

    * 服务器摘要 
    * 数据库摘要
    * 连接指标 
    * 性能指标 
    * 存储指标 

* 审核：为服务器收集的审核事件的摘要和详细信息。 此 Azure Database for MySQL 灵活服务器模板提供以下视图

    * 服务上的管理操作
    * 审核摘要
    * 审核连接事件摘要
    * 审核连接事件
    * 表访问摘要
    * 识别到的错误

* 查询性能见解：实例上的查询工作负载、长时间运行的查询、缓慢查询分析和连接指标的摘要和详细信息。 此 Azure Database for MySQL 灵活服务器模板提供以下视图。

    * 查询负载
    * 活动连接总数
    * 缓慢查询趋势（查询时间超过 10 秒）
    * 缓慢查询详细信息
    * 列出五条最长的查询
    * 按最小值、最大值、平均值和标准偏差查询时间汇总慢查询

还可以编辑这些模板，并根据要求对其进行自定义。 有关详细信息，请参阅 [Azure Monitor 工作簿概述 - Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md#editing-mode)

 ## <a name="how-to-access-workbook-templates"></a>如何访问工作簿模板

若要查看模板，请在 Azure 门户上，导航到 Azure Database for MySQL 灵活服务器的“监视”边栏选项卡，然后选择“工作簿” 。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-all.png" alt-text="显示工作簿的插图。":::

还可以导航到“公共模板”来查看模板列表。

:::image type="content" source="./media/concept-workbook/monitor-workbooks-public.png" alt-text="显示工作簿模板的插图":::


## <a name="next-steps"></a>后续步骤
- 参阅 [Azure Monitor 工作簿](../../azure-monitor/visualize/workbooks-access-control.md)，详细了解工作簿丰富的可视化效果选项。
- 参阅 [Azure Monitor 工作簿入门](../../azure-monitor/visualize/workbooks-overview.md#visualizations)，详细了解工作簿丰富的可视化效果选项。
