---
title: 评审用户话语 - LUIS
titleSuffix: Azure Cognitive Services
description: 评审主动学习捕获的言语，以选择意向并标记真实言语的实体；接受更改、训练并发布。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/05/2021
ms.openlocfilehash: 41c45b2887736847c2413c2193e57d35f49594e7
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518051"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>如何通过评审终结点言语来改进 LUIS 应用

评审终结点言语以进行正确预测的过程称为[主动学习](luis-concept-review-endpoint-utterances.md)。 主动学习捕获终结点查询并选择用户不确定的终结点话语。 评审这些言语以选择意向并标记这些真实言语的实体。 将这些更改接受到示例言语中，然后对其进行训练并将其发布。 然后 LUIS 更准确地识别话语。

## <a name="log-user-queries-to-enable-active-learning"></a>记录用户查询以启用主动学习

若要启用主动学习，必须记录用户查询。 这是通过 `log=true` querystring 参数和值调用[终结点查询](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)实现的。

使用 LUIS 门户构造正确的终结点查询。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 转到“管理”部分，然后选择“Azure 资源”。
1. 对于分配的预测资源，选择“更改查询参数”。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示“更改查询参数”链接。](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. 切换“保存日志”，然后选择“完成”进行保存。

    > [!div class="mx-imgBorder"]
    > ![使用 LUIS 门户保存日志，这是主动学习所必需的。](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     此操作通过添加 `log=true` 查询字符串参数来更改示例 URL。 对运行时终结点进行预测查询时，请复制并使用更改后的示例查询 URL。

## <a name="correct-predictions-to-align-utterances"></a>修正预测以使言语一致

每个言语的“预测意向”列中都显示一个建议意向，并在虚线边界框中显示建议实体。

> [!div class="mx-imgBorder"]
> [![查看 LUIS 无法确定的终结点言语](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

如果同意预测的意向和实体，请选择言语旁边的复选标记。 如果复选标记处于禁用状态，说明没有内容需要确认。  
如果不同意建议的意向，请从“预测意向”下拉列表中选择合适的意向。 如果不同意建议的实体，请开始标记它们。
完成后，选择言语旁的复选标记来确认标记。 选择“保存言语”将其从评审列表中移出，并将其添加到相应的意向中。

> [!TIP]
> 若要查看并纠正来自“评审终结点言语”列表中的所有示例言语的实体预测，请务必转到意向详细信息页。

## <a name="delete-utterance"></a>删除陈述

可删除评审列表中的每个陈述。 删除后，该陈述将不再出现在列表中。 即使用户从终结点中输入同一陈述，结果也是如此。

如果不确定是否应删除言语，请将其移至 None 意向，或创建新的意向（例如 `miscellaneous`），并将言语移至该意向。

## <a name="disable-active-learning"></a>禁用主动学习

若要禁用主动学习，请不要记录用户查询。 这是通过更改如上所示的查询参数或通过在终结点查询中设置 `log=false` 参数来实现的，或者可以省略 `log` 参数，因为默认值为 `false`。


## <a name="next-steps"></a>后续步骤

若要测试标记建议陈述后的性能提升情况，可通过选择顶部面板中的“测试”访问测试控制台。 有关如何使用测试控制台测试应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。
