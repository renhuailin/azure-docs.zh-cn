---
title: 在 LUIS 门户中测试应用
description: 使用语言理解 (LUIS) 持续优化应用程序并改进其语言理解能力。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.author: aahi
author: aahill
ms.manager: nitinme
ms.date: 06/01/2021
ms.openlocfilehash: d5263f85fa8cc2a9f1b55da32b4aa1418e304347
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954263"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>在 LUIS 门户中测试 LUIS 应用


进行测试过程中，会向 LUIS 提供示例话语并获取 LUIS 识别出的意向和实体响应。 可以通过交互方式测试 LUIS，一次提供一条言语，或提供一组言语。 测试时，可以将当前活动模型的预测响应与已发布模型的预测响应进行比较。 


对应用进行测试是一个迭代过程。 训练 LUIS 应用后，采用示例陈述来对应用进行测试，查看应用是否能准确地识别意向和实体。 如果未能准确识别，请对 LUIS 应用进行更新和训练，然后再次测试。

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="interactive-testing"></a>交互式测试

交互式测试在 LUIS 门户的“测试”  面板中完成。 可输入话语，了解意向和实体的识别和打分方式。 在测试面板中，如果 LUIS 根据话语预测的意向和实体不符合预期，则将其作为新话语复制到“意向”页  。 然后为实体标记该话语的各个部分，并训练 LUIS。 

如果一次测试多个言语，请参阅[批处理测试](./luis-how-to-batch-test.md)，若要详细了解预测分数，请参阅[预测分数](luis-concept-prediction-score.md)一文。

可使用[终结点](luis-glossary.md#endpoint)进行测试，最多可使用两个版本的应用。 将主要或实时版本的应用设置为“生产”终结点，将另一版本添加到“暂存”终结点   。 通过此方法可获得三个版本的话语：[LUIS](luis-reference-regions.md) 门户“测试”窗格中的当前模型，以及两个不同终结点上的两个版本。 

所有终结点测试均计入使用配额。 

## <a name="logging"></a>日志记录

LUIS 将记录的所有话语存储在查询日志中，可从 LUIS 门户上的 **应用** 列表页，以及 LUIS [创作 API](https://go.microsoft.com/fwlink/?linkid=2092087) 下载。 

如果对终结点进行测试，并且不希望记录话语，请记得使用 `logging=false` 查询字符串配置。

[LUIS](luis-reference-regions.md) 门户的[“查看终结点话语”](luis-how-to-review-endpoint-utterances.md)页列出了 LUIS 不确定的所有话语。  

## <a name="test-an-utterance"></a>测试陈述

> [!NOTE]
> 请记住在更改模型后[训练](luis-how-to-train.md) LUIS。 在训练应用前在测试中看不到对 LUIS 应用的更改。
> 1. 登录到 LUIS 门户，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
> 2. 在“我的应用”页上选择应用名称以打开应用。
> 3. 为针对最新版的活动应用进行测试，请在测试之前从顶部菜单中选择“训练”。

测试言语不应与应用中的任何示例言语完全相同。 测试言语应包括预期用户使用的选词、短语长度和实体用法。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。

1. 若要访问“测试”滑出面板，请在应用程序的顶部面板中选择“测试” 。

    > [!div class="mx-imgBorder"]
    > ![训练和测试应用页](./media/luis-how-to-interactive-test/test.png)

1. 在文本框中输入陈述，然后按 Enter。 虽然在“测试”中可键入任意数量的测试陈述，但一次只能键入一个。

1. 陈述的最高意向和分数会添加至文本框下方的陈述列表。

    > [!div class="mx-imgBorder"]
    > ![交互式测试识别错误意向](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>检查预测

在“检查”面板中检查测试结果的详细信息。

1. 打开“测试”滑出面板后，对想要比对的陈述选择“检查” 。

    > [!div class="mx-imgBorder"]
    > ![选择“检查”按钮可查看有关测试结果的更多详细信息](./media/luis-how-to-interactive-test/inspect.png)

1. 此时将显示“检查”面板。 此面板包括评分最高的意向以及任何已识别的实体。 此面板显示所选言语的预测。

    > [!div class="mx-imgBorder"]
    > ![“测试检查”面板的部分屏幕截图](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>添加到示例言语

从检查面板中，可以通过选择“添加到示例言语”将测试言语添加到意图。

## <a name="disable-required-features"></a>禁用所需的功能

此切换按钮可帮助你确定经过训练的应用是否根据所需功能正确预测你的实体。 默认设置是在预测过程中按需应用该功能。 选择此切换按钮可查看不需要该子实体功能时的预测结果。

### <a name="when-to-disable-required-features"></a>何时禁用所需的功能

经过训练的应用可能会基于以下某种情况对机器学习实体做出错误预测：
* 示例言语的标记错误。
* 所需的功能与文本不匹配。

例如，一个机器学习实体具有一个子实体，该子实体是某个人的姓名。

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="具有所需功能的 LUIS 门户机器学习实体架构的屏幕截图":::

此机器学习实体的示例言语为：`Assign Bob Jones to work on the new security feature`。

提取应是 `security feature` 作为票证说明，`Bob Jones` 作为工程师，它们是 `Assign ticket` 实体的两个子实体。

为了帮助子实体成功预测，请将预生成实体 [PersonName](luis-reference-prebuilt-person.md) 作为功能添加到 `engineer` 子实体。 如果你将该功能设为必需，这意味着只有在为文本预测 PersonName 预生成实体时，才会提取子实体。 这意味着文本中未使用 PersonName 子实体预测的任何姓名都不会作为标记的子实体 `engineer` 返回。

使用交互式测试窗格，并看到具有所需功能的子实体未进行预测时，请切换此设置，查看是否可以在不需要该功能的情况下预测子实体。 由于对示例言语进行了正确标记，可能能够在没有所需功能的情况下正确预测子实体。

## <a name="view-sentiment-results"></a>查看情绪结果

如果在[发布](luis-how-to-publish-app.md#enable-sentiment-analysis)页面上配置了“情绪分析”，则测试结果会包括在该陈述中发现的情绪 。

## <a name="correct-matched-patterns-intent"></a>更正匹配的模式的意向

如果使用[模式](luis-concept-patterns.md)并且该陈述与某个模式匹配，但是意向预测错误，请选择该模式旁边的“编辑”链接，然后选择正确的意向。

## <a name="compare-with-published-version"></a>与已发布的版本进行比较

可以使用已发布的[终结点](luis-glossary.md#endpoint)版本测试应用的活动版本。 在“检查”面板中选择“与已发布版本进行比较” 。 针对该发布模型的任何测试都会从 Azure 订阅配额余量中扣除。

> [!div class="mx-imgBorder"]
> ![与已发布版本进行比较](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>在测试面板中查看终结点 JSON
选择“显示 JSON 视图”，可以查看该比较返回的终结点 JSON。

> [!div class="mx-imgBorder"]
> ![已发布的 JSON 响应](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>测试面板中的其他设置

### <a name="luis-endpoint"></a>LUIS 终结点

如果有多个 LUIS 终结点，请使用测试的“已发布”窗格上的“其他设置”链接来更改用于测试的终结点。 如果不确定要使用哪个终结点，请选择默认的“Starter_Key”。

> [!div class="mx-imgBorder"]
> ![突出显示了“其他设置”链接的测试面板](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>批处理测试
请参阅批处理测试[概念](./luis-how-to-batch-test.md)并了解陈述批量测试的[方法](luis-how-to-batch-test.md)。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的准确性。

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endpoint-utterances.md)
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md)
* [最佳实践](luis-concept-best-practices.md)