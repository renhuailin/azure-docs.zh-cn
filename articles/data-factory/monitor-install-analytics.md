---
title: 从 Azure 市场安装 Azure 数据工厂分析解决方案
description: 了解如何从 Azure 市场安装 Azure 数据工厂分析解决方案。
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837631"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>从 Azure 市场安装 Azure 数据工厂分析解决方案

此解决方案提供数据工厂总体运行状况的摘要，并提供用于钻取详细信息以及排查意外行为模式的选项。 使用丰富的现成视图可以洞察关键的处理活动，包括：

* 数据工厂管道、活动和触发器运行的一览式摘要
* 可以按类型钻取到数据工厂活动运行
* 数据工厂中最常出现的管道和活动错误的摘要

1. 转到“Azure 市场”，选择“分析”筛选器，然后搜索“Azure 数据工厂分析(预览版)”  

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="转到“Azure 市场”，输入“分析筛选器”，然后选择“Azure 数据工厂分析(预览版)”":::&quot;&quot;&quot;&quot;&quot;&quot;

1. 有关“Azure 数据工厂分析(预览版)”的详细信息

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="有关“Azure 数据工厂分析(预览版)”的详细信息":::&quot;&quot;

1. 选择“创建”，然后创建或选择“Log Analytics 工作区”。 

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="创建新解决方案":::

## <a name="monitor-data-factory-metrics"></a>监视数据工厂指标

安装此解决方案会在所选 Log Analytics 工作区的工作簿部分中创建一组默认视图。 因此，将启用以下指标：

* ADF 运行 - 1) 数据工厂中的管道运行
* ADF 运行 - 2) 数据工厂中的活动运行
* ADF 运行 - 3) 数据工厂中的触发器运行
* ADF 错误 - 1) 数据工厂中最常出现的 10 个管道错误
* ADF 错误 - 2) 数据工厂中最常出现的 10 个活动运行
* ADF 错误 - 3) 数据工厂中最常出现的 10 个触发器错误
* ADF 统计信息 - 1) 按类型列出的活动运行
* ADF 统计信息 - 2) 按类型列出的触发器运行
* ADF 统计信息 - 3) 最长管道运行持续时间

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="突出显示了“工作簿(预览版)”和“AzureDataFactoryAnalytics”的窗口":::&quot;&quot;&quot;&quot;

可将上述指标可视化，查看这些指标背后的查询，编辑查询，创建警报，以及执行其他操作。

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="数据工厂中的管道运行的图形表示形式":::

> [!NOTE]
> Azure 数据工厂分析（预览版）将诊断日志发送到“资源特定”目标表。 可以针对以下表编写查询：_ADFPipelineRun_、_ADFTriggerRun_ 和 _ADFActivityRun_。

## <a name="next-steps"></a>后续步骤

[以编程方式监视和管理管道](monitor-programmatically.md)
