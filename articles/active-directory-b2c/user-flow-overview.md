---
title: Azure Active Directory B2C 中的用户流和自定义策略 | Microsoft Docs
titleSuffix: Azure AD B2C
description: 详细了解 Azure Active Directory B2C 的内置用户流和自定义策略可扩展策略框架。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b663904df2d116bdfa92a0dcef4b05c0806fec14
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112199135"
---
# <a name="user-flows-and-custom-policies-overview"></a>用户流和自定义策略概述

在 Azure AD B2C 中，你可以定义用户在获取应用程序访问权限时遵循的业务逻辑。 例如，可以确定用户在登录、注册、编辑个人资料或重置密码时遵循的步骤顺序。 按顺序完成步骤后，用户会获得一个令牌，并获得对应用程序的访问权限。 

在 Azure AD B2C 中，有两种方式可以提供标识用户体验：

* **用户流** 是我们提供的预定义的内置可配置策略，使你能够在几分钟内创建注册、登录和策略编辑体验。

* 使用 **自定义策略** 可为复杂的标识体验方案创建自己的用户旅程。

以下屏幕截图显示用户流设置 UI，而不是自定义策略配置文件。

![屏幕截图显示用户流设置 UI，而不是自定义策略配置文件。](media/user-flow-overview/user-flow-vs-custom-policy.png)

本文简要概述了用户流和自定义策略，并帮助你确定哪种方法最适合你的业务需求。

## <a name="user-flows"></a>用户流

为了设置最常见的标识任务，Azure 门户中包含了多个称作“用户流”的预定义可配置策略。

可以配置如下所述的用户流设置，以控制应用程序中的标识体验行为：

* 用于登录的帐户类型，例如 Facebook 等社交帐户，或者使用电子邮件地址和密码进行登录的本地帐户
* 要从使用者收集的属性，例如名字、邮政编码，或居住地所在国家/地区
* Azure AD 多重身份验证 (MFA)
* 用户界面的自定义
* 在用户完成用户流后由应用程序接收的令牌中的声明集
* 会话管理
* 等等

应用的大多数常见标识方案都可以通过用户流来有效定义和实施。 除非你的复杂用户旅程方案要求自定义策略具有完全灵活性，否则我们建议使用内置用户流。

## <a name="custom-policies"></a>自定义策略

自定义策略是定义 Azure AD B2C 租户用户体验行为的配置文件。 用户流是在 Azure AD B2C 门户中为最常见标识任务预定义的，而标识开发人员可以全面编辑自定义策略，以完成许多不同的任务。

自定义策略是完全可配置的，它由策略驱动。 它可以协调标准协议中实体之间的信任。 例如，OpenID Connect、OAuth、SAML 和一些非标准协议，如基于 REST API 的系统间声明交换。 该框架创建用户友好的白标体验。

自定义策略可让你使用步骤的任意组合来构造用户旅程。 例如：

* 与其他标识提供者联合
* 第一方和第三方多重身份验证 (MFA) 质询
* 收集任何用户输入
* 使用 REST API 通信来与外部系统集成

每个用户旅程都由策略定义。 你可以根据需要构建任意数量的策略，为组织提供最佳用户体验。

![显示 IEF 启用的复杂用户旅程示例的图示](media/user-flow-overview/custom-policy-diagram.png)

自定义策略由多个 XML 文件定义，这些文件在分层链中相互引用。 XML 元素定义声明架构、声明转换、内容定义、声明提供程序、技术配置文件、用户旅程业务流程步骤，以及标识体验的其他方面。

需要构建复杂的标识方案时，自定义策略的极高灵活性可以发挥最大的作用。 配置自定义策略的开发人员必须严谨地定义信任关系，以包含元数据终结点和确切的声明交换定义，并配置每个标识提供者所需的机密、密钥和证书。

在 [Azure Active Directory B2C 中的自定义策略](custom-policy-overview.md)中详细了解自定义策略。

## <a name="comparing-user-flows-and-custom-policies"></a>比较用户流和自定义策略

下表详细比较了使用 Azure AD B2C 用户流启用的方案和使用自定义策略启用的方案。

| 上下文 | 用户流 | 自定义策略 |
|-|-------------------|-----------------|
| 目标用户 | 具有或不具有标识专业知识的所有应用程序开发人员。 | 标识专业人员、系统集成人员、顾问和内部标识团队。 他们能够熟悉运作 OpenID Connect 流，并了解标识提供者和基于声明的身份验证。 |
| 配置方法 | 具有用户友好用户界面 (UI) 的 Azure 门户。 | 直接编辑 XML 文件，并上传到 Azure 门户。 |
| UI 自定义 | [完整的 UI 自定义](customize-ui-with-html.md)包括 HTML、CSS 和 [JavaScript](javascript-and-page-layout.md)。<br><br>使用自定义字符串实现[多语言支持](language-customization.md)。 | 相同 |
| 属性自定义 | 标准和自定义属性。 | 相同 |
| 令牌和会话管理 | [自定义令牌](configure-tokens.md)和[会话行为](session-behavior.md)。 | 相同 |
| 标识提供者 | [预定义本地](identity-provider-local.md)或[社交提供程序](add-identity-provider.md)，例如 Azure Active Directory 租户联合身份验证。 | 基于标准的 OIDC、OAUTH 和 SAML。  也可通过集成 REST API 进行身份验证。 |
| 标识任务 | 使用本地帐户或多个社交帐户[注册或登录](add-sign-up-and-sign-in-policy.md)。<br><br>[自助式密码重置](add-password-reset-policy.md)。<br><br>[个人资料编辑](add-profile-editing-policy.md)。<br><br>多重身份验证。<br><br>访问令牌流。 | 使用自定义标识提供者或自定义范围完成与用户流相同的任务。<br><br>注册时在另一系统中预配用户帐户。<br><br>使用自己的电子邮件服务提供程序发送欢迎电子邮件。<br><br>使用 Azure AD B2C 外部的用户存储。<br><br>使用 API 通过受信任的系统验证用户提供的信息。 |

## <a name="application-integration"></a>应用程序集成

可以在租户中创建多个不同类型的用户流或自定义策略，并根据需要在应用程序中使用它们。 用户流和自定义策略可在应用程序之间重复使用。 由于这种灵活性，只需对代码做出极少量的更改或根本不需要更改，即可定义和修改标识体验。 

当用户想要登录你的应用程序时，应用程序就会向用户流或自定义策略提供的终结点发起授权请求。 用户流或自定义策略定义并控制用户的体验。 用户完成用户流后，Azure AD B2C 会生成一个令牌，然后将用户重定向回应用程序。

![移动应用，其中的箭头显示 Azure AD B2C 登录页之间的流](media/user-flow-overview/app-integration.png)

多个应用程序可以使用同一个用户流或自定义策略。 单个应用程序可以使用多个用户流或自定义策略。

例如，若要登录到某个应用程序，该应用程序将使用注册或登录用户流。 用户登录后，可能想编辑他们的个人资料。 为了编辑个人资料，应用程序会发起另一个授权请求，这次使用“个人资料编辑”用户流。

应用程序使用包含用户流或自定义策略名称的标准 HTTP 身份验证请求来触发用户流。 接收自定义[令牌](tokens-overview.md)作为响应。


## <a name="next-steps"></a>后续步骤

- 若要创建建议的用户流，请遵照[教程：创建用户流](tutorial-create-user-flows.md)中的说明。
- 了解 [Azure AD B2C 中的用户流版本](user-flow-versions.md)。
- 详细了解 [Azure AD B2C 自定义策略](custom-policy-overview.md)。
