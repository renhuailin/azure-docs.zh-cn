---
title: 管理 QnA Maker 应用-QnA Maker
description: 通过 QnA Maker，多名人员可针对知识库展开协作。 QnA Maker 提供了一种通过活动学习提高知识库质量的功能。 可以查看、接受或拒绝，还可以添加，而无需删除或更改现有问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 9c042d044f5ceba5a64d6bd7dfefa34bbc69b107
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353198"
---
# <a name="manage-qna-maker-app"></a>管理 QnA Maker 应用

QnA Maker 允许你通过提供一种基于协作者角色限制协作者访问的功能，使你能够与不同的作者和内容编辑器进行协作。
详细了解 [QnA Maker 协作者身份验证概念](../Concepts/role-based-access-control.md)。

还可以通过 [活动学习](../Concepts/active-learning-suggestions.md)建议替代问题，从而提高知识库的质量。 将考虑用户提交，并在替代问题列表中显示为建议。 您可以灵活地将这些建议添加为替代问题或拒绝它们。

知识库不会自动更改。 要使更改生效，必须接受建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="add-azure-role-based-access-control-azure-rbac"></a> (Azure RBAC) 中添加 Azure 基于角色的访问控制

QnA Maker 允许多人在同一 QnA Maker 资源中协作处理所有知识库。 此功能与 [AZURE RBAC)  (azure 基于角色的访问控制 ](../../../role-based-access-control/role-assignments-portal.md)一起提供。

## <a name="access-at-the-qna-maker-resource-level"></a>QnA Maker 资源级别的访问权限

不能在 QnA Maker 服务中共享特定的知识库。 如果需要更精细的访问控制，请考虑在不同的 QnA Maker 资源之间分布知识库，并将角色添加到每个资源。

## <a name="add-a-role-to-a-resource"></a>将角色添加到资源

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>将用户帐户添加到 QnA Maker 资源

以下步骤使用 "协作者" 角色，但可以使用以下步骤添加任何[角色](../reference-role-based-access-control.md)

1. 登录到 [Azure](https://portal.azure.com/) 门户，并中转到 QnA Maker 资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. 转到“访问控制 (IAM)”选项卡。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. 选择 **添加** 。

    ![QnA Maker IAM 添加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 从以下列表中选择一个角色：

    |角色|
    |--|
    |所有者|
    |参与者|
    |认知服务 QnA Maker 读者|
    |认知服务 QnA Maker 编辑器|
    |认知服务用户|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM 添加角色。":::

1. 输入用户的电子邮件地址，并按 " **保存**"。

    ![QnA Maker IAM 添加电子邮件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>查看 QnA Maker 知识库

当你将 QnA Maker 服务的用户共享到 [QnA Maker 门户](https://qnamaker.ai)时，他们可以根据其角色查看该服务中的所有知识库。

当他们选择某一知识库时，该知识库中的 QnA Maker 资源上的当前角色就可见了。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="在编辑模式下的知识库中的屏幕截图，其角色名称位于网页左上角的 &quot;知识库名称&quot; 旁边的括号中。":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>升级运行时版本以使用活动学习

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用此功能。

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