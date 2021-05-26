---
title: 教程：使用 Azure 机器人服务创建 FAQ 机器人
description: 在本教程中，你将使用 QnA Maker 和 Azure 机器人服务创建一个无代码 FAQ 机器人。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: f2dffe98f401acad927cc3404ffd2c48378e9026
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110372215"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>教程：使用 Azure 机器人服务创建 FAQ 机器人
使用 QnA Maker 和 Azure [机器人服务](https://azure.microsoft.com/services/bot-service/)，在不编写代码的情况下创建 FAQ 机器人。

在本教程中，你将了解如何执行以下操作：

<!-- green checkmark -->
> [!div class="checklist"]
> * 将 QnA Maker 知识库链接到 Azure 机器人服务
> * 部署机器人
> * 通过网上聊天与机器人聊天
> * 在支持的通道中点亮机器人

## <a name="create-and-publish-a-knowledge-base"></a>创建并发布知识库

遵循[快速入门](../Quickstarts/create-publish-knowledge-base.md)创建一个知识库。 知识库成功发布后，你将到达下面的页面。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

> [!div class="mx-imgBorder"]
> ![成功发布的屏幕截图](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

> [!div class="mx-imgBorder"]
> ![成功发布管理的屏幕截图](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint-managed.png)

---

## <a name="create-a-bot"></a>创建机器人

发布后，可以从“发布”页创建机器人：

* 可以快速创建多个机器人，它们全部可以指向不同区域中的同一个知识库，或者指向单个机器人的定价计划。
* 如果只想为知识库创建一个机器人，请使用“在 Azure 门户中查看所有机器人”链接查看当前机器人的列表。

对知识库进行更改并重新发布时，不需要对机器人采取进一步的操作。 机器人已配置为使用该知识库，并会处理将来对该知识库所做的所有更改。 每当发布某个知识库时，连接到该知识库的所有机器人都会自动更新。

1. 在 QnA Maker 门户中的“发布”页上，选择“创建机器人”。 只有在发布知识库后，才会显示此按钮。

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

    > [!div class="mx-imgBorder"]
    > ![创建机器人的屏幕截图](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

    # <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

    > [!div class="mx-imgBorder"]
    > ![创建机器人管理预览版的屏幕截图](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page-managed.png)

    ---
    

1. 此时会打开 Azure 门户的新浏览器标签页，其中显示了 Azure 机器人服务的创建页。 配置 Azure 机器人服务。 机器人和 QnA Maker 可以共享 Web 应用服务计划，但不能共享 Web 应用。 这意味着，机器人的 **应用名称** 必须不同于 QnA Maker 服务的应用名称。

    * **建议做法**
        * 更改机器人句柄 - 如果它不独一无二。
        * 选择 SDK 语言。 创建机器人后，可以将代码下载到本地开发环境，然后继续开发过程。
    * **不要**
        * 创建机器人时，更改 Azure 门户中的以下设置。 这些值是为现有的知识库预先填充的：
           * QnA 身份验证密钥
           * 应用服务计划和位置


1. 创建机器人后，打开“机器人服务”资源。
1. 在“机器人管理”下，选择“通过网上聊天执行测试”。
1. 出现聊天提示“键入你的消息”时，输入：

    `Azure services?`

    聊天机器人使用知识库中的一个答案进行响应。

    > [!div class="mx-imgBorder"]
    > ![机器人返回响应的屏幕截图](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)


1. 在其他[支持的通道](/azure/bot-service/bot-service-manage-channels)中点亮机器人。


    - 单击机器人服务资源中的“通道”。

    > [!div class="mx-imgBorder"]
    > ![将机器人连接到通道的屏幕截图](../media/qnamaker-tutorial-updates/connect-with-teams.png)
    
## <a name="integrate-the-bot-with-channels"></a>将机器人与通道集成

在已创建的机器人服务资源中单击“通道”。 可在其他[支持的通道](/azure/bot-service/bot-service-manage-channels)中启动机器人。

   >[!div class="mx-imgBorder"]
   >![与 Teams 集成的屏幕截图](../media/qnamaker-tutorial-updates/connect-with-teams.png)

