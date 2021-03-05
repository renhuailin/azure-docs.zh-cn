---
title: 将活动学习与知识库结合使用-QnA Maker
description: 了解如何通过主动学习提高知识库的质量。 查看、接受或拒绝，添加时不删除或更改现有问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 87dde7662050794a24cf976a0bae6237b91d29b2
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213702"
---
# <a name="active-learning"></a>主动学习

利用 _活动的学习建议_ 功能，你可以通过基于用户提交的问题和答案对来建议替代问题，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。

知识库不会自动更改。 要使更改生效，必须接受建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="what-is-active-learning"></a>什么是活动学习？

QnA Maker 通过隐式和显式反馈学习新的问题变体。

* [隐式反馈](#how-qna-makers-implicit-feedback-works) –当用户问题有多个答案，其中包含非常接近的分数并将此视为反馈时，ranker 可以理解。 无需执行任何操作即可执行此操作。
* [明确反馈](#how-you-give-explicit-feedback-with-the-train-api) –当从知识库返回分数变小的多个答案时，客户端应用程序会询问用户哪个问题是正确的问题。 将用户的显式反馈发送到带有 [训练 API](../How-To/improve-knowledge-base.md#train-api)QnA Maker。

这两种方法都为 ranker 提供了聚集的类似查询。

## <a name="how-active-learning-works"></a>主动学习的工作原理

活动学习是根据 QnA Maker 返回的前几个答案的分数来触发的。 如果与查询匹配的 QnA 对之间的分数差异位于一个较小的范围内，则查询将被视为可能的建议 (作为) 每个可能的 QnA 对的替代问题。 接受特定 QnA 对的建议问题后，其他对将拒绝该问题。 在接受建议后，需要记住保存和训练。

在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。 如果有5个或更多个类似查询被聚集，每30分钟 QnA Maker 建议向知识库设计器提供基于用户的问题，以接受或拒绝。 所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。

在 QnA Maker 门户中建议问题后，需要查看并接受或拒绝这些建议。 没有用于管理建议的 API。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 的隐式反馈如何工作

QnA Maker 的隐式反馈使用算法来确定接近的分数，并做出积极的学习建议。 用于确定置信度的算法并不是一个简单的计算。 以下示例中的范围并不是固定的，而应作为指导来了解算法的影响。

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。

在以下从查询到 QnA Maker generateAnswer 的 JSON 响应中，A、B 和 C 的分数接近，将被视为建议。

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

QnA Maker 不知道哪个答案是最佳答案。 使用 QnA Maker 门户的建议列表选择最佳回答并再次训练。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何向训练 API 提供显式反馈

QnA Maker 需要有关哪种答案是最佳答案的明确反馈。 如何确定最好的答案取决于你，并可包括：

* 用户反馈，选择其中一个答案。
* 业务逻辑，例如确定可接受的分数范围。
* 用户反馈和业务逻辑的组合。

使用 [训练 API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train) 将正确答案发送到 QnA Maker，并在用户选择它后发送。

## <a name="upgrade-runtime-version-to-use-active-learning"></a>升级运行时版本以使用活动学习

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)以使用此功能。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

在 QnA Maker 托管 (预览版) ，因为运行时是由 QnA Maker 服务本身承载的，所以无需手动升级运行时。

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>启用其他问题的活动学习

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 开启活动学习后，需要将客户端应用程序中的信息发送到 QnA Maker。 有关详细信息，请参阅 [使用 GenerateAnswer 的体系结构流和从机器人训练 api](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 选择 " **发布** " 以发布知识库。 仅从 GenerateAnswer API 预测终结点收集活动学习查询。 QnA Maker 门户中的 "测试" 窗格查询不会影响主动学习。

1. 若要在 QnA Maker 门户中打开活动学习，请转到右上角，选择你的 **名称**，转到 " [**服务设置**](https://www.qnamaker.ai/UserSettings)"。

    ![从 "服务设置" 页中打开活动学习的建议问题替代方法。 在右上方菜单中选择您的用户名，然后选择 "服务设置"。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”。

    > [!div class="mx-imgBorder"]
    > [![在 "服务设置" 页上，切换活动学习功能。如果无法切换该功能，则可能需要升级您的服务。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上图中的确切版本仅显示为示例。 你的版本可能不同。

    启用 **活动学习** 后，该知识库将基于用户提交的问题，定期提出新问题。 可以通过再次切换设置来禁用“主动学习”。
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

默认情况下，活动学习在 QnA Maker 托管 (预览版) **上打开** 。 若要查看建议的替代问题，请使用 "编辑" 页上的 " [查看选项](../How-To/improve-knowledge-base.md#view-suggested-questions) "。

---

## <a name="review-suggested-alternate-questions"></a>查看建议的替代问题

查看每个知识库的 "**编辑**" 页上的 [替代建议问题](improve-knowledge-base.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)
