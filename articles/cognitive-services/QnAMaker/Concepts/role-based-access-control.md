---
title: 与他人协作 - QnA Maker
description: 了解如何使用 Azure 基于角色的访问控制与其他作者和编辑人员协作。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: 512ae470d3c9068b9d284095a18013fc84dbf36e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232539"
---
# <a name="collaborate-with-other-authors-and-editors"></a>与其他作者和编辑人员协作

使用置于 QnA Maker 资源上的 Azure 基于角色的访问控制 (Azure RBAC) 与其他作者和编辑人员协作。

## <a name="access-is-provided-on-the-qna-maker-resource"></a>提供了对 QnA Maker 资源的访问权限

所有权限均由置于 QnA Maker 资源上的权限控制。 这些权限与读取、写入、发布和完全访问权限一致。 可以通过[更新对 QnA Maker 资源的 RBAC](../how-to/manage-qna-maker-app.md) 访问权限来支持多个用户间的协作。

此 Azure RBAC 功能包括：
* Azure Active Directory (AAD) 100% 向后兼容对所有者和参与者的基于密钥的身份验证。 客户可在其请求中使用基于密钥的身份验证或基于 RBAC 的 Azure 身份验证。
* 快速将作者和编辑人员添加到资源中的所有知识库，因为控件位于资源级别，而不是在知识库级别。

> [!NOTE]
> 请确保为资源添加自定义子域。 默认情况下，应存在[自定义子域](../../cognitive-services-custom-subdomains.md)，如果不存在，请添加

## <a name="access-is-provided-by-a-defined-role"></a>访问权限由定义的角色提供

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>身份验证流

下图显示了从作者角度出发，登录 QnA Maker 门户和使用创作 API 的流。

> [!div class="mx-imgBorder"]
> ![下图显示了从作者角度出发，登录 QnA Maker 门户和使用创作 API 的流。](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|步骤|说明|
|--|--|
|1|门户需要获取 QnA Maker 资源的令牌。|
|2|门户会调用适当的 QnA Maker 创作 API (APIM) 传递令牌而不是密钥。|
|3|QnA Maker API 对令牌进行验证。|
|4 |QnA Maker API 调用 QnAMaker 服务。|

如果打算调用[创作 API](../index.yml)，请了解有关如何设置身份验证的更多信息。

## <a name="authenticate-by-qna-maker-portal"></a>由 QnA Maker 门户进行身份验证

如果使用 QnA Maker 门户开展创作和协作，则在向协作者的资源添加适当的角色后，QnA Maker 门户将管理所有访问权限。

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>由 QnA Maker API 和 SDK 进行身份验证

如果通过 REST 或 SDK 使用 API 进行创作和协作，则需要[创建服务主体](../../authentication.md#assign-a-role-to-a-service-principal)来管理身份验证。

## <a name="next-step"></a>后续步骤

* 为语言和客户端应用程序设计知识库
