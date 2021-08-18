---
title: 显示 Azure Sentinel 警报中的自定义详细信息 | Microsoft Docs
description: 在 Azure Sentinel 分析规则的警报中提取和显示自定义事件详细信息，以获取更好、更完整的事件信息
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 18e176f11da9c8dca86c5f3477cb282c478664cf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748068"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>显示 Azure Sentinel 的警报中的自定义事件详细信息 

> [!IMPORTANT]
>
> - “自定义详细信息”功能现为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="introduction"></a>简介

[计划的查询分析规则](detect-threats-custom.md)会分析与 Azure Sentinel 连接的数据源中的事件，并在这些事件从安全角度来说很重要时生成警报 。 这些警报由 Azure Sentinel 的各种引擎进一步分析、分组和筛选，并提炼成值得 SOC 分析人员注意的事件。 不过，当分析人员查看事件时，只有组件警报本身的属性立即可见。 若要了解实际内容（事件中包含的信息），需要进行一些挖掘。

借助分析规则向导中的“自定义详细信息”功能，你可在从这些事件生成的警报中显示事件数据，使事件数据成为警报属性的一部分 。 实际上，你让你能够立即了解事件中的事件内容，从而可更快、更高效地进行会审、调查、得出结论和作出响应。

下面详细介绍的过程是分析规则创建向导的一部分。 我们在这里单独讲解，来处理在现有分析规则中添加或更改自定义详细信息的方案。

## <a name="how-to-surface-custom-event-details"></a>如何呈现自定义事件详细信息

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 选择计划的查询规则，再单击“编辑”。 或者单击屏幕顶部的“创建”>“计划的查询规则”，新建规则。

1. 单击“设置规则逻辑”选项卡。

1. 在“警报扩充(预览)”部分，展开“自定义详细信息”。

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enrichment.png" alt-text="查找并选择自定义详细信息":::

1. 在现已展开的“自定义详细信息”部分中，添加与要显示的详细信息对应的键值对：

    1. 在“键”字段中，输入你选择的名称，该名称将显示为警报中的字段名称。

    1. 在“值”字段中，选择你希望在下拉列表中的警报内显示的事件参数。 此列表将被填入与作为规则查询主题的表中的字段对应的值。
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="添加自定义详细信息":::

1. 单击“新增”来显示更多详细信息，重复最后一个步骤来定义键值对。 

    如果你改变了主意或当时操作错误，可单击自定义详细信息的“值”下拉列表旁边的垃圾桶图标来删除该详细信息。

1. 定义自定义详细信息后，单击“查看并创建”选项卡。规则验证成功后，单击“保存”。

    > [!NOTE]
    > 
    > **服务限制**
    > - 一个分析规则中最多可定义 20 个自定义详细信息。
    >
    > - 所有自定义详细信息的大小限制统一为 2 KB。

## <a name="next-steps"></a>后续步骤
在本文档中，你了解了如何使用 Azure Sentinel 分析规则在警报中显示自定义详细信息。 要详细了解 Azure Sentinel，请参阅以下文章：
- 全面了解[计划的查询分析规则](detect-threats-custom.md)。
- 有关详细信息，请参阅 [Azure Sentinel 中的实体](entities-in-azure-sentinel.md)。
