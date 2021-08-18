---
title: 在 Azure Sentinel 中自定义警报的详细信息 | Microsoft Docs
description: 根据警报内容自定义警报的命名和描述方法，以及警报的严重性和分配策略。
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
ms.openlocfilehash: 0b6d64167451b3c7363d6bd80c5fafcd67b6fc5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740182"
---
# <a name="customize-alert-details-in-azure-sentinel"></a>在 Azure Sentinel 自定义警报的详细信息 

> [!IMPORTANT]
>
> - 警报详细信息功能现为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="introduction"></a>简介

为计划分析规则定义名称和说明，并为其分配严重性和 MITRE ATT&CK 策略时，由特定规则生成的所有警报（以及因此创建的所有事件）将使用相同的名称、说明等显示，而不考虑特定警报实例的特定内容。

借助警报详细信息功能，你可以根据警报的内容定制警报的外观。 在这里，你可以选择警报中的参数，这些参数可以在警报的每个实例的名称或说明得以表示，也可以包含分配给该警报实例的策略和严重性。 如果所选参数没有值（如果是策略和严重性的话，则值为无效值），那么警报详细信息将还原至向导第一页中指定的默认值。

下面详细介绍的过程是分析规则创建向导的一部分。 我们在这里单独讲解，来处理在现有分析规则中添加或更改警报详细信息的方案。

## <a name="how-to-customize-alert-details"></a>如何自定义警报详细信息

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 选择计划的查询规则，再单击“编辑”。 或者单击屏幕顶部的“创建”>“计划的查询规则”，新建规则。

1. 单击“设置规则逻辑”选项卡。

1. 在“警报扩充（预览版）”这一部分，扩展“警报详细信息”。

    :::image type="content" source="media/customize-alert-details/alert-enrichment.png" alt-text="自定义警报详细信息":::

1. 在现已展开的“警报详细信息”部分中，添加自由文本（包含你希望在警报中显示的详细信息对应的参数）：

    1. 在“警报名称格式”字段中，输入你希望显示为警报名称的文本（警报文本），并（在双大括号中）包含要作为警报文本一部分的任何参数。

        示例：`Alert from {{ProviderName}}: {{AccountName}} failed to log on to computer {{ComputerName}} with IP address {{IPAddress}}.`

    1. 对“警报说明格式”字段执行相同的操作。
    
    1. 只有当你的查询结果包含此信息的列时，才使用“策略列”和“严重性列”字段。 对于每个字段，请选择包含相应信息的列。

    如果你改变了主意，或者出了错，可以通过单击“策略/严重性列”字段旁边的垃圾桶图标删除警报详细信息，或者从“警报名称/说明格式”字段中删除自由文本。

1. 完成自定义警报详细信息后，请继续到向导中的下一个选项卡。 如果要编辑现有规则，请单击“查看并创建”选项卡。规则验证成功后，单击“保存”。

## <a name="next-steps"></a>后续步骤
在本文档中，你了解了如何在 Azure Sentinel 分析规则下自定义警报详细信息。 要详细了解 Azure Sentinel，请参阅以下文章：
- 全面了解[计划的查询分析规则](detect-threats-custom.md)。
- 有关详细信息，请参阅 [Azure Sentinel 中的实体](entities-in-azure-sentinel.md)。
