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
ms.date: 05/11/2021
ms.author: yelevin
ms.openlocfilehash: 95158fc5aa9d768e7174bc3c72736614efa7f57c
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111371646"
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

以下部分描述了可用的现成模板类型：

### <a name="microsoft-security"></a>Microsoft 安全

Microsoft 安全模板根据其他 Microsoft 安全解决方案中生成的警报自动实时创建 Azure Sentinel 事件。 可以使用 Microsoft 安全规则作为模板，以创建具有类似逻辑的新规则。

有关安全规则的详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。

### <a name="fusion"></a>合成

基于 Fusion 技术，Azure Sentinel 中的高级多阶段攻击检测使用可缩放的机器学习算法，该算法可将多个产品中的许多低保真警报和事件关联到高保真和可操作的事件中。 默认情况下启用 Fusion。 由于逻辑是隐藏的，因此无法自定义，只能使用此模板创建一个规则。

> [!IMPORTANT]
> Fusion 规则模板中的某些检测当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>
> 若要了解哪些检测为预览版，请参阅 [Azure Sentinel 中的高级多阶段攻击检测](fusion.md)。

此外，Fusion 引擎现在可以将[计划分析规则](#scheduled)生成的警报与来自其他系统的警报相关联，从而生成高保真事件。

### <a name="machine-learning-ml-behavioral-analytics"></a>机器学习 (ML) 行为分析

这些模板基于专有的 Microsoft 机器学习算法，因此无法查看其工作方式和运行时间的内部逻辑。 由于逻辑是隐藏的，因此无法自定义，只能使用此类型的每个模板创建一个规则。

> [!IMPORTANT]
> - 机器学习行为分析规则模板当前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>
> - 通过基于 ML 行为分析模板创建和启用任何规则，你可以向 Microsoft 授予权限，以便将引入的数据复制到 Azure Sentinel 工作区的地理位置之外，以供机器学习引擎和模型进行处理。

### <a name="anomaly"></a>异常

异常规则模板使用 SOC-ML（机器学习）来检测特定类型的异常行为。 每个规则都有其独特的参数和阈值，适合于所分析的行为，虽然不能更改或微调其配置，但可以复制规则，更改和微调副本，在“外部测试”模式下运行副本，在“生产”模式下同时运行原始副本，比较结果，并在需要微调时将副本切换到“生产”  。 详细了解 [SOC-ML](soc-ml-anomalies.md) 和[使用异常规则](work-with-anomaly-rules.md)。

> [!IMPORTANT]
> 异常规则模板目前处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

### <a name="scheduled"></a>计划

计划分析规则基于 Microsoft 安全专家编写的内置查询。 可以查看查询逻辑并对其进行更改。 可以使用计划规则模板并自定义查询逻辑和计划设置以创建新规则。

几个新的计划分析规则模板生成警报，Fusion 引擎将这些警报与来自其他系统的警报相关联，以生成高保真事件。 有关详细信息，请参阅[高级多阶段攻击检测](fusion.md#configure-scheduled-analytics-rules-for-fusion-detections)。

> [!TIP]
> 规则计划选项包括将规则配置为每隔指定的分钟数、小时数或天数运行，并在启用规则时开始计时。
>
> 建议在启用新的或已编辑的分析规则时多加小心，以确保这些规则能够及时获取新的事件堆栈。 例如，你可能希望在 SOC 分析师开始工作时同步运行规则，然后启用这些规则。
>

## <a name="use-out-of-the-box-detections"></a>使用开箱即用检测

1. 若要使用内置模板，请单击模板名称，然后单击详细信息窗格中的“创建规则”按钮，基于该模板创建新的活动规则。 每个模板都具有所需数据源的列表。 打开模板时，会自动检查数据源的可用性。 如果存在可用性问题，则可能会禁用“创建规则”按钮，或者你可能会看到有关此影响的警告。

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="检测规则预览面板":::

1. 单击“创建规则”按钮将根据所选模板打开规则创建向导。 所有详细信息都将自动填充，通过使用“计划”或“Microsoft 安全”模板可以自定义逻辑和其他规则设置，以更好地满足你的特定需求 。 可以重复此过程以基于内置模板创建其他规则。 完成规则创建向导中的步骤后，你就完成了基于模板创建规则的过程。 新规则将显示在“活动规则”选项卡中。

    有关如何在规则创建向导中自定义规则的详细信息，请参阅[教程：创建自定义分析规则以检测威胁](tutorial-detect-threats-custom.md)。

## <a name="export-rules-to-an-arm-template"></a>将规则导出到 ARM 模板

如果要将规则作为代码进行管理和部署，可以轻松[将规则导出到 Azure 资源管理器 (ARM) 模板](import-export-analytics-rules.md)。 还可以从模板文件导入规则，以便在用户界面中进行查看和编辑。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何开始使用 Azure Sentinel 检测威胁。

若要了解如何自动完成对威胁的响应，请参阅[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。
