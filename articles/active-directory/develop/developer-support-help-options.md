---
title: Azure AD 应用开发人员的支持和帮助选项
description: 了解在创建与 Microsoft 标识（Azure Active Directory 和 Microsoft 帐户）集成的应用程序时，如何获取所遇到的开发相关问题的帮助和支持
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: c72a305a7000052be4f11c9c24a76db94de47ace
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650101"
---
# <a name="support-and-help-options-for-developers"></a>开发人员的支持和帮助选项

如果是刚刚开始与 Azure Active Directory (Azure AD)、Microsoft 标识或 Microsoft 图形 API 集成，或者是正在应用程序中实现新功能，有时需要从社区获得帮助，或了解开发人员可用的支持选项。 可以通过本文了解这些选项，其中包括：

> [!div class="checklist"]
> * 如何通过搜索来确定是问题尚未由社区做出解答，还是你尝试实现的功能已存在现有文档
> * 在某些情况下，我们只想使用自己的支持工具来帮助调试特定的问题
> * 如果你找不到所需的答案，你可能想要提问 *&*
> * 如果发现某个身份验证库出现问题，请提出 *GitHub* 问题
> * 最后，如果需要与某人交流，可以提出支持请求

## <a name="search"></a>搜索

如果你有与开发相关的问题，则可以在文档、 [GitHub 示例](https://github.com/azure-samples)或 [Microsoft Q 答案&](/answers/products/) 问题中找到答案。

### <a name="scoped-search"></a>限定范围的搜索

为了更快地获得结果，请通过在常用的搜索引擎中使用以下查询，将搜索范围限定为 [Microsoft Q&](/answers/products/)文档和代码示例：

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

其中， *{Your Search Terms}* 对应于搜索关键字。

## <a name="use-the-development-support-tools"></a>使用开发支持工具

| 工具  | 说明  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | 粘贴 ID 或访问令牌来解码声明名称和值。 |
| [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)| 用于针对 Microsoft Graph API 发出请求和查看响应的工具。 |

## <a name="post-a-question-to-microsoft-qa"></a>将问题发布到 Microsoft Q&

[Microsoft Q&](/answers/products/) 是与开发相关的问题的首选渠道。 在这里，开发人员社区成员和 Microsoft 团队成员可直接助你解决问题。

如果通过搜索找不到问题的答案，请将新问题提交给 [Microsoft Q&](/answers/products/) 。 在提问时请使用以下标记之一，以便社区更快速地识别问题和解答问题：

|组件/区域  | Tags |
|---------|---------|
| ADAL 库 | [[adal]](/answers/topics/azure-ad-adal-deprecation.html) |
| MSAL 库     | [[msal]](/answers/topics/azure-ad-msal.html) |
| OWIN 中间件  | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](/answers/topics/azure-ad-b2b.html) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](/answers/topics/azure-ad-b2c.html) |
| [Microsoft 图形 API](https://developer.microsoft.com/graph/) | [[azure--graph]](/answers/topics/azure-ad-graph.html) |
| 与身份验证或授权主题相关的其他任何方面 | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |

下面的 [Microsoft Q&](/answers/products/) 文章包含有关如何提问和如何添加源代码的提示。 遵循以下准则可提高社区成员快速评估和响应问题的可能性：

* [如何正确提问](/answers/articles/24951/how-to-write-a-quality-question.html)
* [How to create a minimal, complete, and verifiable example](/answers/articles/24907/how-to-write-a-quality-answer.html)（如何创建最小、完整且可验证的示例）

## <a name="create-a-github-issue"></a>创建 GitHub 问题

如果发现了与库相关的 Bug 或问题，请在 GitHub 存储库中提出问题。 由于我们的库是开源的，因此也可提交拉取请求。

有关库及其 GitHub 存储库的列表，请参阅以下内容：

* [Azure Active Directory 身份验证库 (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) 库和 GitHub 存储库
* [Microsoft 身份验证库 (MSAL)](reference-v2-libraries.md) 库和 GitHub 存储库

## <a name="open-a-support-request"></a>提出支持请求

如果需要与某人交流，可以提出支持请求。 Azure 客户可以使用多个支持选项。 若要比较计划，请参阅[此页](https://azure.microsoft.com/support/plans/)。 开发人员支持也适用于 Azure 客户。 有关如何购买开发人员支持计划的信息，请参阅[此页](https://azure.microsoft.com/support/plans/developer/)。

* 如果已有 Azure 支持计划，请[在此处提出支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* 如果你不是 Azure 客户，也可以通过[我们的商业支持](https://support.serviceshub.microsoft.com/supportforbusiness)向 Microsoft 提出支持请求。

还可以尝试通过[虚拟代理](https://support.microsoft.com/contactus/?ws=support)获取支持或提出问题。