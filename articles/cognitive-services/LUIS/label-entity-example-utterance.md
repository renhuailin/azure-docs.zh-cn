---
title: 标记实体示例言语
description: 了解如何使用 LUIS 门户的意向详细信息页中的示例言语中的子实体来标记机器学习实体。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: ffbaa2e40d5924ba61e548398e63295cf7dba2b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96019729"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>在示例言语中标记机器学习实体

在示例言语中标记实体可为 LUIS 提供示例，其中包含实体的示例，以及实体可以在言语中出现的位置的示例。

你可以标记机器学习的实体和子实体。

由于无法标记正则表达式、列表或预生成的实体，请创建一个实体或子实体，然后在适用时将这些实体作为特征添加到实体或子实体。

## <a name="label-example-utterances-from-the-intent-detail-page"></a>从意向详细信息页中标记示例言语

若要标记言语中实体的示例，请选择言语的意向。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 选择一个意向，该意向有你想要对其进行标记以使用实体进行提取的示例言语。
1. 选择要标记的文本，然后选择实体。

## <a name="two-techniques-to-label-entities"></a>标记实体的两种方法

意向详细信息页上支持两种标记方法。
* 从[实体调色板](#label-with-the-entity-palette-visible)中选择实体或子实体，然后在示例言语文本中进行选择。 这是建议使用的方法，因为你可以根据自己的架构，直观地验证你使用的是否为正确的实体或子实体。
* 首先在示例言语文本中进行选择。 执行此操作时，会显示[标记选项](#how-to-label-entity-from-in-place-menu)的弹出菜单。

## <a name="label-with-the-entity-palette-visible"></a>显示了“实体调色板”的标签

[规划包含实体的架构](luis-how-plan-your-app.md)后，在标记时请让“实体调色板”保持可见状态。 **实体调色板** 会提醒你计划提取哪些实体。

若要访问 **实体调色板**，请在示例言语列表上方的上下文工具栏中选择 **@** 符号。

> [!div class="mx-imgBorder"]
> ![意向详细信息页上的实体调色板的屏幕截图。](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>如何从实体调色板中标记实体

实体调色板提供之前的标记体验的替代方法。 它允许对文本进行画笔处理，使其能够立即使用实体进行标记。

1. 通过选择言语表右上方的 **@** 符号来打开实体调色板。

2. 从调色板中选择要标记的实体。 此操作采用可视方式指示新的光标。 当你在 LUIS 门户中移动时，光标会跟随鼠标。

3. 在示例言语中，用光标绘制  实体。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示了用光标绘制的实体。](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>从实体调色板中将实体添加为特征

实体调色板的下半部分允许你向当前选定的实体添加特征。 你可以从所有现有实体和短语列表中进行选择，也可以创建新的短语列表。

> [!div class="mx-imgBorder"]
> ![实体调色板的屏幕截图，其中包含作为特征的实体](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>标记实体角色

实体角色使用 **实体调色板** 进行标记。

1. 在“意向详细信息”页上，从上下文工具栏中选择“实体调色板”。
1. 在实体调色板打开后，从实体列表中选择实体。
1. 在实体列表下方，选择一个现有角色。
1. 在示例言语文本中，使用实体角色标记文本。

## <a name="how-to-label-entity-from-in-place-menu"></a>如何从就地菜单中标记实体

就地标记允许你快速选择言语中的文本并对其进行标记。 你还可以从已标记的文本创建机器学习实体或列表实体。

请考虑示例言语 `hi, please I want a cheese pizza in 20 minutes`。

选择最左侧的文本，接着选择实体最右侧的文本，然后从就地菜单中选择想要用其进行标记的实体。

> [!div class="mx-imgBorder"]
> ![标记完整的机器学习实体](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>查看标记的文本

进行标记后，查看示例言语，并确保选定的一段文本中的选定实体带有下划线。 实线指示文本已被标记。

> [!div class="mx-imgBorder"]
> ![已标记的完整机器学习实体](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>确认预测的实体

如果这一段文本被虚线框框起来，则表示此文本已进行预测，但尚未标记。 若要将预测转变为标签，请选择该言语所在的行，然后从上下文工具栏中选择“确认实体”。

## <a name="relabeling-over-existing-entities"></a>在现有实体上重新标记

如果你重新标记已标记的文本，则 LUIS 可以拆分或合并现有标签。

## <a name="labeling-for-punctuation"></a>标点符号的标记

你不需要为标点符号进行标记。 请使用[应用程序设置](luis-reference-application-settings.md)来控制标点符号对言语预测的具体影响。

## <a name="unlabel-entities"></a>取消标记实体

> [!NOTE]
> 仅可将机器已学习的实体取消标记。 不能对正则表达式实体、列表实体或预生成的实体进行标记或取消标记操作。

若要取消标记某个实体，请选择该实体，然后从就地菜单中选择“取消标记”。

> [!div class="mx-imgBorder"]
> ![屏幕截图，显示了如何将实体取消标记](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>父实体和子实体的自动标记

如果你标记父实体，则会标记可根据当前训练的版本对其进行预测的任何子实体。

如果你为某个子实体进行标记，则会自动标记父实体。

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>非机器学习的实体的自动标记

非机器学习的实体包括预生成实体、正则表达式实体、列表实体和 pattern.any 实体。 这些实体由 LUIS 自动标记，因此无需用户手动标记它们。

## <a name="intent-prediction-errors"></a>意向预测错误

意向预测错误表明，系统不会在给出当前训练的应用的情况下针对意向来预测示例言语。

了解如何在意向详细信息页上[查看这些错误](luis-how-to-add-intents.md#intent-prediction-errors)。

## <a name="entity-prediction-errors"></a>实体预测错误

实体预测错误表示预测的实体与标记的实体不匹配。 这通过言语旁边的警告指示器直观显示。

> [!div class="mx-imgBorder"]
> ![机器学习实体的实体调色板](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>后续步骤

使用[仪表板](luis-how-to-use-dashboard.md)并[查看终结点言语](luis-how-to-review-endpoint-utterances.md)来提高应用的预测质量。
