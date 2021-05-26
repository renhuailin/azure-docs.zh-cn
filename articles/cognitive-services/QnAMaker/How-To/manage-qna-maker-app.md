---
title: 管理 QnA Maker 应用 - QnA Maker
description: 通过 QnA Maker，多名人员可针对知识库展开协作。 QnA Maker 提供了通过主动学习来提高知识库质量的功能。 用户可以在不删除或更改现有问题的情况下查看、接受或拒绝以及添加问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: fc5de9ff694c1c5a3841c206334fb87d240a568c
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378764"
---
# <a name="manage-qna-maker-app"></a>管理 QnA Maker 应用

QnA Maker 通过提供一个基于协作者角色限制协作者访问权限的功能，让你可以与不同的创建者和内容编辑者进行协作。
详细了解 [QnA Maker 协作者身份验证概念](../Concepts/role-based-access-control.md)。

## <a name="add-azure-role-based-access-control-azure-rbac"></a>添加 Azure 基于角色的访问控制 (Azure RBAC)

QnA Maker 允许多人在同一 QnA Maker 资源中的所有知识库上进行协作。 此功能通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../../role-based-access-control/role-assignments-portal.md) 提供。

## <a name="access-at-the-cognitive-resource-level"></a>在认知资源级别进行访问

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

你不能在 QnA Maker 服务中共享特定知识库。 如果想要更精细的访问控制，请考虑将你的知识库分布在不同的 QnA Maker 资源上，然后为每个资源添加角色。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

你不能在文本分析服务中共享特定知识库。 如果想要更精细的访问控制，请考虑将你的知识库分布在不同的文本分析资源上，然后为每个资源添加角色。

---

## <a name="add-a-role-to-a-resource"></a>向资源添加角色

### <a name="add-a-user-account-to-the-cognitive-resource"></a>向认知资源添加用户帐户

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

应对 QnA Maker 资源应用 RBAC 控制。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应该将 RBAC 控制应用于将自定义问答作为一项功能的文本分析资源。

---

以下步骤使用协作者角色，但你可以使用这些步骤添加任何角色

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后转到你的认知资源。

    ![QnA Maker 资源列表](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. 转到“访问控制 (IAM)”选项卡。

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. 选择 **添加** 。

    ![QnA Maker IAM 添加](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. 请从下面的列表中选择一个角色：

    |角色|
    |--|
    |“所有者”|
    |参与者|
    |认知服务 QnA Maker 读取者|
    |认知服务 QnA Maker 编辑者|
    |认知服务用户|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM - 添加角色。":::

1. 输入用户的电子邮件地址，然后按“保存”。

    ![QnA Maker IAM 添加电子邮件](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>查看 QnA Maker 知识库

当你与之共享 QnA Maker 服务的人员登录到 [QnA Maker 门户](https://qnamaker.ai)时，他们可以根据自己的角色查看该服务中的所有知识库。

当他们选择一个知识库时，他们在该 QnA Maker 资源上的当前角色会显示在知识库名称旁边。

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="“编辑”模式下知识库的屏幕截图，其中带括号的角色名称显示在网页左上角的知识库名称旁边。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)
