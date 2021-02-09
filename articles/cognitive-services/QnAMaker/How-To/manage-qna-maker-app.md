---
title: 管理 QnA Maker 应用-QnA Maker
description: 通过 QnA Maker，多名人员可针对知识库展开协作。 QnA Maker 提供了一种通过活动学习提高知识库质量的功能。 可以查看、接受或拒绝，还可以添加，而无需删除或更改现有问题。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 652489e8de68d61707fd419843f0dbb2ffd83754
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987943"
---
# <a name="manage-qna-maker-app"></a>管理 QnA Maker 应用

QnA Maker 允许你通过提供一种基于协作者角色限制协作者访问的功能，使你能够与不同的作者和内容编辑器进行协作。
详细了解 [QnA Maker 协作者身份验证概念](../Concepts/role-based-access-control.md)。

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./manage-knowledge-bases.md)
