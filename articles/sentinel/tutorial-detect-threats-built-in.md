---
title: 使用 Azure Sentinel 中的内置分析规则检测威胁 | Microsoft Docs
description: 了解如何使用基于内置模板的开箱即用威胁检测规则，这些规则可在发生可疑情况时通知你。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2021
ms.author: yelevin
ms.openlocfilehash: 951c616961ff68b810ca135d09a6f6253cb2b7ba
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773549"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教程：检测最新威胁

[将数据源连接](quickstart-onboard.md)到 Azure Sentinel 后，你希望在发生可疑情况时收到通知。 因此，Azure Sentinel 提供了开箱即用的内置模板来帮助你创建威胁检测规则。 这些模板是由 Microsoft 的安全专家和分析师团队基于已知的威胁、常见的攻击途径和可疑活动升级链设计的。 基于这些模板创建的规则将自动在整个环境中搜索任何看起来可疑的活动。 可以自定义许多模板以搜索活动，或根据需要筛选它们。 这些规则生成的警报将创建可在环境中分配和调查的事件。

本教程将帮助你使用 Azure Sentinel 检测威胁：

> [!div class="checklist"]
> * 使用开箱即用威胁检测
> * 自动响应威胁

## <a name="about-out-of-the-box-detections"></a>关于开箱即用检测

若要查看所有现成的检测，请转到 **Analytics**，然后转到“规则模板”。 此选项卡包含所有的 Azure Sentinel 内置规则。

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="使用 Azure Sentinel 通过内置检测来查找威胁":::

可用的模板类型如下：

- **Microsoft 安全**
   
   Microsoft 安全模板根据其他 Microsoft 安全解决方案中生成的警报自动实时创建 Azure Sentinel 事件。 可以使用 Microsoft 安全规则作为模板，以创建具有类似逻辑的新规则。 有关安全规则的详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。

- **合成** 

    基于 Fusion 技术，Azure Sentinel 中的高级多阶段攻击检测使用可缩放的机器学习算法，该算法可将多个产品中的许多低保真警报和事件关联到高保真和可操作的事件中。 默认情况下启用 Fusion。 由于逻辑是隐藏的，因此无法自定义，只能使用此模板创建一个规则。

    > [!IMPORTANT]
    > Fusion 规则模板中的某些检测当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
    >
    > 若要了解哪些检测为预览版，请参阅 [Azure Sentinel 中的高级多阶段攻击检测](fusion.md)。

- **机器学习行为分析**

    这些模板基于专有的 Microsoft 机器学习算法，因此无法查看其工作方式和运行时间的内部逻辑。 由于逻辑是隐藏的，因此无法自定义，只能使用此类型的每个模板创建一个规则。

    > [!IMPORTANT]
    > - 机器学习行为分析规则模板当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
    >
    > - 通过基于 ML 行为分析模板创建和启用任何规则，你可以向 Microsoft 授予权限，以便将引入的数据复制到 Azure Sentinel 工作区的地理位置之外，以供机器学习引擎和模型进行处理。

- **计划**

    计划分析规则基于 Microsoft 安全专家编写的内置查询。 可以查看查询逻辑并对其进行更改。 可以使用计划规则模板并自定义查询逻辑和计划设置以创建新规则。

## <a name="use-out-of-the-box-detections"></a>使用开箱即用检测

1. 若要使用内置模板，请单击模板名称，然后单击详细信息窗格中的“创建规则”按钮，基于该模板创建新的活动规则。 每个模板都具有所需数据源的列表。 打开模板时，会自动检查数据源的可用性。 如果存在可用性问题，则可能会禁用“创建规则”按钮，或者你可能会看到有关此影响的警告。
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="检测规则预览面板":::
 
1. 单击“创建规则”按钮将根据所选模板打开规则创建向导。 所有详细信息都将自动填充，通过使用“计划”或“Microsoft 安全”模板可以自定义逻辑和其他规则设置，以更好地满足你的特定需求 。 可以重复此过程以基于内置模板创建其他规则。 完成规则创建向导中的步骤后，你就完成了基于模板创建规则的过程。 新规则将显示在“活动规则”选项卡中。

    有关如何在规则创建向导中自定义规则的详细信息，请参阅[教程：创建自定义分析规则以检测威胁](tutorial-detect-threats-custom.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何开始使用 Azure Sentinel 检测威胁。 

若要了解如何自动完成对威胁的响应，请参阅[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。
