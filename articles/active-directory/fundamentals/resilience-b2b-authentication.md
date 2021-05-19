---
title: 使用 Azure Active Directory 在外部用户身份验证中构建复原能力
description: 用于为外部用户构建可复原身份验证的面向 IT 管理员和架构师的指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 487efce1fe57413dda740c42a7fd3d5ea91cfa49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724953"
---
# <a name="build-resilience-in-external-user-authentication"></a>在外部用户身份验证中构建复原能力

[Azure Active Directory B2B 协作](../external-identities/what-is-b2b.md) (Azure AD B2B) 是[外部标识](../external-identities/delegate-invitations.md)的一项功能，可实现与其他组织和个人协作。 该功能可让来宾用户安全加入到 Azure AD 租户中，而无需管理其凭据。 外部用户使用其外部标识提供者 (IdP) 提供的标识和凭据，因此不必记住新凭据。 

## <a name="ways-to-authenticate-external-users"></a>对外部用户进行身份验证的方法

可以选择外部用户对目录进行身份验证的方法。 可以使用 Microsoft IdP 或其他 IdP。

对于每个外部 IdP，你都依赖于该 IdP 的可用性。 通过一些连接到 IdP 的方法，可以执行一些操作来提升复原能力。

> [!NOTE] 
> Azure AD B2B 具有的内置功能可对任何 [Azure Active Directory](../index.yml) 租户的任何用户或使用个人 [Microsoft 帐户](https://account.microsoft.com/account)的任何用户进行身份验证。 不必使用这些内置选项进行任何配置。

### <a name="considerations-for-resilience-with-other-idps"></a>使用其他 IdP 实现复原能力的注意事项

使用外部 IdP 进行来宾用户身份验证时，必须确保维护某些配置以防止中断。

| 身份验证方法| 复原能力注意事项 |
| - | - |
| [Facebook](../external-identities/facebook-federation.md) 或 [Google](../external-identities/google-federation.md) 等社交 IDP 的联合身份验证。| 必须通过 IdP 维护帐户，并配置客户端 ID 和客户端机密。 |
| [SAML 和 WS 联合身份验证标识提供者的直接联合身份验证](../external-identities/direct-federation.md)| 必须与 IdP 所有者协作，以便访问其终结点（你依赖于这些终结点）。 <br>必须维护包含证书和终结点的元数据。 |
| [电子邮件一次性密码](../external-identities/one-time-passcode.md)| 对于此方法，你依赖于 Microsoft 的电子邮件系统、用户的电子邮件系统和用户的电子邮件客户端。 |


 

## <a name="self-service-sign-up-preview"></a>自助注册（预览版）

作为发送邀请或链接的替代方法，可以启用[自助注册](../external-identities/self-service-sign-up-overview.md)。  这使外部用户可请求访问应用程序。 必须创建 [API 连接器](../external-identities/self-service-sign-up-add-api-connector.md)与用户流关联。 可将定义用户体验的用户流与一款或多款应用程序相关联。 

还可使用 [API 连接器](../external-identities/api-connectors-overview.md)将自助注册用户流与外部系统的 API 集成。 此 API 集成可用于[自定义审批工作流](../external-identities/self-service-sign-up-add-approvals.md)、[执行身份验证](../external-identities/code-samples-self-service-sign-up.md)和其他任务，如覆盖用户属性。 使用 API 需要管理以下依赖项。

* **API 连接器身份验证**：设置连接器需要终结点 URL、用户名和密码。 设置一个过程来维护这些凭据，并与 API 所有者合作，确保你了解任何过期日程安排。

* **API 连接器响应**：在注册流中将 API 连接器设计为在 API 不可用时正常失败。 检查这些 [API 响应示例](../external-identities/self-service-sign-up-add-api-connector.md)和[故障排除最佳做法](../external-identities/self-service-sign-up-add-api-connector.md)并提供给 API 开发人员。 与 API 开发团队合作，测试所有可能的响应场景，包括延续、验证错误和阻止响应。 

## <a name="next-steps"></a>后续步骤
面向管理员和架构师的复原能力资源
 
* [利用凭据管理构建复原能力](resilience-in-credentials.md)

* [使用设备状态构建复原能力](resilience-with-device-states.md)

* [使用连续访问评估 (CAE) 构建复原能力](resilience-with-continuous-access-evaluation.md)

* [在混合身份验证中构建复原能力](resilience-in-hybrid.md)

* [利用应用程序代理构建应用程序访问的复原能力](resilience-on-premises-access.md)

适用于开发人员的复原能力资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
