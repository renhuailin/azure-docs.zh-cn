---
title: 与他人协作-QnA Maker
description: 了解如何使用 Azure 基于角色的访问控制与其他作者和编辑人员协作。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 1d434697802596a0200dc0cb5f1afe5bc67afef5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379703"
---
# <a name="collaborate-with-other-authors-and-editors"></a>与其他作者和编辑人员协作

使用 Azure 基于角色的访问控制来与其他作者和编辑人员进行协作 (Azure RBAC) 置于 QnA Maker 资源。

## <a name="access-is-provided-on-the-qna-maker-resource"></a>QnA Maker 资源上提供了访问权限

所有权限都受 QnA Maker 资源上的权限控制。 这些权限与读取、写入、发布和完全访问权限一致。

此 Azure RBAC 功能包括：
* Azure Active Directory (AAD) 为100% 向后兼容所有者和参与者的基于密钥的身份验证。 客户可在其请求中使用基于密钥的身份验证或基于密钥的 Azure 身份验证。
* 快速将作者和编辑器添加到资源中的所有知识库，因为控件位于资源级别，而不是在知识库级别。

> [!NOTE]
> Ar 确保为资源添加自定义子域。 默认情况下应存在[自定义子域](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)，但如果不存在，请添加它

## <a name="access-is-provided-by-a-defined-role"></a>访问由定义的角色提供

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>身份验证流

下图显示了从作者的角度来登录到 QnA Maker 门户并使用创作 Api 的流。

> [!div class="mx-imgBorder"]
> ![下图显示了从作者的角度来登录到 QnA Maker 门户并使用创作 Api 的流。](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|步骤|说明|
|--|--|
|1|门户获取 QnA Maker 资源的标记。|
|2|门户会调用适当的 QnA Maker 创作 API (APIM) 传递令牌，而不是密钥。|
|3|QnA Maker API 验证令牌。|
|4 |QnA Maker API 调用 QnAMaker 服务。|

如果要调用 [创作 api](../index.yml)，请详细了解如何设置身份验证。

## <a name="authenticate-by-qna-maker-portal"></a>QnA Maker 门户进行身份验证

如果使用 QnA Maker 门户创建和协作，则在将适当的角色添加到协作者的资源之后，QnA Maker 门户将管理所有访问权限。

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>QnA Maker Api 和 Sdk 进行身份验证

如果通过 REST 或 Sdk 使用 Api 创作和协作，则需要 [创建服务主体](../../authentication.md#assign-a-role-to-a-service-principal) 来管理身份验证。

## <a name="next-step"></a>后续步骤

* 为语言和客户端应用程序设计知识库
