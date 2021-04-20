---
title: 使用主动学习改进知识库 - QnA Maker
description: 了解如何使用主动学习改进知识库的质量。 在不删除或更改现有问题的情况下检查、接受或拒绝以及添加问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102213702"
---
# <a name="active-learning"></a>主动学习

通过“主动学习建议”功能，可以根据用户提交的内容为问答对提供替代问题建议，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。

知识库不会自动更改。 要使更改生效，必须先接受建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="what-is-active-learning"></a>什么是主动学习？

QnA Maker 通过隐式和显式反馈学习新的问题变体。

* [隐式反馈](#how-qna-makers-implicit-feedback-works) - 排名程序了解何时用户问题具有多个分数非常接近的答案，并将其视为反馈。 无需对此规则执行任何操作。
* [显式反馈](#how-you-give-explicit-feedback-with-the-train-api) - 从知识库返回分数差异很小的多个答案时，客户端应用程序会询问用户哪个是正确问题。 使用[训练 API](../How-To/improve-knowledge-base.md#train-api) 将用户的显式反馈发送给 QnA Maker。

这两种方法都会为排名程序提供聚集的类似查询。

## <a name="how-active-learning-works"></a>主动学习的工作原理

主动学习根据 QnA Maker 返回的前几个答案的评分触发。 如果与查询匹配的 QnA 对之间的分数差异位于一个较小范围内，则将该查询视为每个可能的 QnA 对的可能建议（作为替代问题）。 接受关于特定 QnA 对的建议问题后，其他对将拒绝该问题。 接受建议后，切记需要保存和训练。

在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。 聚集 5 个或更多类似的查询后，QnA Maker 每隔 30 分钟便会向知识库设计器建议接受或拒绝基于用户的问题。 所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。

在 QnA Maker 门户中显示建议的问题后，需要审阅并接受或拒绝这些建议。 没有用于管理建议的 API。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 隐式反馈的工作原理

QnA Maker 隐式反馈使用算法确定分数置信度，并给出主动学习建议。 用于确定置信度的算法并不是一个简单的计算。 以下示例中的范围并不是固定的，仅用作了解算法影响的指南。

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。

在以下查询对 QnA Maker generateAnswer 的 JSON 响应中，A、B 和 C 的分数接近，将被视为建议。

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker 不知道哪个答案是最佳答案。 使用 QnA Maker 门户的建议列表选择最佳答案，并重新训练。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用训练 API 提供显式反馈

QnA Maker 需要有关哪个答案是最佳答案的显式反馈。 如何确定最佳答案取决于你，还可能包括：

* 用户反馈，选择其中一个答案。
* 业务逻辑，例如确定可接受的分数范围。
* 用户反馈和业务逻辑的组合。

在用户做出选择后，使用[训练 API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) 将正确答案发送到 QnA Maker。

## <a name="upgrade-runtime-version-to-use-active-learning"></a>升级运行时版本以使用主动学习

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)以使用此功能。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

在 QnA Maker 托管（预览版本）中，由于运行时由 QnA Maker 服务本身承载，所以无需手动升级运行时。

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>对替代问题启用主动学习

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 启用主动学习后，需要将来自客户端应用的信息发送到 QnA Maker。 有关详细信息，请参阅[使用机器人中的 GenerateAnswer 和训练 API 的体系结构流程](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 选择“发布”可发布知识库。 主动学习查询只能从 GenerateAnswer API 预测终结点收集。 QnA Maker 门户中“测试”窗格的查询不会影响主动学习。

1. 要在 QnA Maker 门户中启用主动学习，请转到右上角，选择“名称”，然后转到“服务设置”[](https://www.qnamaker.ai/UserSettings)。

    ![在“服务设置”页中打开主动学习的建议问题替代。 在右上方菜单中选择你的用户名，然后选择“服务设置”。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”。

    > [!div class="mx-imgBorder"]
    > [![在“服务设置”页上，启用“主动学习”功能。如果无法切换该功能，则可能需要升级服务。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上图中显示的确切版本仅用作示例。 你的版本可能与之不同。

    启用“主动学习”后，知识库会根据用户提交的问题定期建议新的问题。 可以通过再次切换设置来禁用“主动学习”。
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

默认情况下，QnA Maker 托管（预览版本）中的主动学习处于打开状态。 要查看建议的替代问题，请使用“编辑”页上的[“查看”选项](../How-To/improve-knowledge-base.md#view-suggested-questions)。

---

## <a name="review-suggested-alternate-questions"></a>查看建议的替代问题

在每个知识库的“编辑”页上[查看建议的替代问题](improve-knowledge-base.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)
