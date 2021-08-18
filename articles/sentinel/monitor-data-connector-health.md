---
title: 通过此 Azure Sentinel 工作簿监视数据连接器的运行状况 | Microsoft Docs
description: 使用“运行状况监视”工作簿跟踪数据连接器的连接性和性能。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 1b8507f29d157cd715d6423c773b01a561a5e181
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737564"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>通过此 Azure Sentinel 工作簿监视数据连接器的运行状况

“数据连接器运行状况监视”工作簿可用于跟踪 Azure Sentinel 内的数据连接器的运行状况、连接性和性能。 该工作簿提供了其他监视器，检测异常情况，并提供了有关工作区的数据引入状态的见解。 可以使用该工作簿的逻辑来监视引入数据的一般运行状况，并生成自定义视图和基于规则的警报。

## <a name="use-the-health-monitoring-workbook"></a>使用“运行状况监视”工作簿

1. 在 Azure Sentinel 门户中，从“威胁管理”菜单中选择“工作簿”。

1. 在“工作簿”库中，在搜索栏中输入“运行状况”，然后从结果中选择“数据收集运行状况监视”。

1. 选择“查看模板”以按原样使用工作簿，或选择“保存”以创建工作簿的可编辑副本。 创建复制时，选择“查看保存的工作簿”。

1. 进入工作簿后，首先选择要查看的“订阅”和“工作区”，然后定义 TimeRange 以根据需要筛选数据。 使用“显示帮助”开关显示工作簿的就地说明。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="“数据连接器运行状况监视”工作簿登陆页面" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

此工作簿中有三个选项卡式部分：

1. “概述”选项卡显示所选工作区中数据引入的一般状态：卷度量值、EPS 速率和上次接收日志的时间。

1. “数据收集异常”选项卡将帮助你按表和数据源检测数据收集过程中的异常。 每个选项卡都显示特定表的异常情况（“常规”选项卡包含表的集合）。 使用 series_decompose_anomalies() 函数计算异常，该函数会返回异常分数。  [详细了解此函数](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx)。 为要计算的函数设置以下参数：

    - **AnomaliesTimeRange**：此时间选取器仅适用于数据收集异常视图。
    - **SampleInterval**：给定时间范围内的数据采样时间间隔。 仅对最后一个时段内的数据计算异常分数。
    - **PositiveAlertThreshold**：此值定义为正的异常分数阈值。 它接受小数值。
    - **NegativeAlertThreshold**：此值定义为负的异常分数阈值。 它接受小数值。

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="“数据连接器运行状况监视”工作簿异常情况页面" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. “代理信息”选项卡显示有关安装在各种计算机（无论是 Azure VM、其他云 VM、本地 VM 还是物理计算机）上的 Log Analytics 代理的运行状况信息。 可监视以下方面：

   - 系统位置

   - 检测信号状态和延迟

   - 可用内存和磁盘空间

   - 代理操作

    在本部分中，必须选择描述计算机环境的选项卡：如果只想查看 Azure Arc 托管的计算机，请选择“Azure 托管的计算机”选项卡；选择“所有计算机”选项卡可查看安装了 Log Analytics 代理的托管计算机和非 Azure 计算机。

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="“数据连接器运行状况监视”工作簿代理信息页面" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>后续步骤
了解如何[将数据载入到 Azure Sentinel](quickstart-onboard.md)、[连接数据源](connect-data-sources.md)以及[获取数据和潜在威胁的见解](get-visibility.md)。